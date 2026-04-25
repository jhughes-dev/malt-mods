# Triggers

A **trigger** decides when a registered card fires (i.e., enters the queue to be shown to the player). Cards never display "automatically" - they always require a trigger.

## The `RosettaTrigger` interface

```java
public interface RosettaTrigger {
    void register(TriggerContext ctx);
}
```

When you `Rosetta.register(card)` a card with a trigger, the trigger's `register` method is called once. It receives a `TriggerContext` whose `fire()` method enqueues the card for display.

A trigger typically subscribes to an event source and calls `ctx.fire()` when the event happens.

## `BuiltinTriggers.firstLoad()`

Fires once when the player joins a world. The most common trigger for first-launch onboarding.

```java
import io.github.malteas.rosetta.trigger.BuiltinTriggers;

Rosetta.register(new SimpleCard(id, title, category, BuiltinTriggers.firstLoad())
    .addText(welcomeText)
);
```

Behavior:

- **Fabric**: subscribes to `ClientPlayConnectionEvents.JOIN`.
- **NeoForge**: subscribes to `ClientPlayerNetworkEvent.LoggingIn`.

Rosetta's persistence layer ensures cards already acknowledged don't fire again - `firstLoad` calls `ctx.fire()` every join, but `Rosetta.showIfUnseen(id)` (used internally) only displays unseen cards.

## `BuiltinTriggers.onEvent(Consumer<Runnable> registrar)`

A helper for adapting your own event source. You provide a registrar that takes a `Runnable` (the "fire this card" action) and registers it as a callback on whatever event you want.

```java
RosettaTrigger pickedUpFirstNugget = BuiltinTriggers.onEvent(fire ->
    MyMod.NUGGET_PICKUP_EVENT.register(player -> fire.run())
);
```

This indirection is useful when:
- Your event source is a Fabric API event, NeoForge event bus listener, or a custom event you publish.
- You want to separate "what triggers this card" from "what the card is" without wrapping the trigger in a class.

## Writing a custom trigger class

When the registration logic is non-trivial - for example, you need to filter events or fire on the first occurrence only - implement `RosettaTrigger` directly.

```java
public class FirstChestOpened implements RosettaTrigger {
    @Override
    public void register(TriggerContext ctx) {
        ScreenEvents.AFTER_INIT.register((client, screen, w, h) -> {
            if (screen instanceof ContainerScreen) {
                ctx.fire();
            }
        });
    }
}
```

```java
Rosetta.register(new SimpleCard(id, title, category, new FirstChestOpened()).addText(...));
```

## Notes

- A trigger's `register()` runs at registration time (typically client init). It should not block.
- You can safely call `ctx.fire()` more than once. Rosetta deduplicates: if the card is already in the pending queue or has already been acknowledged, repeated fires are no-ops.
- Rosetta automatically suppresses display while the player is in combat, gliding, sleeping, dead, or in a screen. See [Concepts → Suppression rules](concepts.md#suppression-rules).
- If you don't pass a trigger (`null` for the `trigger` parameter of `SimpleCard`), the card is registered but never auto-fires. You can still show it on demand via `Rosetta.show(id)`.
