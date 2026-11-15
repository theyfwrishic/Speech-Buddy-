<div align="center">

# 🗣️ Speech Buddy

### AI-powered speech practice and therapy support platform

"Turning speech practice into accessible, personalized progress."

[![Built for Odoo Hackathon 2026](https://img.shields.io/badge/Odoo%20Hackathon-2026-714B67)](#hackathon)
[![HTML5](https://img.shields.io/badge/HTML5-single%20file-E34F26?logo=html5&logoColor=white)](index.html)
[![Vanilla JS](https://img.shields.io/badge/JavaScript-vanilla-F7DF1E?logo=javascript&logoColor=black)](index.html)
[![No build step](https://img.shields.io/badge/build-none-brightgreen)](#quick-start)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

[Quick start](#quick-start) · [Features](#features) · [How it works](#how-it-works) · [Architecture](docs/ARCHITECTURE.md) · [Pitch deck](docs/Speech-Buddy-Pitch-Deck.pptx)

<img src="docs/screenshots/articulation.png" alt="Speech Buddy articulation practice screen" width="520">

</div>

---

## About

Speech-related difficulties affect communication, confidence, education and employment. Most people in therapy depend on scheduled clinic visits, and there is very little structured, tracked practice happening at home between sessions.

Speech Buddy closes that gap. A clinician captures a baseline assessment, the patient gets a personalized practice pathway, and every spoken attempt becomes data that both the patient and the therapist can see.

This repository contains the **Speech Practice Assistant**: the patient-facing practice app. It is a single, dependency-free HTML file that runs entirely in the browser using the Web Speech API and the Web Audio API.

> **Important:** Speech Buddy supports therapy. It is not a diagnostic tool and does not replace a licensed speech-language pathologist.

## Features

The app has four practice modes that map to common speech and language difficulty categories, plus a shared progress tracker.

| Mode | What it targets | What you can do |
|---|---|---|
| 🎯 **Articulation** | Producing specific sounds such as /r/, /s/, /z/ | Pick a target sound or blend (/r/, /s/, /z/, /l/, /bl/, /br/, /cl/, /cr/, /fl/, /st/, /str/), see the phonemes, watch an animated mouth and tongue diagram, hear the word, then say it into the mic. Common substitutions (for example /w/ for /r/) are flagged. |
| 🔤 **Phonological** | Organizing sounds into correct patterns | Practice final sounds, blends, back sounds and airflow sounds with minimal pairs ("seat" vs "see"). Includes a "Which word did you hear?" listening game and free-sentence practice. |
| 🌊 **Fluency** | Flow and rhythm of speech (e.g. stuttering) | Guided 4-in / 6-out breathing, then paced reading at 60, 90 or 120 words per minute with a word-by-word highlight. Feedback covers word accuracy, speaking rate and repetitions. |
| 🎙️ **Voice** | Pitch, loudness and quality | Live loudness meter with a green target zone, plus a steady-pitch exercise with real-time Hz readout from autocorrelation pitch detection. |
| 📈 **Weekly progress** | Motivation and therapist review | Correct, wrong and accuracy totals plus a 6-week stacked bar chart. Stored locally in the browser. |

Other details:

- Use your own sentences in every mode
- Slow and normal playback speeds
- Automatic light and dark theme
- Mobile friendly, with safe-area support for notched phones
- No accounts, no server, no tracking. Practice data stays in `localStorage`

### Screenshots

| Articulation | Phonological | Fluency | Voice |
|---|---|---|---|
| <img src="docs/screenshots/articulation.png" width="200"> | <img src="docs/screenshots/phonological.png" width="200"> | <img src="docs/screenshots/fluency.png" width="200"> | <img src="docs/screenshots/voice.png" width="200"> |

<img src="docs/screenshots/progress.png" alt="Weekly progress chart" width="480">

## Quick start

No install and no build step.

```bash
git clone https://github.com/<your-username>/speech-buddy.git
cd speech-buddy
```

Then pick one:

**Option 1: open the file.** Double-click `index.html`. Hearing words, mouth animation and progress work right away. Some browsers block the microphone on `file://` pages, so use option 2 for mic practice.

**Option 2: run a local server (recommended).**

```bash
# Python 3
python3 -m http.server 8000

# or Node.js
npx serve .
```

Open <http://localhost:8000> and allow microphone access when asked.

**Option 3: GitHub Pages.** This repo includes a workflow in `.github/workflows/pages.yml`. Push to `main`, then go to **Settings → Pages → Source: GitHub Actions**. Your app will be live at `https://<your-username>.github.io/speech-buddy/`.

### Browser support

| Feature | Chrome / Edge | Safari | Firefox |
|---|---|---|---|
| Hear it (text-to-speech) | ✅ | ✅ | ✅ |
| Mouth and tongue animation | ✅ | ✅ | ✅ |
| Mic practice (speech recognition) | ✅ | ⚠️ Partial | ❌ |
| Voice meter and pitch | ✅ | ✅ | ✅ |
| Progress tracking | ✅ | ✅ | ✅ |

Speech recognition uses the browser's built-in `SpeechRecognition` API. Chrome and Edge send audio to their speech service for transcription, so an internet connection is needed for mic practice. The microphone requires HTTPS or `localhost`.

## How it works

```
Patient speaks
   │
   ▼
Speech recognition (browser Web Speech API → transcript)
   │
   ▼
Speech analysis (word match, substitution patterns, rate in WPM, repetitions,
                 loudness RMS, pitch via autocorrelation)
   │
   ▼
Performance data (attempt log: timestamp, correct/wrong, target)
   │
   ▼
Feedback + weekly progress  ──►  (planned) Odoo / PostgreSQL + AI personalization
```

**Worked example.** Target sound /r/, prompt "The rabbit is running." If the patient says "The wabbit is wunning", the transcript does not match the target, and the app recognizes the /w/-for-/r/ substitution and tells the user that tongue placement is the key.

Key techniques inside `index.html`:

- **Phonemes:** a small hand-built IPA dictionary, with a digraph-aware fallback for any other word
- **Mouth animation:** each phoneme maps to one of six lip shapes (closed, mid, wide, open, round, narrow) and one of six tongue positions, drawn as SVG paths
- **Substitution detection:** checks the transcript against common error rewrites (r→w, s→th, z→d, l→w, and reduced blends like "top" for "stop")
- **Fluency scoring:** measures speaking time from `onspeechstart` / `onspeechend`, then computes WPM and flags extra words as possible repetitions
- **Loudness:** RMS level in dB, normalized to 0 to 1, with a 0.5 to 0.8 target zone
- **Pitch:** time-domain autocorrelation over a 70 to 500 Hz range
- **Storage:** attempts saved under the `speechPractice.attempts.v1` key in `localStorage`, grouped by weeks starting Monday

See [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) for the full design, including the planned Odoo platform.

## Project structure

```
speech-buddy/
├── index.html                       # The complete app (HTML + CSS + JS, no dependencies)
├── docs/
│   ├── ARCHITECTURE.md              # System design and Odoo integration plan
│   ├── Speech-Buddy-Pitch-Deck.pptx # Odoo Hackathon 2026 presentation
│   └── screenshots/                 # Images used in this README
├── .github/
│   ├── workflows/pages.yml          # Deploy to GitHub Pages
│   ├── ISSUE_TEMPLATE/              # Bug report and feature request templates
│   └── pull_request_template.md
├── CONTRIBUTING.md
├── CODE_OF_CONDUCT.md
├── LICENSE
└── .gitignore
```

## Customizing

Everything lives in plain JavaScript objects near the top of each section in `index.html`:

| To change | Edit |
|---|---|
| Articulation target sounds and words | `SOUNDS` |
| Preset words with exact IPA | `WORDS` |
| Phonological patterns and minimal pairs | `PHO` (and matching `PRULE`) |
| Paced reading sentences | `FL` |
| Voice exercises | `VM` |
| Playback speed and volume | `SPEED_DELAY`, `SPEED_RATE`, `HEAR_VOLUME` |
| Recognition language | `rec.lang = 'en-US'` |

## Roadmap

The full Speech Buddy platform from our hackathon pitch:

- [x] Patient practice app with articulation, phonological, fluency and voice modes
- [x] Instant feedback and weekly progress tracking
- [ ] Odoo module (`my_elearning`) with patient pathways built on Odoo eLearning
- [ ] Clinician assessment records and patient onboarding
- [ ] Therapist dashboard: pathway completion, practice frequency, attempt logs, challenge areas
- [ ] Attempt history stored in PostgreSQL with role-based access for clinicians and patients
- [ ] AI personalization that adapts exercises to each patient's performance
- [ ] Deeper pronunciation scoring (e.g. OpenPronounce)
- [ ] Motor speech and language modules
- [ ] More languages

## Hackathon

Built for **Odoo Hackathon 2026** (30 September 2026) at **Heriot-Watt University Dubai**.

**Team Stack Underflow:** Rishi · Ojasva · Abdur · Jenoah

The pitch deck is in [docs/Speech-Buddy-Pitch-Deck.pptx](docs/Speech-Buddy-Pitch-Deck.pptx).

## Privacy

- No backend, no analytics, no accounts
- Progress stays in your browser's `localStorage`. Use **Reset stats** to clear it
- Audio for the Voice mode is analyzed locally and never recorded or uploaded
- Mic practice uses your browser's speech recognition service, which may process audio on the browser vendor's servers

## Contributing

Contributions are welcome. Read [CONTRIBUTING.md](CONTRIBUTING.md) to get started.

## License

Released under the [MIT License](LICENSE).

## Disclaimer

Speech Buddy is an educational practice tool. It does not provide medical advice, diagnosis or treatment. Feedback and scores are practice signals, not clinical measurements. Always work with a qualified speech-language pathologist.
