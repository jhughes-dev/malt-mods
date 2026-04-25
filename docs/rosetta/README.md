# Rosetta - Mod Author Documentation

Rosetta is a client-side library mod for Minecraft (Fabric & NeoForge) that gives mod authors a unified system for first-launch tutorials, hint pop-ups, and changelog notes. Other mods declare their content; Rosetta handles registration, display, persistence, replay, and combat-aware suppression.

## Why should you use Rosetta over building your own onboarding solution?

I had two mods that both needed onboarding, and they both popped up at the same time. One rendered partially over the other. Rosetta manages all the card displays so that only one shows up at a time. It also provides you with a lot of convenience for building common patterns, while still allowing you to implement whatever you need with custom renderer.

This documentation is for **mod authors integrating Rosetta**. For player-facing description, see the Modrinth or CurseForge project page.

## Table of Contents

- [Getting Started](getting-started.md) - Add Rosetta to your build, register your first card, run the game.
- [Cards](cards.md) - `SimpleCard` builder API: titles, text, separators, buttons, icons, backgrounds, sizing.
- [Triggers](triggers.md) - When cards fire: `BuiltinTriggers.firstLoad`, custom trigger lambdas, and the `RosettaTrigger` interface.
- [Concepts](concepts.md) - Identifiers, versioning, persistence, suppression rules, `CardCategory`.
- [API Reference](api-reference.md) - Full `Rosetta` class methods: registry, show/replay, notifications, `defer`, `isSafeToShow`.
- [Examples](examples.md) - Full integration walkthrough using Drop Highlighter as a worked example.
- [Testing](testing.md) - In-game `/rosetta` commands for listing, showing, and resetting cards while iterating.

## Supported targets

| | Fabric | NeoForge |
|---|---|---|
| 1.21.1 – 1.21.11 | ✅ | ✅ |
| 26.1, 26.1.1, 26.1.2 | ✅ | ✅ |

Java 21 (1.21.x) / Java 25 (26.x). Client-only - Rosetta does not need to be installed on a server, and cannot send messages from server to client.

## Maven

- Maven: `https://malteas-games.github.io/maven/`
- Group / Artifact: `io.github.malteas.rosetta:rosetta-fabric` or `rosetta-neoforge`
- Version format: `<rosetta-version>+<minecraft-version>`, e.g. `0.1.1+1.21.11`
