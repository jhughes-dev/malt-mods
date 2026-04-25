# Concepts

Background on the design choices that affect how cards behave.

## Identifiers

Every card is keyed by a `net.minecraft.resources.Identifier` (a.k.a. `ResourceLocation` on older versions). The convention:

```
<your-modid>:<card-name>
```

The namespace **must** be your mod's ID. The `rosetta:*` namespace is reserved for Rosetta itself.

```java
Identifier.fromNamespaceAndPath("mymod", "welcome")          // mymod:welcome
Identifier.fromNamespaceAndPath("mymod", "tutorial/builds")  // mymod:tutorial/builds
```

Path segments may contain `/` for organization but it's purely cosmetic - Rosetta doesn't infer hierarchy from the path.

## Versioning

Each card carries an integer `version` (default `1`). Persistence stores the version a player acknowledged.

There are two patterns for delivering new content to players who already saw a card:

### Bump the version on an existing card

```java
.version(2)
```

Players who saw `version: 1` will see the card again on next load. Use this when the underlying feature changed and the existing tutorial needs correcting.

### Register a new card with a new ID

```java
Identifier.fromNamespaceAndPath("mymod", "whats_new_v2")
```

Use this for "what's new in this release" notes. Every major version of your mod ships a fresh card; players (including returning ones) see it because the ID is new.

These patterns coexist. Most mods use the new-ID pattern for changelogs and the version-bump pattern for tutorial corrections.

## Persistence

Acknowledged cards are persisted across game sessions in the player's Minecraft install. State is per-install - there is no per-world, per-server, or cloud sync.

The persistence file lives in the standard Minecraft config / data directory; the exact path is an implementation detail and may change. Use Rosetta's APIs to get at the data instead of directly reading the config.

API for managing persistence (see [API Reference](api-reference.md)):

- `Rosetta.isSeen(id)` - has this player acknowledged this card-version?
- `Rosetta.markSeen(id)` - manually mark a card as already-acknowledged. Useful when migrating to Rosetta from an existing onboarding system: detect your old "seen" flag at startup and call `markSeen` to suppress the card for existing users.
- `Rosetta.forget(id)` / `Rosetta.forgetAll()` - re-show one card or all cards. Mostly useful for testing.

## Suppression rules

Even when a card is queued, Rosetta will not display it while:

- The player is dead or dying
- The player is sleeping
- The player is gliding (elytra)
- The player is a spectator
- The player is taking damage (`hurtTime > 0`)
- A screen is already open
- The player or world is null

Queued cards are held until `isSafeToShow()` returns true, then drained.

If you need to check this state from your own code, call `Rosetta.isSafeToShow()`.

## Categories

Cards are tagged with one of:

| Category | Meaning |
|---|---|
| `TUTORIAL` | Teaches how to use a feature |
| `TIP` | Small contextual hint |
| `WARNING` | Important behavior the player should know about |
| `NEWS` | What's new / changelog |
| `CREDITS` | Author info, links, support |

Categories drive the replay screen's grouping. Pick the one that matches the card's intent - players use them to filter the replay UI.

## Notifications vs. cards

Cards are full-screen modals. **Notifications** (`RosettaNotification`) are small HUD pop-ups that don't interrupt the player. Use a notification when:

- The player is doing something else and you don't want to block them
- The information is non-blocking ("you found a new biome")
- The cue should be peripheral, not modal

Notifications and cards can coexist - fire a notification immediately and queue a card to explain later. See [API Reference → notify](api-reference.md#notifications).
