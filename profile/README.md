# Void DAW

A from-scratch, mobile-first Digital Audio Workstation for Android — not a wrapper around an existing engine, not a port of a desktop DAW, not a reskin.

> **Build the engine right the first time. Everything else is a plugin.**

The audio core is a hand-written real-time C++17 engine (Oboe-backed) built around a **topological DSP execution graph** — not a flat per-track loop. Plugins run through a custom binary ABI (`.void` format, currently ABI v5) with sample-accurate MIDI, sidechain bus routing, and state serialization for presets. The entire DAW canvas — including plugin UIs — is drawn through a renderer-independent draw-command system on a custom OpenGL ES 3.1+ pipeline; plugins never touch pixels directly. The Kotlin/Jetpack Compose layer only handles shell, menus, and non-realtime UI.

Source is closed — this page exists to show what's being built without exposing the codebase.

---

## Full arrangement view with live FX rack

Multi-track arrangement with waveform display, per-track mute/solo, and a live effects rack (VoidEQ Lite, VoidReverb) showing real-time engine stats — DSP time, XRuns, RAM, thermal state — right next to the controls.

![Arrangement view with FX rack](https://raw.githubusercontent.com/void-daw/.github/main/profile/images/arrangement-fx-rack.png)

## VoidEQ5 — full parametric equalizer

A 5-band parametric EQ plugin with a live spectrum analyzer, draggable frequency nodes, per-node freq/gain/width controls, and a real-time gain curve — rendering at 240fps, drawn entirely through the plugin draw-command ABI (no pixel buffers).

![VoidEQ5 parametric equalizer](https://raw.githubusercontent.com/void-daw/.github/main/profile/images/voideq5.png)

## Full drum arrangement

A complete beat built inside the DAW — kick, snare, hi-hats, 808, open hat, and a MIDI lane, each on its own color-coded track.

![Drum arrangement](https://raw.githubusercontent.com/void-daw/.github/main/profile/images/drum-arrangement.png)

## Plugin browser

Four working stock plugins (VoidReverb, VoidHyperdrive, VoidEQ5, VoidEQ Lite) across Reverb, Distortion, and Equalizer categories, each with its own visual identity in the rack.

![Plugin browser](https://raw.githubusercontent.com/void-daw/.github/main/profile/images/plugin-browser.png)

---

## What makes this different

- **Graph-based DSP execution, not a flat loop.** Every audio callback walks a topologically-sorted execution graph (Kahn's BFS, cycle-checked) of `AudioClip → Plugin → TrackBus → MasterBus` nodes. The graph is immutable after publish and atomically swapped when tracks/effects change, with deferred cleanup on a separate janitor thread — no locks on the audio thread.
- **Custom plugin ABI, not a JUCE/VST wrapper.** Currently ABI v5: sample-accurate MIDI events, sidechain bus declarations, parameter descriptors, and full state serialization for `.void` project save/load and preset export — all in a versioned binary contract, loaded via `dlopen`/`dlsym` at runtime.
- **Sandboxed plugin execution ("Void Shield").** Plugins run in an isolated process, communicating over futex-synchronized shared memory — this keeps the host safe from misbehaving plugins and keeps licensing boundaries clean between the closed-source host and any third-party plugin code.
- **Renderer-independent plugin UI.** Plugins emit draw commands (rects, paths, MSDF text, gradients, textured quads) rather than touching pixels — the host owns the entire GPU backend (OpenGL ES 3.1+), so plugin UIs stay portable and fast.
- **Dynamic latency tiers.** Runtime block size adapts to device capability: 128 frames (~2.7ms, Pro), 256 (~5.3ms, Standard), 512 (~10.7ms, Compatible), or 4096 for offline export — never hardcoded.
- **Verified on real hardware**, not just simulator: stable audio graph execution confirmed on-device (Samsung SM-E156B, Android 16) with zero drift over sustained playback.

## Ecosystem

- **void-daw** — the DAW host application (private)
- **void-juce-lab** — desktop JUCE plugin lab, where DSP gets validated on desktop before being ported to the mobile engine (private)
- **void-utils** — VoidHub (plugin distribution/discovery hub) and Voidmaker (DSP development workstation) (private)

More screenshots and detail coming as the project progresses.
