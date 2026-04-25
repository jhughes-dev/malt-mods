# Testing

Rosetta registers a set of **client-side chat commands** under `/rosetta` so you can exercise your cards without relaunching the game or editing persisted state by hand. They run on both Fabric and NeoForge.

All commands are client-only. Typing them at the chat prompt does not send anything to the server; they operate on the local Rosetta registry and persistence.

## `/rosetta list`

Lists every registered card, marking which ones the current player has already acknowledged.

```
/rosetta list

[rosetta] Registered cards (3):
  - mymod:welcome [seen]
  - mymod:tutorial/builds
  - drophighlighter:welcome [seen]
```

Use this to confirm your card is actually registered - if it doesn't show up here, your `Rosetta.register(...)` call didn't run (wrong entry point, hit an exception, or the class was never loaded).

## `/rosetta show <id>`

Immediately opens the card with the given identifier, regardless of whether the player has acknowledged it. Tab-completion suggests all registered card ids.

```
/rosetta show mymod:welcome
```

Equivalent to `Rosetta.show(id)` from code, but run from a running session - handy for iterating on the card's text, layout, or buttons without closing and reopening the world.

If the id isn't registered, the command prints an error and does nothing.

## `/rosetta reset [id]`

Clears persisted "acknowledged" state so the card(s) will fire again on the next trigger.

### Reset a single card

```
/rosetta reset mymod:welcome
```

The card's entry is removed from persistence. Next time its trigger fires (e.g., joining a world for `BuiltinTriggers.firstLoad()`), the card will display.

### Reset everything

```
/rosetta reset
```

No argument - forgets every acknowledgement for every card, across every mod using Rosetta. Useful for simulating a fresh install.

## `/rosetta replay`

Opens the Rosetta replay screen - the same UI the player uses to browse previously acknowledged cards. Useful for verifying that your card renders correctly in the replay context (replay uses the same renderer but without the trigger / suppression logic).

```
/rosetta replay
```

## Typical iteration workflow

When tweaking an onboarding card:

1. Launch the game, load a world.
2. `/rosetta list` - confirm your card is registered.
3. `/rosetta show mymod:welcome` - see the current render.
4. Edit the card code, rebuild with hot-reload (or restart).
5. `/rosetta reset mymod:welcome` - clear the acknowledged flag.
6. Trigger the card naturally (e.g., reload the world for `firstLoad`) to verify the trigger path end-to-end.

## Version bump testing

If you're testing the `.version(n)` re-show behavior:

1. Register the card at `.version(1)` and dismiss it.
2. `/rosetta list` - confirm `[seen]`.
3. Bump the code to `.version(2)` and rebuild.
4. Trigger the card - it should fire again because the player's persisted version (1) is less than the current (2).
5. `/rosetta list` - should still show `[seen]` after acknowledging v2.

`/rosetta reset` is the fast way to reset between iterations without waiting for version-bump logic.

## Caveats

- Commands are only available once Rosetta has finished client init. If you try them from the main menu before loading a world, they won't be registered yet.
- The `show` command uses `Rosetta.defer(...)` so the card opens after the chat screen closes. Expect a one-frame delay.
- On servers, these commands do nothing useful - Rosetta is client-only and the command tree is registered on the client command dispatcher.
