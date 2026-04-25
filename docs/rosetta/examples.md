# Examples

Worked examples of Rosetta integrations from real mods.

## Drop Highlighter - first-launch keybind explainer

[Drop Highlighter](https://github.com/Malteas-Games/drop-highlighter) is a small Fabric/NeoForge mod that highlights dropped items. It uses Rosetta to teach players the toggle keybinds on first launch and to offer a "disable radar" button for players who don't want that feature.

Source: [`DropHighlighterOnboarding.java`](https://github.com/Malteas-Games/drop-highlighter/blob/main/src/main/java/io/github/malteas/drophighlighter/client/DropHighlighterOnboarding.java)

### The card

```java
public final class DropHighlighterOnboarding {

    public static void register() {
        Component tap = Component.translatable("screen.drophighlighter.onboarding.tap");
        Component hold = Component.translatable("screen.drophighlighter.onboarding.hold");

        Rosetta.register(new SimpleCard(
                Identifier.fromNamespaceAndPath(DrophighlighterMod.MOD_ID, "welcome"),
                Component.translatable("screen.drophighlighter.onboarding.title"),
                CardCategory.TUTORIAL,
                BuiltinTriggers.firstLoad())
            .width(320)
            .addText(keyRow("key.drophighlighter.toggle", tap, hold)).addSep()
            .addText(keyRow("key.drophighlighter.toggle_radar", tap, hold))
            .addButton(Component.translatable("screen.drophighlighter.onboarding.disable_radar"),
                    () -> DropHighlighterConfig.setRadarEnabled(false))
        );

        Identifier welcomeId = Identifier.fromNamespaceAndPath(DrophighlighterMod.MOD_ID, "welcome");
        if (DropHighlighterConfig.hasShownOnboarding()) {
            Rosetta.markSeen(welcomeId);
        }
    }

    private static MutableComponent keyRow(String keyId, Component tapHint, Component holdHint) {
        MutableComponent row = Component.empty()
                .append(Component.translatable(keyId)).append(" - ")
                .append("[").append(Component.keybind(keyId)).append("] ")
                .append(tapHint);
        if (holdHint != null) {
            row.append(" / ").append(holdHint);
        }
        return row;
    }
}
```

### Walkthrough

**Identifier.** `drophighlighter:welcome` - a single card registered under the mod's ID. The path `welcome` is the convention for a first-launch card.

**Trigger.** `BuiltinTriggers.firstLoad()` - fires every world load, but Rosetta only displays cards that haven't been acknowledged at their current version, so the player sees it once.

**Width.** `320` - wider than the default `300` to fit the longer keybind rows comfortably.

**Body.** Two `addText(...)` rows separated by `addSep()`. Each row is composed from a translatable strings, a live `Component.keybind(...)` placeholder (which Minecraft renders as the player's currently-bound key), and tap/hold hint text. This keeps the card accurate when players rebind keys.

**Button.** A single optional action button - `setRadarEnabled(false)` - that lets the player opt out of the radar feature directly from the onboarding card. The Acknowledge button is added automatically below it.

**Migration shim.** Drop Highlighter had a pre-Rosetta config flag (`hasShownOnboarding`) tracking whether players had seen the old welcome screen. After registering the card, the code checks the legacy flag and calls `Rosetta.markSeen(welcomeId)` so existing users don't see a fresh card on update.

### Where it's wired up

Drop Highlighter calls `DropHighlighterOnboarding.register()` from its client entry point during initialization, after Rosetta itself has loaded:

```java
// Fabric: ClientModInitializer.onInitializeClient
@Override
public void onInitializeClient() {
    // ... other setup ...
    DropHighlighterOnboarding.register();
}
```

The trigger handles the rest - it subscribes to the player-join event itself, so there's no per-tick or per-event wiring elsewhere in the mod.

### Build dependency

The fabric build script declares Rosetta:

```kotlin
repositories {
    maven("https://malteas-games.github.io/maven/") {
        content { includeGroup("io.github.malteas.rosetta") }
    }
}

dependencies {
    if (isUnobfuscated) {
        implementation("io.github.malteas.rosetta:rosetta-fabric:0.1.0+$minecraft")
    } else {
        modImplementation("io.github.malteas.rosetta:rosetta-fabric:0.1.0+$minecraft")
    }
}
```

And `fabric.mod.json` lists Rosetta as a required dependency:

```json
{
  "depends": {
    "rosetta": "*"
  }
}
```

That's the entire integration: ~50 lines of Java for the card, three lines of build script, one line of mod metadata.
