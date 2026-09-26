# Evidence model

## Registered observation

`submit_evidence(evidence_id, claim_id, source_url, relationship, target_evidence_id="")` stores the caller alias, immutable claim link, HTTPS URL, submitter, and assertion. The only retrieval type is fixed by the contract to `RENDER_HTML`. Hashes are not accepted from callers.

During verification, leader and validator render the same stored URL independently. For rendered HTML `R`, canonical visible text `T`, claim identifier `C`, and URL `U`:

```text
render_hash  = SHA256(UTF8(R))
content_hash = SHA256(UTF8(lowercase(collapse_whitespace(strip_comments_tags_script_style(R)))))
evidence_id  = SHA256(UTF8(JSON_COMPACT([
  "provenance-evidence-v1", C, U, "RENDER_HTML", render_hash,
  content_hash, "html-text-whitespace-lower-v1"
])))
```

`JSON_COMPACT` uses JSON array ordering, UTF-8 encoding, and separators `,` and `:` without spaces. It does not add a timestamp or submitter. Identical artifact observations derive identical IDs; an observation alias remains available to clients.

## Finding and graph

Consensus classifies claim relationship as `SUPPORTS`, `CONTRADICTS`, or `INSUFFICIENT`, and separately classifies a target relation as `NONE`, `SUPERSEDES`, `EXPIRES`, or `RESTORES`. An unavailable render produces `UNAVAILABLE` without asking the model to call it contradictory. Graph edges can only point to an older verified item. `SUPERSEDES` and `EXPIRES` deactivate the target; a later `RESTORES` edge reactivates it. All edges and evidence remain in history.

## Deterministic aggregate

Only active findings younger than seven days affect current semantic state. Both support and contradiction yield `DISPUTED`; support alone yields `CONFIRMED`; contradiction alone yields `CONTRADICTED`; no decisive active finding plus stale evidence yields `STALE`; unavailable-only evidence yields `UNAVAILABLE`; otherwise status is `INSUFFICIENT`. One source may confirm a claim. No publisher-independence guarantee is made.
