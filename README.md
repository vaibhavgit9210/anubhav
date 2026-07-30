# anubhav (अनुभव)

Realtime music visualizer — play your music anywhere (Spotify, YouTube, another app, speakers in the room) and watch it breathe. No music API, no account, no build step: the page just *listens* and turns what it hears into light.

**Live:** https://vaibhavkumar.is-a.dev/anubhav/

## Audio sources

| Source | How | Where it works |
|---|---|---|
| 🎤 Microphone | `getUserMedia` with voice-processing (echo cancellation / noise suppression / AGC) disabled so music isn't eaten | Every device & browser — phones included |
| 🖥️ Tab / screen audio | `getDisplayMedia({audio})` — pick the playing tab, tick "also share tab audio". Clean digital signal, perfect for headphones | Desktop Chrome / Brave / Edge only (button hidden elsewhere; graceful message if the share has no audio) |

Nothing is recorded and nothing leaves the page — the stream feeds a Web Audio `AnalyserNode` and dies there.

## Six styles (cycle with the ◐ pill, keys 1–6, or ←/→)

1. **bloom** — 12 chroma petals (one per pitch class, C at top), bass-breathing orb, waveform ring, beat particle bursts; dominant pitch class steers the page hue
2. **scope** — Takens phase portrait: `x(t)` vs `x(t−τ)`, the signal folded onto itself; phosphor persistence, B&W
3. **chladni** — real Chladni plate physics: 2,600 sand grains descend the gradient of `cos(mπx)cos(nπy) − cos(nπx)cos(mπy)` and pile on nodal lines; (m, n) track the two strongest spectral peaks; silence lets the pattern dissolve
4. **fourier** — the live waveform DFT'd into 22 epicycles chained tip-to-tip, drawing itself (complex-EMA'd so phases don't jitter)
5. **helix** — pitch spiral, one turn per octave: same note in every octave lands on one ray with one colour
6. **manifold** — (spectral centroid, RMS, flux) traced as a wireframe trajectory through R³, axes auto-normalised to the recent range, numeric vertex labels — same idea as the vocal manifold on anime-voice-fights

Every style carries a scientific readout (top-left): RMS dBFS, spectral centroid, flux, detected key, live BPM estimate (median of recent beat gaps), gate level.

**Pause behaviour:** an absolute-loudness gate (fast attack, ~1 s release) scales every visual — chromagram normalisation included — so pausing the music collapses the page to black instead of freezing on the noise floor.

## Analysis (all in `analyse()`)

RMS, bass/mid/treble bands, beat detection (bass vs ~0.7 s rolling average, gated), 12-bin chromagram (55 Hz–4.2 kHz folded into pitch classes), spectral centroid, spectral flux, two strongest spectral peaks → Chladni modes, beat-gap median → BPM.

## Screenshot hooks (headless Chrome)

rAF never fires under `--virtual-time-budget`, so the hooks render synchronously without it:

- `#shot=home` — landing screen
- `#shot=viz` — visualizer driven by ~3.5 s of synthetic music (120 bpm kick + A-minor chord + drifting spectral tail), deterministic (seeded RNG)
- `#shot=viz&style=<key>` — specific style: `bloom|scope|chladni|fourier|helix|manifold`

A late headless resize event wipes the canvas after the script runs — `resize()` skips no-op resizes and re-renders the shot frame, so screenshots survive it.

## Deploy

Repo `vaibhavgit9210/anubhav`, GitHub Pages from `gh-pages`:

```bash
git push origin main main:gh-pages
```

Carries the standard site-analytics beacon (see `../site-analytics/README.md`).
