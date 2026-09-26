# Consensus design

`verify_claim` is the only method that uses non-deterministic web and model operations. Each validator independently observes the submitted source URL and evaluates its relevance to the immutable claim. GenLayer's comparative equivalence principle accepts only semantically equivalent responses.

The contract then parses exactly two fields from that consensus response: `decision` and `visual`. It rejects missing fields, surplus fields, malformed JSON, and values outside the allow-lists. Raw fetched text and raw model output are never persisted.

After the consensus boundary, relationship handling, hashes, receipts, identifiers, storage, freshness arithmetic, authorization, and escrow transitions are deterministic.

The state machine never treats uncertainty as confirmation:

| Consensus decision | Evidence verification | Claim status |
| --- | --- | --- |
| `SUPPORTS` with declared `SUPPORTS` | `VERIFIED` | `CONFIRMED` |
| `CONTRADICTS` or declared contradiction | `CONTRADICTED` | `CONTRADICTED` |
| `UNAVAILABLE` | `UNAVAILABLE` | `UNAVAILABLE` |
| any insufficient outcome | `INSUFFICIENT` | `INSUFFICIENT` |
