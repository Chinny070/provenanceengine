# Architecture

```text
immutable claim + admitted HTTPS URL
                 |
          independent validators
        /                       \
 browser render + hashes     browser render + hashes
 semantic claim finding      semantic claim finding
 target graph finding        target graph finding
        \                       /
         typed consensus result
                 |
 server-side digest and identity checks
                 |
 append-only evidence + optional targeted edge
                 |
 deterministic active-graph status and freshness
          /                       \
 provenance passport         bounty settlement
```

Only `verify_claim` crosses into web/LLM nondeterminism. Contract state is read before the call and written only after the leader result passes independent validation. Evidence identity binds the observed artifact; graph relations bind a specific earlier evidence ID. Both claim views and bounty settlement call the same deterministic status derivation.

The contract bounds claims, evidence, edges, history, and bounties. Graph changes never delete a historical item. The portable passport is JSON intended for other contracts/services to consume; it is not a signed off-chain certificate or publisher signature.
