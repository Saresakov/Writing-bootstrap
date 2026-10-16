# Corpus-to-Co-Domain v1.1 — fixed deliverables

Open **`index.html`** directly in Chrome or Edge. No server, build step, token, or API key needed. Bootstrap 5 styling attempts to load from CDN; an inlined layout fallback stays functional when offline. Paste a corpus or upload `.txt` or `.md`, click **Analyze corpus**, then open **Co-domain**.

- **Copy prompt only** — copies the exact text in the prompt editor.
- **Copy Prompt + JSON** — copies ONE valid JSON object containing `prompt`, `parameters`, `style_signature`, `prompt_state`, and `provenance` to ONE clipboard operation.
- **Export JSON** — downloads the same unified JSON structure. Repeated exports of an unchanged state serialize identically.
- **Reset preset** — explicitly discards only the current preset's manual draft, not all drafts. Changing settings does not silently overwrite manually edited text; a `stale` indicator warns when the analysis has changed.

The original corpus does not appear as a full-text field inside exported JSON. Nevertheless, the prompt may contain the optional target T and the style signature contains short n-gram excerpts; assess privacy before copying outside the device.

## Files

`index.html`: the principal application. `rethoricsID-1.2-fixed.html`: a separate, patched runtime policy builder included for juxtaposition; it is **not integrated** into the first application. `ALGORITHM_DOCUMENTATION.md`: metric and serialization definitions. `JUXTAPOSITION_AND_BEST_PRACTICES.md`: comparative explanation and hardening recommendations. `VALIDATION_REPORT.md`: observed tests and caveats. `test_regression.py`: runnable Playwright regression cases (requires Python Playwright and a working Chromium installation). `REGRESSION_RESULTS.json`: observed pass/fail receipts from local execution.

## Running the regression script (optional)

In an environment with Python Playwright and Chromium installed, adjust the paths in the test file for the local extraction directory, then run `python test_regression.py`. It was executed in the authoring environment with Chromium headless, external stylesheet requests blocked; individual OS clipboard permissions and other browser engines remain unverified.

## Important boundaries

This is an exploratory statistical + instructional instrument. The rhetorical labels are primarily rule-based; the program does not run an external LLM, verify semantics, or attribute style to an identifiable author. Copying a JSON bundle does not validate a downstream paraphrase.
