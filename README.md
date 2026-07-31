# Artificial Eye 👁️

An AI-powered vision assistant for blind and low-vision users. Point your phone's camera at anything, tap the screen, and the app describes it out loud — scenes, text and signs, currency, and road/safety hazards — powered by Google's Gemini API.

**[Demo video](https://youtube.com/shorts/SwK1bqKV0jk?si=ilW9HX3PKuQqmiVB)**

## What it does

- **Scene description** — "What's in front of me?"
- **Read text aloud** — signs, labels, documents
- **Currency detection** — identifies notes/coins and denominations
- **Road & sign awareness** — traffic signals, crosswalks, hazards, prioritized by urgency

Tap anywhere, hear a spoken answer within a couple of seconds. No screen-reading required to get the result — the output is audio.

## How it works

1. The camera captures a frame on tap
2. The frame + a mode-specific prompt are sent to Google's **Gemini API** (`gemini-3.1-flash-lite`)
3. Gemini's response is spoken aloud using the device's native text-to-speech engine

## Tech stack

| Layer | Tool |
|---|---|
| App logic (UI, camera, capture) | Plain HTML / CSS / JavaScript — no framework |
| AI | Google Gemini API via the official `@google/genai` SDK |
| Text-to-speech | Native Android TTS via a Capacitor plugin (Web Speech API fallback for browser testing) |
| Native Android wrapper | [Capacitor](https://capacitorjs.com/) |
| Build & signing | Android Studio + Gradle |
| API key storage | Browser `localStorage` — entered once via an in-app Settings screen, never hardcoded |
| Version control | Git + GitHub |

**Why this architecture:** built as a web app first, then wrapped natively with Capacitor — this keeps the core logic in simple, portable JavaScript while still getting real native camera and text-to-speech access, and a genuinely installable `.apk`. No backend server; the app talks directly to Gemini's API.

## Setup

1. Clone the repo
2. Get a free Gemini API key at [aistudio.google.com/apikey](https://aistudio.google.com/apikey)
3. Run locally:
   ```bash
   python3 -m http.server 8000
   ```
   Open `http://localhost:8000`, tap the gear icon, paste your API key
4. To build the Android app:
   ```bash
   npm install
   npx cap sync
   npx cap open android
   ```
   Then build/run from Android Studio

## Real challenges solved

- **Live API authentication change** — mid-project, Google rolled out a new API key format that broke direct REST calls. Diagnosed via terminal `curl` testing, isolated the exact failure mode, and resolved it by switching to Google's official SDK.
- **Native platform gaps** — the Web Speech API (used for TTS in-browser) doesn't exist inside Android's WebView. Solved with a Capacitor plugin bridging to the device's real native TTS engine, with a browser fallback for local testing.
- **Gradle dependency conflict** — duplicate Kotlin stdlib classes from mismatched plugin versions; resolved by excluding the redundant legacy modules in `build.gradle`.

## Known limitations

- API key is stored client-side (fine for a personal/demo project; a production version would proxy requests through a small backend)
- Response latency is ~1-3 seconds per capture (network + model inference)
- Currency and sign-reading accuracy depends on lighting and the vision model's limits

## Roadmap

The current UI still relies on visible mode-selector buttons — not ideal for the actual target user. Next phase:
- Redesign the interface to be fully non-visual: the whole screen as a single tap target, voice-command mode switching instead of visible buttons
- Explore Android's Accessibility Shortcut system so the app can be launched hands-free, even from the lock screen, without a native Kotlin Accessibility Service

## License

This project was built as a personal/academic project. Feel free to explore the code for learning purposes.
