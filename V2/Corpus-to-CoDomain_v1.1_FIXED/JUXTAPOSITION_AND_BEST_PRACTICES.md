# Juxtaposition & Best Practices — Corpus-to-Co-Domain versus RhetoricsID

## Scope / provenance

Compared three user-supplied artifacts: `CORPUS_TO_CODOMAIN_DELIVERABLES(1).zip` (containing `index.html`, Algorithm Documentation, Validation Report), `Pasted markdown(6).md`, and `rethoricsID-1.1(1).html`. Do not infer common authorship or that the latter HTML is an agreed target architecture. The ZIP passed CRC (`ZipFile.testzip() == None`); compression damage was not reproduced. The pasted Markdown displays mojibake for several symbols (`â€”`, `Î¸`, `Ã—`), whereas the Markdown inside the ZIP has the correct Unicode symbols. The latter is an encoding discrepancy affecting the pasted copy, not proof of ZIP corruption.

## A. The theoretical contrast

**Corpus-to-Co-Domain** is an **observational stylometry instrument**. It observes `D` under preprocessing `θ`, generates statistics and traceable evidence `S`, then proposes four prompt presets `G(S, configuration) = P`. It does not infer semantic equivalence or execute `P+T` against an LLM.

**RhetoricsID Runtime Policy Builder** is a **normative/instructional control-plane prototype**. It constructs `Input` (audience, goals, text function, stakes, signals) and `Runtime` (rule IDs, precedence, argument moves M1–M7, marker enforcement/suppression, humanize presets, compact frequency outputs) as `rewrite_engine_run: { Input, Runtime }`. Its sample-text counts are ancillary: the hard-coded `style.syntax.mean = 22`, `max = 40`, `meta` fields and rhetorical examples should not be recategorized as corpus-derived findings.

These are not rival implementations of the same estimator. They should be *juxtaposed by jurisdiction*:

| Question | Corpus analysis | Policy builder | Handoff rule |
|---|---|---|---|
| What actually occurs in corpus? | Observed token spans, n-grams, paragraph positions | Optional text counts | Only observed features may be tagged as measurements. |
| What is suggested about rhetoric? | Rule-based cues and evidence-status labels | Rules, marker policies and custom phrases | Inferences remain hypotheses until context adjudication. |
| What should the output do? | Four evidence-aware preset families | Audience/goal/stakes-governed rhetorical plan | User policy is chosen/declared, never reverse-inferred as fact. |
| What must not change? | Semantic invariants in preset prompt | Auditability and high-stakes/replication guards | Hard safety/fidelity constraints outrank stylistic preferences. |
| How is an artifact transported? | Editable prompt + JSON containing measured signature | `Copy Merge` for Input+Runtime | One schema-defined bundle, with provenance and conflicts resolved. |

## B. Target architecture — recommended, not implemented in the combined app

```
Reference corpus D --- parameters θ ---> A(D, θ)
                                           |
                                           v
                                 Style signature S
                      [observed | heuristic | undetermined]
                                           |
Operator intent ---------------------------+------> Policy Resolver
  audience, stakes, genre, goals                      |
  selected rhetorical moves, constraints              v
                                         Validated Instruction Contract
                                [hard constraints | soft preferences]
                                                      |
                                               Serializer G
                                                      |
                                    {prompt, style_signature, policy,
                                     provenance, validation, parameters}
                                                      |
                                               external LM + T
                                                      |
                                      paraphrase + independent evaluation
```

**Do not silently compose Corpus-to-Co-Domain classifications into RhetoricsID rules.** Introduce a typed handoff and explicit precedence if full integration is commissioned. `genre:Academic` inferred from citation-like strings cannot itself activate high-stakes `method_section` rules. Likewise `stakes:high` chosen by the user cannot be promoted to an empirically observed corpus property. Keep a field-origin enum: `OBSERVED`, `HEURISTIC`, `CONTEXT_REVIEWED`, `MANUAL`, `UNKNOWN`. If a high-stakes safeguard conflicts with stylistic disorientation, *the safeguard wins*, and the rejected style operation must be documented. Distinguish instructional feature weighting from empirical similarity measures; 40/30/30 is an attention budget, **not a validated statistical estimator**.

A proposed integration schema:

```json
{
  "schema": "corpus-policy-handoff/proposed-1",
  "style_signature": {"observed": {}, "heuristic": {}, "undetermined": []},
  "policy_input": {"audience": "mixed", "stakes": "medium", "goals": []},
  "resolved_policy": {"hard_constraints": [], "soft_preferences": [], "conflicts": []},
  "prompt": "<verbatim user-edited text>",
  "source_provenance": {"corpus": "local", "manual_overrides": []}
}
```

This *proposed* schema is not represented as an implemented runtime merge. The implemented `unified-1.1` format is currently the Corpus-to-Co-Domain JSON plus verbatim prompt, without the separate RhetoricsID policy runtime.

## C. Confirmed bug and repair record

| Severity / status | Observed reproducer | Root mechanism | Patch + regression |
|---|---|---|---|
| High / CONFIRMED | Edit prompt; switch preset A→B→A; the edit disappears | `renderPreset()` assigned a fresh generated string on all redraws | Per-preset drafts; preserve manual content; `Reset preset` explicitly regenerates; V04–V10 pass. |
| High / CONFIRMED | Edit prompt; change a slider or target T; edit disappears | Parameter input invoked unconditional regeneration | Draft with `stale` flag, bundle declares its status; V05–V07 pass. |
| Medium / CONFIRMED | Type `argumen` in RhetoricsID first custom phrase; only `a` remains | `renderPhraseInputs()` replaces the actively focused DOM input every keystroke | Regenerate visible rows only when a new slot is needed, then restore selection; R01–R03 pass. |
| Medium / CONFIRMED | RhetoricsID token `Namunsaja` triggers substring `namun` | `countOccurrences()` uses substring rather than token/phrase boundaries | Count token phrases within sentence units; R07 pass. |
| Medium / CONFIRMED | RhetoricsID word n-grams span punctuation and sentence boundary | N-grams built on flattened, filtered entire document stream | Use per-sentence raw word streams; prevent skipped-term bridging; R08 pass. |
| Medium / CONFIRMED | `post_exclude` versus `pre-exclude` did not implement true rank-before-filter semantics | Excluded terms removed before both ranking branches | Branch ranking before versus after masking; R09 pass. |
| Medium / CONFIRMED | No Bootstrap CDN: grid class `.row` is computed as `block` | Bootstrap stylesheet not embedded; fallback lacked grid utilities | Minimal local CSS fallback; checked `display:flex`, 4-column desktop metric grid and 2-column narrow metric layout. |
| Product gap / CONFIRMED | No clipboard button for prompt and JSON together | Only `Copy full preset` and `Export JSON` separately | New `Copy Prompt + JSON`, `buildUnifiedPayload()` shared with JSON export; V11–V18 pass. |
| Encoding / CONFIRMED | Pasted Markdown displays `Î¸`, `â€”`, `Ã—` | Pasted text has encoding corruption | Preserve original ZIP Markdown as canonical; do not overwrite it with pasted copy. |

## D. Best-practice control contract

**Measurement:** retain original span offsets; define the denominator and corpus units for every statistic; report ties deterministically; treat sentence splitting as heuristic; separate raw from filtered counts, and preserve negation, modality and connectives in the rhetorical stream. Never let filtered-token deletion create n-grams that were not adjacent. Document sample size and invalid metric cases (`null` instead of a guessed value). For language detection, prefer independently tested and language-specific resources before claiming calibrated ID/EN classification; `Intl.Segmenter` could improve boundaries if evaluated against ID/EN annotated examples.

**Inference:** use `Feature → Context → Candidate Function → Evidence → Status/Limit`; require contextual review to claim rhetorical function. Treat topic, genre, historical era and manuscript section as confounders. Use text-unit perturbations (shuffle paragraphs, duplicate corpus, add negation, change topic) as falsifiers; do not infer a writer's stable identity from one text. Keep observed values stable when users manually change style label preferences.

**Prompt construction:** keep lexical/syntactic/discourse emphasis as user-adjustable *soft preferences*, not as a universal similarity score. Make semantic preservation, negation, quantities, named entities, attribution, and argument stance hard constraints. Let high-stakes/auditability guards override embellishment. Preserve the exact human-edited prompt; when analysis changes, flag `stale` rather than rewriting user edits. Do not include full reference corpus in export by default; avoid copying long idiosyncratic passages as templates.

**Serializer / clipboard:** use a single stable schema with explicit version (`corpus-to-codomain/unified-1.1`), one `prompt` field containing the user's current editor value, measured `style_signature`, user-selected `parameters`, and distinguish `observed`, `heuristic` and `manual` provenance. Ensure clipboard copy and JSON export share the exact same function. A machine-readable bundle should remain valid JSON; don't concatenate two top-level JSON objects. Implement Clipboard API with a tested fallback for restricted `file://` browser environments and an accessible selectable copy-area if both APIs fail. Note that a JSON export containing the prompt can itself contain T and recurring reference n-grams—privacy-minimized is not anonymous.

**Security / front-end:** never evaluate corpus text; use `textContent`, `.value`, or appropriately escaped output for user-supplied strings. OWASP recommends safe DOM text sinks rather than direct `innerHTML` for arbitrary text. Keep file processing client-side. Verify responsive layout without relying on CDN availability, keyboard access and fallback clipboard states. Avoid full DOM re-creation while the user types in an active field.

**Testing / shipping:** maintain a standalone browser test reproducing the original failure and an adjacent negative control (unedited prompt *does* update after config changes). Run in-browser A–I corpus fixtures; run browser integration including prompt edits, clipping, JSON parse, mobile width, and malicious HTML input. Record commands, files, exit status, and known omissions. ZIP integrity is not sufficient to establish application correctness. Avoid claims of measured linguistic accuracy absent a labeled gold standard and out-of-sample testing.

## E. Deployment and maturity distinctions

- **Fully implemented in revised Corpus-to-Co-Domain:** deterministic corpus analysis baseline; four presets; draft preservation and warnings; one-click unified clipboard; matching JSON export; offline responsive fallback.
- **Heuristically approximated:** ID/EN language detection, passive prefixes, framing/commonplace labels, syntactic pattern proxy, discourse adjacency networks.
- **Requires resources not supplied:** reliable POS/parser, entity resolution, RST-style relations, semantic entailment tests, attribution accuracy benchmarking.
- **Not implemented:** combined RhetoricsID policy resolver, external LLM paraphrase execution, actual cross-application handoff, semantic faithfulness certification.

**Smallest decisive next experiment:** collect a controlled multi-document bilingual corpus with known paragraph/genre/topic labels. Compare held-out authored works and topic-shuffled controls. If style descriptors no longer discriminate after topic control, avoid attributing them to individual style. For integration, first define conflict precedence and the typed input/output contract of the resolver; only then build the UI merge.
