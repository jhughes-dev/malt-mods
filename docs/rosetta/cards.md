# Cards

A **card** is one piece of onboarding content. The current implementation ships one card type, `SimpleCard`, which renders a centered modal with a title, body content (text and separators), and optional action buttons.

## Constructor

```java
new SimpleCard(Identifier id, Component title, CardCategory category, RosettaTrigger trigger)
```

| Parameter | Description |
|---|---|
| `id` | Unique identifier `<your-modid>:<card-name>`. The namespace **must** be your mod's ID. |
| `title` | Displayed at the top of the card, scaled 1.5x. |
| `category` | One of `TUTORIAL`, `TIP`, `WARNING`, `NEWS`, `CREDITS`. Used by the replay screen for filtering. |
| `trigger` | A `RosettaTrigger` that decides when the card fires. See [Triggers](triggers.md). |

All four are required and non-null.

## Body content

Body items are appended in order. Each method returns `this` for chaining.

### `addText(Component text)`

Adds a wrapping text block. Word-wraps automatically to the card's content width.

```java
.addText(Component.literal("Press B to open the build menu."))
```

### `addSep()`

Adds a thin horizontal separator with padding above and below. Useful for grouping related rows.

```java
.addText(rowOne).addSep().addText(rowTwo)
```

### `addButton(Component label, Runnable onClick)`

Appends a button below the body content. Multiple buttons stack vertically. Each button spans the card's full content width. The Acknowledge button (which closes the card) is always shown beneath your buttons.

```java
.addButton(Component.literal("Open Settings"), () -> openMyConfig())
```

The runnable executes on the render thread when the button is clicked.

## Visual customization

### `addIcon(Identifier icon)`

Renders a 16×16 texture to the left of the title. The texture must be registered as a resource at the given identifier (e.g. `assets/yourmodid/textures/gui/card_icon.png`).

```java
.addIcon(Identifier.fromNamespaceAndPath("yourmodid", "textures/gui/card_icon.png"))
```

### `addBackground(Identifier background)`

Renders a full-card background texture beneath the overlay color. Used for branded headers or decorative imagery.

```java
.addBackground(Identifier.fromNamespaceAndPath("yourmodid", "textures/gui/card_bg.png"))
```

### `width(int pixels)`

Sets the card's outer width. Default: `300`. The card is always centered horizontally; height is computed from content.

```java
.width(360)
```

### `overlay(int argb)`

ARGB color drawn above the background and below the content. Default: `0x80000000` (50% black).

```java
.overlay(0xC0202030)
```

### `border(CardBorder border)`

Optional decorative border drawn around the card. Pass a `CardBorder` instance (see `CardBorder` in the source for the interface contract).

## Behavior

### `version(int n)`

Default: `1`. Bump this integer to mark the card as updated content. Players who saw the previous version will see the card again on next load. Use for tutorials whose underlying feature changed.

```java
.version(2)
```

For "what's new in this release" notes, prefer registering a new card with a new ID per major version (e.g., `mymod:whats_new_v2`) rather than bumping a single card's version.

### `noSound()`

Default: cards play `UI_TOAST_IN` when shown. Call `noSound()` to silence the audio cue.

```java
.noSound()
```

## Full example

```java
Rosetta.register(new SimpleCard(
        Identifier.fromNamespaceAndPath("mymod", "welcome"),
        Component.translatable("screen.mymod.onboarding.title"),
        CardCategory.TUTORIAL,
        BuiltinTriggers.firstLoad())
    .width(340)
    .addIcon(Identifier.fromNamespaceAndPath("mymod", "textures/gui/icon.png"))
    .addText(Component.translatable("screen.mymod.onboarding.intro"))
    .addSep()
    .addText(Component.translatable("screen.mymod.onboarding.controls"))
    .addButton(Component.translatable("screen.mymod.onboarding.open_settings"),
            () -> MyConfig.openScreen())
    .version(1)
);
```

Translatable components are recommended over `Component.literal(...)` so your card respects player locale.
