# Security and economic policy

## Trust boundary

Claim statements are immutable. URLs, fetched bytes, rendered HTML, submitter relationship labels, and model outputs are untrusted. The contract admits HTTPS hostnames without credentials, explicit ports, IP literals, malformed labels, or configured local-name suffixes. `PINNED_TEXT` independently fetches and hashes exact response bytes, then records lower-assurance text findings as `TEXT_ONLY`; it cannot produce `CONFIRMED`, `CONTRADICTED`, graph targets, or bounty payouts. `RENDERED_WEB` uses browser-rendered HTML for those higher-assurance outcomes. `PINNED_JSON` and `VISUAL` are unsupported. An unavailable fetch or pin mismatch is never `CONTRADICTED`.

Observed `content_hash`, `render_hash`, and canonical `evidence_id` are derived inside the independent leader/validator operation. The caller's expected text digest is only a pin assertion, and the caller's human-readable alias is not a provenance root. The validator independently re-fetches or re-renders and recomputes identity, then substantively reclassifies the claim and any targeted graph relation. Prompt instructions in source data are explicitly ignored. Unknown or malformed model output cannot confirm evidence.

This is semantic provenance, not a source-authenticity protocol. It does not prove who published a page, DNS resolution safety for every public hostname, or independence among publishers. One active support observation is enough for `CONFIRMED`; syndicated or related sources are not clustered. HTML text normalization strips tags/comments/script/style and collapses whitespace, but does not implement a full browser accessibility tree or a canonical DOM standard. The returned render hash binds the actual HTML string given by GenLayer's render API.

## Deterministic evidence graph and claim policy

Evidence is append-only and per-claim limited to 128 items. Graph edges are globally limited to 512 and per claim to 256. A graph edge can only target an older verified item. `SUPERSEDES` and `EXPIRES` deactivate that target; `RESTORES` reactivates it. Historical records remain queryable. Duplicate observations of an identical claim, URL, and artifact share a canonical evidence identity and cannot supersede themselves.

At read and settlement time, active non-stale findings are aggregated consistently. Fresh support and fresh contradiction yield `DISPUTED`; support-only yields `CONFIRMED`; contradiction-only yields `CONTRADICTED`. If decisive evidence exists only beyond seven days, the result is `STALE`; unavailable-only evidence is `UNAVAILABLE`; otherwise it is `INSUFFICIENT`. `FRESH` lasts 24 hours, `AGING` lasts through seven days. Evidence older than seven days does not decide current status.

The challenge entry point is deliberately narrow: it requires a verified support/contradiction conflict, a fixed reason code, and at most three challenge records per claim. It only records that a conflicting finding was challenged; it does not erase findings or independently resolve an appeal. A separate bonded challenge adjudication system is not implemented and must not be claimed.

## Escrow rules

`create_bounty` accepts only the GEN value attached to that payable call. Invalid input returns a typed `REJECTED:<reason>` outcome normally and issues a refund transfer request; it does not rely on revert rollback. Studionet acceptance of rejected-call refunds and EVM transfer delivery remain live gates until measured with sender and contract balances. For a confirmed claim, permissionless settlement pays the submitter of the first active, non-stale rendered supporting evidence item in append order; the settlement caller never becomes beneficiary. Contradicted, unavailable, stale, and text-only outcomes cannot pay a bounty. Disputed, insufficient, or text-only claims stay open for up to 30 days, after which anyone can trigger a sponsor refund. No automatic timer executes on-chain, so a caller must invoke settlement after the deadline.

Before transfer, the contract sets amount to zero, marks the bounty `PAID` or `REFUNDED`, records the recipient and winning evidence, and appends history. All GEN transfers go through `_send_gen`. Global and per-claim history limits reserve a terminal history slot for every open bounty. Repeated settlement fails because the state is no longer `OPEN`.

| Bounty condition | Who can trigger | Recipient | Terminal result |
| --- | --- | --- | --- |
| Active `CONFIRMED` claim | Anyone | First active supporting evidence submitter | `PAID` |
| `CONTRADICTED`, `UNAVAILABLE`, or `STALE` | Anyone | Sponsor | `REFUNDED` |
| `DISPUTED` or `INSUFFICIENT`, before 30 days | Anyone may retry; no transfer | None | Remains `OPEN` |
| `DISPUTED` or `INSUFFICIENT`, at/after 30 days | Anyone | Sponsor | `REFUNDED` |

The fixed 30-day policy has no custom per-bounty deadline or sponsor cancellation method. The only supported financial asset is native GEN.

## Bounded state

The contract caps claims at 256, evidence globally at 1,024, evidence per claim at 128, graph edges globally at 512 and per claim at 256, history globally at 8,192 and per claim at 256, and bounties at 256. Creating ordinary history is prevented from consuming slots reserved for open-bounty terminal settlement. These limits bound storage growth but large deployments should still measure worst-case view and write execution cost on the target network.

No private keys, wallet passwords, mnemonics, or signing material belong in the repository or transaction evidence.
