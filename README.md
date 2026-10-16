# Corpus → Co-Domain

**Corpus → Co-Domain** is a local-first workbench for exploring writing style in Indonesian, English, or mixed-language text. It turns corpus evidence into a structured style profile and copyable paraphrase prompt presets.

The project separates measured features from interpretations. It treats frequency and graph relationships as evidence to inspect, not as proof of meaning or authorial intent.

## Try the app

Open [`index.html`](index.html) in a modern browser. The app runs without a build step, account, or API key.

1. Paste text into the corpus box, or load a `.txt` or `.md` file (up to 1 MB).
2. Choose the analysis options and run the analysis.
3. Inspect the lexical, sentence, paragraph, n-gram, and graph views, including KWIC context.
4. Review or override the evidence-aware style classifications.
5. Choose a preset, optionally enter text to paraphrase, then copy the generated prompt or export the analysis as JSON.

Entering text to paraphrase only inserts it into the prompt. The app does not call a language model or execute the paraphrase.

## What it analyzes

- Token, sentence, and paragraph structure, with original token positions.
- Bigram and trigram frequencies, paragraph dispersion, normalized rates, and frequency bands.
- Lexical variety, word length, sentence length, punctuation, and selected discourse markers.
- Paragraph positions, concordance excerpts, and three inspectable graph views.
- Heuristic style classifications with evidence status, excerpts, and manual overrides.
- Four prompt preset approaches: high-fidelity style, rhetorical structure, lexical and syntactic adaptation, and balanced semantic paraphrase.

The app includes adjustable stopword handling, including options to preserve contrast markers and negation. Its built-in fixture checks can be run from the **Run core checks** control.

## Privacy and limitations

Analysis runs in the browser. The app does not upload or persist corpus text and does not use an external API. Exported JSON may contain excerpts and corpus-derived statistics, so review it before sharing.

Language detection, tokenization, classifications, and graph layers use deterministic heuristics. They are not a POS tagger, dependency parser, semantic similarity model, or reliable detector of an author's identity, personality, era, or intent. Results are more useful as an evidence-backed inspection aid than as definitive labels. Very short or narrow corpora may not support meaningful inferences.

## Project specification

See [`Prototype/notes.md`](Prototype/notes.md) for the detailed requirements, measurement definitions, and methodological constraints.

## License

No license has been specified yet. All rights are reserved unless a license is added to this repository.
