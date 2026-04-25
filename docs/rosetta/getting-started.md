# Getting Started with Rosetta

This page walks through adding Rosetta to a Fabric or NeoForge mod and registering a "hello world" card that shows the first time a player loads a world after installing your mod.

## 1. Add the Maven repository

In your mod's build script (`build.fabric.gradle.kts` or `build.neoforge.gradle.kts`), add the Malteas-Games maven:

```kotlin
repositories {
    maven("https://malteas-games.github.io/maven/") {
        content { includeGroup("io.github.malteas.rosetta") }
    }
}
```

The `includeGroup` filter keeps Gradle from probing this maven for unrelated artifacts.

## 2. Add the dependency

### Fabric

```kotlin
dependencies {
    // For obfuscated MC (1.21.x) you must use modImplementation:
    modImplementation("io.github.malteas.rosetta:rosetta-fabric:0.1.1+${minecraft}")

    // For unobfuscated MC (26.x) the artifact is shipped non-remapped, so use plain implementation:
    // implementation("io.github.malteas.rosetta:rosetta-fabric:0.1.1+${minecraft}")
}
```

If your project supports both ranges (via Stonecutter or similar), gate on the MC version:

```kotlin
val isUnobfuscated = !minecraft.startsWith("1.")
if (isUnobfuscated) {
    implementation("io.github.malteas.rosetta:rosetta-fabric:0.1.1+$minecraft")
} else {
    modImplementation("io.github.malteas.rosetta:rosetta-fabric:0.1.1+$minecraft")
}
```

### NeoForge

```kotlin
dependencies {
    implementation("io.github.malteas.rosetta:rosetta-neoforge:0.1.1+${minecraft}")
}
```

## 3. Declare Rosetta as a required dependency

### Fabric (`fabric.mod.json`)

```json
{
  "depends": {
    "rosetta": "*"
  }
}
```

### NeoForge (`META-INF/neoforge.mods.toml`)

```toml
[[dependencies.yourmodid]]
modId = "rosetta"
type = "required"
versionRange = "[0.1.1,)"
ordering = "NONE"
side = "CLIENT"
```

## 4. Register your first card

Create a small class in your client-side initialization path:

```java
package com.example.yourmod.client;

import io.github.malteas.rosetta.CardCategory;
import io.github.malteas.rosetta.Rosetta;
import io.github.malteas.rosetta.SimpleCard;
import io.github.malteas.rosetta.trigger.BuiltinTriggers;
import net.minecraft.network.chat.Component;
import net.minecraft.resources.Identifier;

public final class YourModOnboarding {

    private YourModOnboarding() {}

    public static void register() {
        Rosetta.register(new SimpleCard(
                Identifier.fromNamespaceAndPath("yourModId", "welcome"),
                Component.literal("Welcome to YourMod"),
                CardCategory.TUTORIAL,
                BuiltinTriggers.firstLoad())
            .addText(Component.literal("This mod adds X. Press Y to do Z."))
        );
    }
}
```

Call `YourModOnboarding.register()` from your **client** entry point (Fabric: `ClientModInitializer.onInitializeClient`; NeoForge: a `FMLClientSetupEvent` listener).

## 5. Run the game

Launch a client. Load a single-player world. The card should appear once the player has spawned. Click "Acknowledge" to dismiss it; it won't reappear unless you bump its `version()` or call `Rosetta.forget(id)`.

## Next steps

- [Cards](cards.md) - Add buttons, separators, icons, backgrounds, and tune the card width.
- [Triggers](triggers.md) - Fire cards in response to in-game events instead of on first load.
- [Concepts](concepts.md) - How identifiers, versioning, and persistence work.
- [Examples](examples.md) - Full Drop Highlighter integration walked through.
