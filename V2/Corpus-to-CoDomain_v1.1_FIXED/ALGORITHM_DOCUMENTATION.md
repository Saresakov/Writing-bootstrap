# Corpus-to-Co-Domain — Algorithm Documentation

**Version:** 1.1 browser prototype (with v1.1 follow-up fixes)
**Runtime:** a single `index.html` containing HTML, inline CSS, core JavaScript and UI JavaScript. Bootstrap 5 CSS is loaded via CDN; the analysis itself has no network dependency, API key, database or backend.
**Units:** input corpus D; Unicode-preserved token stream; words/numbers; sentence (heuristic); paragraph (separated by blank lines); n-gram type and occurrence; marker hit; directed graph node/edge.

## 1. Computational contract

`D + θ → preprocessing(D, θ) → n-gram/statistical features → evidence-aware S → G(S, preset configuration) → P`.

`T` is *never* joined into the reference corpus. It is inserted as the optional target text in P. The application does **not** execute `P + T` against an LLM, and cannot certify semantic equivalence after paraphrasing.

All core processing occurs on the client. Export JSON occurs only upon explicit download. No corpus is uploaded or stored remotely; loading Bootstrap styling can create a CDN request unrelated to text processing.

## 2. Deterministic preprocessing

- Store the original corpus, paragraph offsets and token offsets (JavaScript UTF-16 indices; use `original.slice(start,end)` for auditing). Token character lengths use Unicode code-point counting.
- Paragraphs are separated by one or more blank lines; a single newline inside a paragraph is not a paragraph break. Original text is retained; no destructive rewrite.
- Tokenizer: Unicode letters/numbers with a basic internal hyphen/apostrophe rule, decimal numeric sequences, and individual non-whitespace punctuation. No external morphological tokenizer.
- Unicode case-folding: lowercasing via `toLocaleLowerCase()`; normalization setting NFC or NFKC. This is a separate comparison layer from the original surface form. Full linguistic Unicode case folding is not implemented.
- Sentence segmentation: punctuation `.?!` and a limited abbreviation rule; this is **not** supervised sentence-boundary detection. Potential abbreviations and uncommon punctuation may be missegmented.
- Every token records original, normalized, paragraph/sentence IDs, token position within paragraph, relative word position, word length, category (`word`, `number`, `punctuation`), and source offsets.
- Language detection is a stopword-count heuristic returning `id`, `en`, `mixed`, or `undetermined`. User-selected language is a mode/preference; it alters eligible rhetorical-marker dictionaries but is **not** evidence of the actual language. No neural ID/EN detection.
- Raw word stream is preserved. Filtering allows ID-only, EN-only, both, or no built-in stopword lists, plus case-insensitive custom contiguous stopwords/phrases separated by comma or line break. A protected negation/discourse-marker set is optional. Crucially, filtered n-grams do **not** skip filtered words to invent new neighbor relations.
- Word n-grams are computed within sentence and paragraph boundaries. Punctuation-aware bigrams use the original complete token sequence in a separate view.

## 3. Definitions and equations

Let `N` be the number of available word tokens in the active mode, `P` the number of non-empty paragraphs, `c(g,p)` the number of occurrences of n-gram type `g` in paragraph `p`, `f(g)=Σ_p c(g,p)`, `df(g)=|{p:c(g,p)>0}|`.

| Statistic | Operational definition | Validity qualification |
|---|---|---|
| Absolute frequency | `f(g)` | Frequency is not semantic relevance. |
| Relative frequency | `100 × f(g) / N` | Denominator is active word-token count, not n-gram-window count. |
| Document frequency | `df(g)` with **paragraph as document** | Corpus segmentation defines document units. |
| Paragraph dispersion | `df(g)/P` | P≥1. Occurrence is not semantic continuity. |
| TF-IDF | `mean_p[(c(g,p)/wordCount(p)) × (1+ln((1+P)/(1+df(g))))]` | Smoothed, paragraph-averaged implementation; paragraph denominator uses raw word count. |
| Burstiness | `Var_population({c(g,p)}) / Mean({c(g,p)})` | Fano factor; unavailable if P<2 or mean=0. This is *not* a historical timing model. |
| Positional distribution | Hit counts in thirds of paragraph sequence + exact word/span offsets | Paragraph order is text order, not authoring chronology. |
| PMI-style association | `log2[(f(g)/total_ngram_windows)/Π_i(unigramCount(word_i)/rawWordCount)]` | Only when f≥2. Generalized independence approximation (including trigrams), not semantic strength. |
| Contextual diversity | `distinct (L:preceding word / R:following word) / (2×f(g))` | Types of adjacent words; not semantic contexts. |
| TTR | `unique normalized word types / word tokens` | Very length-sensitive; not directly comparable across unequal corpora. |
| MATTR-50 | Mean TTR over every overlapping 50-word window | `null` if N<50. |
| Mean word length | Mean Unicode code-point length of words and numbers | Formatting and mixed languages influence distribution. |
| Sentence mean, variance | Word count per heuristically segmented sentence; population variance | Variance unavailable for one sentence. |
| Paragraph-length bin | Quantiles at 33% and 67% (or inclusive manual cutoffs) | Short/Medium/Long are corpus-relative. |
| Word-length histogram | Number of words for each code-point count | Separate from paragraph length. |
| Transition density | 100 × (contrast+cause+addition marker hits) / paragraph word count | Rule-based proxy, not proven discourse function. |

### Frequency bands and ties

Sort **unique n-gram types** by frequency ascending, breaking type ties alphabetically for deterministic display. Define the percentile value `q_k = sortedFrequencies[floor(k/100 × (M−1))]`, `M=number of types`. Default `k=25,75`, configurable.

- `Lower`: all `f≤q25`;
- `Median`: `q25<f≤q75`;
- `Upper`: `f>q75`.

**Tie rule:** all types with equal frequency share one band, assigned to the lowest applicable band. Bands may be empty. Every type is allocated exactly once. The UI shows type counts, summed occurrence counts, boundary values, frequency per 100 tokens, and n-gram KWIC. No band is automatically a semantic class.

## 4. Context and inferential safeguards

KWIC shows up to 28 occurrences for the chosen type: original source text around the source span, paragraph and sentence IDs, original offsets, and paragraph count/density. Clicking a paragraph heatmap cell shows its original excerpt and length histogram.

Surface cue dictionaries cover contrast, cause, addition, conditionality, epistemic modality, negation, examples, definition, evidence and coordination in ID/EN. The selected n-gram receives a *candidate* function only when its source span overlaps a matching cue. A cue within two or more paragraphs is labeled `context-supported` (still heuristic); one observed context is `rule-based`; absence is `unverified`. This does not constitute validated rhetorical relation extraction.

The style-profile classifications default to `Unknown` or `Undetermined`. Limited explicit rules may propose comparative/cause framing, commonplaces, academic genre (citation-like surfaces), or exploratory textual temperament. Each proposal stores evidence excerpt, supporting paragraph IDs, heuristic explanation and evidence status. A manual override changes only the **instructional** style profile; it never alters corpus evidence. Genre supports multiple manual labels. Era is not inferred from diction.

**Never infer:** semantic similarity from lexical co-occurrence, an author’s identity or personality, the historical era, evidence quality, writer intentions, or the correctness of causal/argumentative relations.

## 5. Three graph layers

A graph is a directed weighted **network representation** rather than a mathematical claim about continuity or homeomorphism.

1. **Lexical adjacency:** node = frequent normalized lexical unit; edge = within-sentence successor `w_i→w_(i+1)`; weight = observed adjacency count; normalized annotation = count/total word-adjacency opportunities.
2. **Syntactic pattern proxy:** nodes = marker category and sentence ID; edge = `markerClass→sentence` when string-cue observed; edge weight = matching cue count. This is not a parse tree or dependency network.
3. **Discourse sequence:** node = paragraph; edge `P_i→P_(i+1)`; weight = 1 adjacency; separate annotation = adjacent-paragraph lexical Jaccard overlap `|A∩B|/|A∪B|`. No semantic entailment from Jaccard.

Filters hide edges whose integer weight is below the selected threshold. Weakly connected components use undirected projection of **filtered** edges. In/out degree and weighted degree are shown. Normalized weighted-degree centrality = `(weighted in+weighted out)/sum(all endpoint weights)`. The display limits graph size to 24 lexical or 30 other nodes; therefore network summaries describe the **displayed subgraph**, not the full corpus graph. Community detection is not implemented.

## 6. Co-domain and preset logic

Preset A: high-fidelity stylistic reconstruction. Preset B: rhetorical organization transfer. Preset C: lexical/syntactic adaptation. Preset D: balanced semantic paraphrase.

Each embeds actual measured quantities (corpus size, mean sentence length, TTR, MATTR if valid, recurring marker frequencies, distributed n-grams, and paragraph length groups), supported or manually overridden labels, and a **normalized user-set instructional emphasis** for lexical/syntactic/discourse dimensions. Weights prioritize instructions only: they are not similarity coefficients or calibrated inferential probabilities.

Each preset includes `TASK DEFINITION`, `TARGET STYLE SIGNATURE`, `MEASURED CHARACTERISTICS`, `SUPPORTED RHETORICAL TENDENCIES`, `TRANSFORMATION INSTRUCTIONS`, `SEMANTIC INVARIANTS`, `NEGATIVE CONSTRAINTS`, `UNCERTAIN PROPERTIES EXCLUDED`, `INPUT TEXT T`, `VALIDATION CHECKLIST`, and `OUTPUT`. Semantic invariants include propositions, logic, negation, entities, citations/attribution, numerical values, level of certainty, and stance. Long corpus passages are not inserted as imitation templates.

Prompts are manually editable. `Reset preset` reconstructs the current one from the current core state. Copy uses Clipboard API then legacy `execCommand('copy')`, with manual-select fallback. Export generates a client-side JSON download. Neither operation invokes an LLM.

## 7. Implementation / non-implementation matrix

| Capability | State |
|---|---|
| Unicode-aware original span tracing, paragraph segmentation, IDs | Implemented (segmentation heuristic) |
| Configurable case layer, NFC/NFKC, ID/EN stopword and phrase masking | Implemented |
| Raw/filtered/comparative n-grams, KWIC, bands and deterministic ties | Implemented |
| Frequency, dispersion, TF-IDF, Fano, positional density | Implemented |
| Character length, word-length bins, TTR, MATTR-50 | Implemented |
| Three directed network layers, weighted degree, weak components | Implemented (graph subset limitation) |
| Manual labels, four source-grounded editable presets | Implemented |
| Browser file input, JSON export, clipboard fallback | Implemented; OS clipboard permissions vary |
| Code switching and linguistic class labels | Heuristic, uncalibrated |
| Passive markers, genre/framing/topoi/temperament | Heuristic, underdetermined when unsupported |
| Emotion, abstraction, register, narrative voice, interpretive discourse | Not reliably derived; manual contextual review required |
| POS model, dependency depth, verified entity recognition | Requires external NLP resources |
| Semantic paraphrase execution, semantic fidelity scoring | Not implemented / external LLM required |
| Community detection, authorship attribution, mathematical topological invariants | Not implemented |

## 8. Assumptions, threats and falsifiers

A single corpus does not identify a stable authorial trait; topic and genre are major confounders. Repetition can alter TTR and TF-IDF, and moving paragraphs can alter positional patterns without altering within-paragraph lexical frequencies. Very short corpora must not produce confident style labels. Negation counterexamples disprove any claim that overlap implies semantic agreement. Citation strings are not verification that sources exist. Surface prefixes are not POS tags.

This prototype is an **instrument for exploratory corpus description and instruction construction**, not an empirically validated stylistic imitation or semantic-paraphrase model.

## 9. v1.1 patch: unified clipboard, stable drafts, offline styling

`index.html` (v1.1) implements `buildUnifiedPayload()` as the **single source of serialization truth** for both `Copy Prompt + JSON` and `Export JSON`. The clipboard receives exactly one syntactically valid JSON object (no concatenated incompatible fragments). It has root-level `schema`, `kind`, `preset`, `prompt` (the exact currently displayed, possibly edited text), `prompt_state`, `parameters`, `style_signature`, and `provenance`. Full original reference corpus is deliberately omitted, but recurring n-grams and the target text already embedded inside the prompt can reveal parts of the supplied material. This is a privacy-minimized export, **not guaranteed anonymization**.

**Prompt draft state machine:** one independent draft per preset A/B/C/D. An unedited draft is regenerated when measured input or configuration changes. When manually edited, the text is preserved even if preset selection, weights, target text, filtering, or corpus changes; the UI reports `MANUAL EDIT`, and `stale` is true if the freshly generated prompt would now be different. `Reset preset` is the explicit destructive action for the active preset only. `Clear` removes corpus and all drafts. The exported JSON declares `edited` and `stale` separately instead of pretending the saved text automatically matches the current statistics.

**Clipboard behavior:** try `navigator.clipboard.writeText(...)`; otherwise attempt an ephemeral textarea + legacy `document.execCommand('copy')`; if both fail, display a persistent, selectable textarea containing the **whole JSON**, without marking the action as successful. Existing `Copy prompt only` remains available and uses the same fallback mechanism.

**Bootstrap offline fallback:** the Bootstrap 5 CDN link remains; on successful stylesheet load an HTML class disables the minimal inlined grid/form/button fallback. When network resources are blocked, the app remains responsive enough for local use, with no third-party data processing. This fallback is a practical approximation, not a vendored copy of all Bootstrap CSS or a pixel-equivalent offline theme.

## 10. Independent policy builder (`rethoricsID-1.2-fixed.html`)

This companion artifact is kept separate. Its `Input + Runtime` policy rules are not measurements of the target writer. The revised companion fixes input focus on dynamic phrase entry, verifies clipboard fallback, changes string-substring marker counts to whole-token phrase counts within sentence segments, prevents n-grams and contexts from spanning sentence boundaries or filtered-token gaps, implements pre-/post-exclusion backfill as two distinct algorithms, and substitutes `unknown` for an unearned hard-coded historical era. It is **not yet wired into** Corpus-to-Co-Domain, so there is no unsupported claim of a complete end-to-end combined policy engine.
