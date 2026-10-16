# Corpus-to-Co-Domain — Validation Report

**Execution record:** October 15, 2026. Core tests executed in Node.js v22; DOM/UI probes executed through Playwright against the bundled HTML in Chromium headless with external CDN requests blocked. Both suites exercise actual JavaScript, not inspection-only PASS claims.

**Summary:** core **12/12 PASS**; browser initial **17/17 PASS**, browser extended **9/9 PASS**. These are implementation checks, not corpus-linguistic external validation.

## Test A–I and additional engine regression tests

| Test / input fixture | Expected | Actual execution | Status |
|---|---|---|---|
| A original offsets, language, punctuation, Unicode, abbreviations | Two paragraphs, original offsets reversible; Unicode token retained | Assertion returned `PASS` in Node.js | PASS |
| B exact accounting | Bigram `alpha beta` count=2, document frequency=2, normalized frequency=33.333/100 tokens | Assertion returned `PASS` in Node.js | PASS |
| C stopword ablation without bridging | Negation preserved; custom filtering does not manufacture `alpha beta` across removed `dalam` | Assertion returned `PASS` in Node.js | PASS |
| D ties exact partition | Four equal/tied candidates assigned exactly once | Assertion returned `PASS` in Node.js | PASS |
| E relocate | Internal n-gram frequency invariant under paragraph swap, positional paragraph IDs change | Assertion returned `PASS` in Node.js | PASS |
| F duplicate | Word/token count doubles; TTR changes in expected direction | Assertion returned `PASS` in Node.js | PASS |
| G insufficient | `MATTR` and 1-sentence variance return null and warnings are emitted | Assertion returned `PASS` in Node.js | PASS |
| H negation no semantic similarity claim | Do not infer semantic equivalence between positive and negative statement | Assertion returned `PASS` in Node.js | PASS |
| I HTML original safe core; no code evaluation | Raw HTML retained as string and not executed in analysis engine | Assertion returned `PASS` in Node.js | PASS |
| all 4 prompt presets use corpus and semantic invariants | Four distinct full prompts, invariants and optional T placeholders | Assertion returned `PASS` in Node.js | PASS |
| graph weak components and threshold deterministic | At least one connected component and a valid directed edge set | Assertion returned `PASS` in Node.js | PASS |
| per-paragraph sentence segmentation never bridges | No n-gram crosses sentence/paragraph boundaries | Assertion returned `PASS` in Node.js | PASS |

## Real-browser UI probes

| Browser action | Expected | Actual | Status |
|---|---|---|---|
| empty state | Corresponding UI state or safe behavior observed | Assertion result `True` | PASS |
| sample and results | Corresponding UI state or safe behavior observed | Assertion result `True` | PASS |
| ngram tables | Corresponding UI state or safe behavior observed | Assertion result `True` | PASS |
| graph SVG | Corresponding UI state or safe behavior observed | Assertion result `True` | PASS |
| KWIC evidence and heatmap | Corresponding UI state or safe behavior observed | Assertion result `True` | PASS |
| prompt A comprehensive | Corresponding UI state or safe behavior observed | Assertion result `True` | PASS |
| B differs | Corresponding UI state or safe behavior observed | Assertion result `True` | PASS |
| text T inserted | Corresponding UI state or safe behavior observed | Assertion result `True` | PASS |
| filter changes state | Corresponding UI state or safe behavior observed | Assertion result `True` | PASS |
| n-size changes | Corresponding UI state or safe behavior observed | Assertion result `True` | PASS |
| discourse layer | Corresponding UI state or safe behavior observed | Assertion result `True` | PASS |
| edge filtering | Corresponding UI state or safe behavior observed | Assertion result `True` | PASS |
| manual override | Corresponding UI state or safe behavior observed | Assertion result `True` | PASS |
| in app A-I tests | Corresponding UI state or safe behavior observed | Assertion result `True` | PASS |
| XSS not executed | Corresponding UI state or safe behavior observed | Assertion result `True` | PASS |
| no browser exceptions | Corresponding UI state or safe behavior observed | Assertion result `True` | PASS |
| clear state | Corresponding UI state or safe behavior observed | Assertion result `True` | PASS |

## Extended browser probes

| Probe | Actual | Status |
|---|---|---|
| genre multi-label | `True` | PASS |
| custom era is manual metadata | `True` | PASS |
| language mode affects analysis | `True` | PASS |
| core retains original with KWIC | `True` | PASS |
| clipboard writes full edited prompt | `True` | PASS |
| clipboard fallback selects prompt | `True` | PASS |
| JSON export browser download | `True` | PASS |
| file upload loads corpus | `True` | PASS |
| mobile: analysis remains usable | `True` | PASS |

## Limits of the tests

- Clipboard success was verified with an injected clipboard stub that captured the complete copied string; fallback was verified with Clipboard API disabled and `document.execCommand` forced to fail. Actual OS clipboard permissions remain environment dependent.
- Browser Blob JSON export and `.md` file-input event were executed and passed. The native operating-system file-picker dialog itself was not tested.
- Mobile (390 px) basic visibility was verified, but full visual appearance across all resolutions and real devices was **not independently screenshot-reviewed**.
- Input sanitization was browser-probed using injected HTML/script text: the analysis completed without executing injected content or creating injected DOM images. This does not constitute a complete security audit.
- Statistical properties were checked by deterministic small fixtures, not against an externally curated linguistic gold standard. Genre, rhetorical function, language detection and style similarity have **no measured predictive accuracy**.
- CDN load was intentionally blocked to demonstrate that core analysis and controls continue operating without the visual dependency. That does not guarantee identical appearance when offline.

## Confirmed failure discovered and repaired

**Browser parsing (CONFIRMED → FIXED):** an early JavaScript test fixture included a literal closing `</script>` tag within an inline JS string. HTML parsing terminated the inline script and raised syntax errors, even though `node -c` checks of the standalone JS source succeeded. **Repair:** split the string construction so the HTML parser never sees a literal closing script delimiter. **Regression check:** the full browser UI suite now executes with no page errors. This is why independent DOM execution was necessary rather than relying only on JS syntax tests.

## Residual risks / falsifiers

A corpus of multiple documents from the same author could reveal that inferred framing here is topic-specific rather than style-specific. A bilingual linguistic gold standard could reveal systematic language-detection and marker-count errors. A POS/dependency-annotated dataset could falsify the passive-marker proxy or sentence parsing. Until such comparisons are available, do not report statistical accuracy or human-equivalent style replication.

## v1.1 follow-up: bugs, browser-level regression and unchanged limitations

**Baseline comparison:** the user-uploaded archive `CORPUS_TO_CODOMAIN_DELIVERABLES(1).zip` passed CRC with three entries. Structural validity of the archive does not prove runtime correctness. Independent browser reproduction demonstrated lost user prompt edits after preset change or weight adjustment. The accompanying RhetoricsID page also lost focus after one character in custom-phrase entry.

**Modified artifact:** `index.html` (v1.1): adds per-preset manual draft protection; explicit reset; stale-state warnings; shared `buildUnifiedPayload()` for clipboard and JSON download; one-click `Copy Prompt + JSON`; `file://`-compatible Clipboard API fallback; and an offline CSS grid/form fallback. The companion `rethoricsID-1.2-fixed.html` fixes focus, clipboard handling, token-boundary marker counts, sentence-bounded n-grams, true pre/post-exclude top-K behavior and removes an ungrounded hard-coded historical era.

**Executed command:** `python test_regression.py`, using installed Playwright with `/usr/bin/chromium`, headless browser, stylesheet network requests blocked. Actual fixture results are recorded in `REGRESSION_RESULTS.json`. **33/33 browser assertions PASS**, covering 24 principal-app checks and 9 independent policy-builder checks. The principal app additionally ran its nine in-browser A–I assertions; the browser suite verifies the A–I aggregate `9/9 PASS` as one of its 24 checks. Do **not** sum the aggregate and its containing test as independent trials.

**Direct witnesses:** manual changes retained after A→B→A preset switching; retained after weights, T and filter changes; `stale` visibly flagged; unedited new preset refreshes, and explicit reset discards one draft. `Copy Prompt + JSON` produces a single parsable JSON object with the editor's verbatim prompt and current measurements; export matches that object exactly for unchanged state. When Clipboard API and legacy copying are intentionally disabled, a selectable JSON textarea appears, and success is not falsely claimed. Upload, XSS example, responsive control presence and SVG graph were exercised. The separate RhetoricsID fixture typed multi-character phrases without losing focus, copied its merge payload, demonstrated fallback selection and tested exclusion and phrase boundaries.

**Offline layout witness:** with Bootstrap CDN requests blocked, computed `#metricCards` display was `flex` at both 1280px and 390px viewports; metric widths were approximately 240px on desktop and 167px on mobile, with no page-wide horizontal overflow. This is a style smoke test, not a pixel comparison of all controls.

**Residual not executed or not validated:** external OS-level clipboard permissions (a stub simulated success and a forced failure tested fallback); Firefox/Safari/WebKit; full screen-reader traversal and WCAG audit; linguistically annotated held-out corpora; full security penetration test; performance stress with 500k-character corpora; LLM-executed semantic paraphrase. These limitations remain open rather than counted as passes.
