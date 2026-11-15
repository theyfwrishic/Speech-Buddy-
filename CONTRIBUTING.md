# Contributing to Speech Buddy

Thanks for helping make speech practice more accessible.

## Getting started

1. Fork the repository and clone your fork
2. Create a branch: `git checkout -b feature/my-change`
3. Run a local server: `python3 -m http.server 8000`
4. Open <http://localhost:8000> in Chrome or Edge to test mic features

## Guidelines

- Keep the app dependency-free and in a single `index.html` unless there is a strong reason to split it
- Test in light and dark mode and on a narrow (mobile) screen
- Test mic features in Chrome or Edge, and check that unsupported browsers show a clear message
- Keep feedback text encouraging and plain. Avoid clinical or diagnostic claims
- Do not add analytics, tracking or any code that sends practice data off the device without clear user consent

## Adding content

- New articulation sounds: add to `SOUNDS`
- New phonological patterns: add to `PHO` with matching `[target, error]` pairs, plus a rule in `PRULE`
- New paced-reading sentences: add to `FL`

## Commit messages

Use short, descriptive messages, for example `Add /th/ articulation set` or `Fix pitch meter on Safari`.

## Pull requests

Describe what changed, why, and how you tested it. Add screenshots for UI changes.
