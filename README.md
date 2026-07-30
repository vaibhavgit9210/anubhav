# anubhav (अनुभव)

Realtime music visualizer — play your music anywhere (Spotify, YouTube, another app, speakers in the room) and watch it breathe. No music API, no account, no build step: the page just *listens* and turns what it hears into light.

**Live:** https://vaibhavkumar.is-a.dev/anubhav/

## Audio sources

| Source | How | Where it works |
|---|---|---|
| 🎤 Microphone | `getUserMedia` with voice-processing (echo cancellation / noise suppression / AGC) disabled so music isn't eaten | Every device & browser — phones included |
| 🖥️ Tab / screen audio | `getDisplayMedia({audio})` — pick the playing tab, tick "also share tab audio". Clean digital signal, perfect for headphones | Desktop Chrome / Brave / Edge only (button hidden elsewhere; graceful message if the share has no audio) |

Nothing is recorded and nothing leaves the page — the stream feeds a Web Audio `AnalyserNode` and dies there.

## What drives the visuals

- **RMS amplitude** → waveform-ring thickness/brightness
- **Bass / mid / treble bands** → core orb breathing, ring jitter
- **Beat detection** (bass energy vs ~0.7 s rolling average) → pulse flash + particle bursts
- **12-bin chromagram** (FFT bins folded into pitch classes, 55 Hz–4.2 kHz) → the 12 petals around the orb, one per note, C at top; the dominant pitch class steers the whole page's hue. Real chord *naming* is out of scope — this gets the "colours follow the harmony" feel without pretending to be a transcriber.

## Screenshot hooks (headless Chrome)

rAF never fires under `--virtual-time-budget`, so the hooks render synchronously without it:

- `#shot=home` — landing screen
- `#shot=viz` — visualizer driven by ~2.5 s of synthetic music (120 bpm kick + A-minor chord), deterministic (seeded RNG)

A late headless resize event wipes the canvas after the script runs — `resize()` skips no-op resizes and re-renders the shot frame, so screenshots survive it.

## Deploy

Repo `vaibhavgit9210/anubhav`, GitHub Pages from `gh-pages`:

```bash
git push origin main main:gh-pages
```

Carries the standard site-analytics beacon (see `../site-analytics/README.md`).
