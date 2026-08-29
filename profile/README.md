# Void DAW

A from-scratch, mobile-first Digital Audio Workstation for Android — not a wrapper around an existing engine, not a port of a desktop DAW, not a reskin.

> **Build the engine right the first time. Everything else is a plugin.**

The audio core is a hand-written real-time C++17 engine (Oboe-backed), the entire DAW canvas is drawn by a custom OpenGL ES renderer speaking its own immediate-mode draw-command language, and plugins run through a custom binary plugin ABI (`.void` format) loaded via `dlopen` in a sandboxed process. The Kotlin/Jetpack Compose layer only handles shell, menus, and non-realtime UI.

Source is closed — this page exists to show what's being built without exposing the codebase.

---

## Full arrangement view with live FX rack

Multi-track arrangement with waveform display, per-track mute/solo, and a live effects rack (VoidEQ Lite, VoidReverb) showing real-time engine stats — DSP time, XRuns, RAM, thermal state — right next to the controls.

![Arrangement view with FX rack](https://raw.githubusercontent.com/void-daw/.github/main/profile/images/arrangement-fx-rack.png)

## VoidEQ5 — full parametric equalizer

A 5-band parametric EQ plugin with a live spectrum analyzer, draggable frequency nodes, per-node freq/gain/width controls, and a real-time gain curve — rendering at 240fps.

![VoidEQ5 parametric equalizer](https://raw.githubusercontent.com/void-daw/.github/main/profile/images/voideq5.png)

## Full drum arrangement

A complete beat built inside the DAW — kick, snare, hi-hats, 808, open hat, and a MIDI lane, each on its own color-coded track.

![Drum arrangement](https://raw.githubusercontent.com/void-daw/.github/main/profile/images/drum-arrangement.png)

## Plugin browser

Four working stock plugins (VoidReverb, VoidHyperdrive, VoidEQ5, VoidEQ Lite) across Reverb, Distortion, and Equalizer categories, each with its own visual identity in the rack.

![Plugin browser](https://raw.githubusercontent.com/void-daw/.github/main/profile/images/plugin-browser.png)

---

## What makes this different

- **Custom plugin ABI, not a JUCE/VST wrapper.** Every `.void` plugin exports 5 C entry points (`void_create`, `void_process`, `void_destroy`, `void_get_params`, `void_set_param`) against a versioned ABI contract, loaded via `dlopen`/`dlsym` at runtime.
- **Sandboxed plugin execution ("Void Shield").** Plugins run in an isolated process, communicating over standard IPC/shared memory — this keeps the host safe from misbehaving plugins and keeps licensing boundaries clean between the closed-source host and any third-party plugin code.
- **Hand-written audio engine**, not a wrapper around an existing DAW engine — built on Google Oboe for low-latency Android audio, with an automatic latency-tier system (Pro / Standard / Compatible) based on device capability.
- **Custom OpenGL ES renderer** for the entire DAW canvas — grid, waveforms, playhead, plugin UIs — not standard Android views.

## Ecosystem

- **void-daw** — the DAW host application (private)
- **void-juce-lab** — desktop JUCE plugin lab, where DSP gets validated on desktop before being ported to the mobile engine (private)
- **void-utils** — VoidHub (plugin distribution/discovery hub) and Voidmaker (DSP development workstation) (private)

More screenshots and detail coming as the project progresses.
