# API Reference

The `io.github.malteas.rosetta.Rosetta` class is the entry point for all integration. All methods are static.

## Registry

### `Rosetta.register(RosettaOnboardCard card)`

Adds a card to the registry and invokes its trigger's `register()`. Idempotent for the same `id` - re-registering replaces the previous card with the same id (the trigger is re-registered, so callers should avoid re-registering during a single client session).

### `Rosetta.get(Identifier id) → Optional<RosettaOnboardCard>`

Looks up a registered card by id. Returns `Optional.empty()` if not registered.

### `Rosetta.registered() → Map<Identifier, RosettaOnboardCard>`

Unmodifiable view of all registered cards. Useful for building debug UIs or replay menus.

## Display

### `Rosetta.show(Identifier id)`

Immediately opens the card's screen, regardless of whether it has been acknowledged. No-op if the id isn't registered.

### `Rosetta.showIfUnseen(Identifier id)`

Enqueues the card for display only if it hasn't been acknowledged at its current `version`. Equivalent to triggering it manually. Respects [suppression rules](concepts.md#suppression-rules) - the card is held in the pending queue until `isSafeToShow()` becomes true.

### `Rosetta.openReplayScreen()`

Opens the Rosetta replay screen, where the player can browse and re-read previously acknowledged cards filtered by category.

## Persistence

### `Rosetta.isSeen(Identifier id) → boolean`

`true` if the player has acknowledged this card at its current `version`. `false` if not seen, or if the card isn't registered.

### `Rosetta.markSeen(Identifier id)`

Marks the card as acknowledged at its current version. Use during migration from a pre-Rosetta onboarding flow:

```java
if (LegacyConfig.hasShownWelcome()) {
    Rosetta.markSeen(welcomeId);
}
```

### `Rosetta.forget(Identifier id)`

Removes any persisted "acknowledged" state for the card so it can fire again. Mostly useful for testing.

### `Rosetta.forgetAll()`

Forgets every persisted acknowledgement. Mostly useful for testing.

## Notifications

Notifications are small HUD pop-ups that don't interrupt the player. See `RosettaNotification` builder for content options (icon, color, optional show-action button).

### `Rosetta.notify(RosettaNotification notification)`

Adds a notification to the HUD inbox. Plays `UI_TOAST_IN` unless the notification was built with `.noSound()`.

```java
Rosetta.notify(new RosettaNotification(
        Identifier.fromNamespaceAndPath("mymod", "biome_found"),
        Component.translatable("notify.mymod.biome_found"))
    .icon(Identifier.fromNamespaceAndPath("mymod", "textures/gui/biome.png"))
    .onShow(Component.translatable("notify.mymod.biome_found.show"),
            () -> Rosetta.show(biomeCardId))
);
```

The `onShow` action becomes a "Show" button on the notification - typical pattern: open a related card.

### `Rosetta.dismissNotification(Identifier id)`

Removes a notification from the inbox.

### `Rosetta.pendingNotifications() → List<RosettaNotification>`

Unmodifiable view of currently-displayed notifications.

### `Rosetta.openNotificationScreen()`

Opens the full-screen notifications inbox.

## Lifecycle / Internal

These are exposed for advanced cases and are usually called by Rosetta itself. Most mods don't need them.

### `Rosetta.isSafeToShow() → boolean`

`true` if the current player state allows interrupting with a card. See [Concepts → Suppression rules](concepts.md#suppression-rules).

### `Rosetta.defer(Runnable action)`

Queues a runnable to execute the next time `mc.screen == null`. Useful when you want to open a screen but can't because another screen is already active.

### `Rosetta.drainPending()`

Called by Rosetta's tick handler. Pulls queued card ids and presents them in the cycling card screen if `isSafeToShow()` is true. You should not normally call this directly.
