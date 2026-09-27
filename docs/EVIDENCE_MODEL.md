# Evidence model

## Registered observation

`submit_evidence(evidence_id, claim_id, source_url, relationship, evidence_class, expected_digest, target_evidence_id)` stores a caller-selected observation ID, immutable claim link, HTTPS URL, submitter, evidence class, optional pin assertion, and relationship assertion. That observation ID is never changed. The derived canonical artifact ID is stored separately per observation and is not an alias lookup key, so repeated artifacts cannot overwrite the caller-ID index. For GenLayer CLI calls, use the literal string `NONE` for an absent digest or target; its scalar parser turns a bare empty positional argument into integer zero.

Implemented classes are deliberately limited to `PINNED_TEXT` and `RENDERED_WEB`. `PINNED_JSON` and `VISUAL` are not exposed as supported classes.

### PINNED_TEXT

Leader and validator independently call `gl.nondet.web.get`, require a successful HTTP status and bounded nonempty response bytes, and recompute SHA-256 over the exact bytes. The caller's expected digest is only an assertion. A mismatch is stored as `INTEGRITY_MISMATCH`, is treated as unavailable for claim aggregation, and is never passed to the semantic model. UTF-8 decoding is required after the byte digest matches; decoding failure is `INSUFFICIENT`.

```text
content_hash = SHA256(response.body exact bytes)
render_hash  = ""
```

The model receives the decoded text as untrusted data only after the exact digest check. It classifies the text against the immutable claim; validators repeat both retrieval/hash and semantic interpretation. Normalized artifacts larger than 16,384 characters are stored as inconclusive rather than classifying a truncated prefix. For accepted artifacts, the complete canonical text is sent to consensus and retained for later graph review.

### RENDERED_WEB

Leader and validator independently render the same URL using `gl.nondet.web.render(url, mode="html")`. For rendered HTML `R` and canonical visible text `T`:

```text
render_hash  = SHA256(UTF8(R))
content_hash = SHA256(UTF8(lowercase(collapse_whitespace(strip_comments_tags_script_style(R)))))
```

For either evidence class, identity is `SHA256(UTF8(JSON_COMPACT(["provenance-evidence-v1", claim_id, source_url, evidence_class, render_hash, content_hash, class_normalization_version])))`. `JSON_COMPACT` uses JSON array ordering, UTF-8 encoding, and separators `,` and `:` without spaces. It does not add a timestamp or submitter. Identical artifact observations derive identical IDs; an observation alias remains available to clients.

## Finding and graph

The class-specific normalization versions are `exact-response-bytes-sha256-v1` and `html-text-whitespace-lower-v1`. No timestamp or submitter enters identity.

Consensus classifies claim relationship as `SUPPORTS`, `CONTRADICTS`, `INSUFFICIENT`, or `UNAVAILABLE`, and separately classifies a target relation as `NONE`, `SUPERSEDES`, `EXPIRES`, or `RESTORES`. An unavailable source or pin mismatch is never called contradictory. Graph relations require rendered web evidence from the same exact submitted host as the older target, and validators receive that target's retained canonical content and observation hashes. Pinned text cannot create graph edges. `SUPERSEDES` and `EXPIRES` deactivate the target; a later `RESTORES` edge reactivates the target. All edges and evidence remain in history.

## Deterministic aggregate

Only active findings younger than seven days affect current semantic state. Both support and contradiction yield `DISPUTED`; support alone yields `CONFIRMED`; contradiction alone yields `CONTRADICTED`; no decisive active finding plus stale evidence yields `STALE`; unavailable-only evidence yields `UNAVAILABLE`; otherwise status is `INSUFFICIENT`. One source may confirm a claim. No publisher-independence guarantee is made.
