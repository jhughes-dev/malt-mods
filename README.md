# Community Hub & Issue Tracker

Welcome! This repository is the central place for reporting bugs, requesting features, and contributing community translations for the mod. Please read through the guidelines below before opening an issue — it helps us respond faster and keeps things organized for everyone.

---

## How to Submit an Issue

### Before You Post

1. **Search existing issues first.** Your bug or request may already be reported. If it is, add a thumbs-up reaction (👍) to the original post rather than opening a duplicate — this helps me prioritize.
2. **Make sure you're on the latest version.** Many bugs are fixed in newer releases. Check that you're running the most recent version of the mod before reporting.
3. **Disable other mods and test.** If possible, confirm the issue still occurs with only this mod active. This rules out conflicts and speeds up diagnosis. If not--still report it but share the mods you're using to help narrow it down. The shorter the list, the easier that will be and the sooner a fix will be available.

### Writing a Good Bug Report

Use the **Bug Report** issue template when available. A strong bug report includes:

- **A clear, specific title.** Avoid vague titles like "It's broken" — instead, write something like "Crafting menu crashes when selecting the third tab."
- **Steps to reproduce.** Number them out so anyone can follow along. The more precise, the better. Link a video if you have it.
- **Expected behavior vs. actual behavior.** Tell us what *should* happen and what *actually* happens.
- **Environment details.** Include your game version, mod version, operating system, and any other mods you have installed.
- **Screenshots or logs.** A picture is worth a thousand words. If the game produces a log file or crash report, attach it. Use code blocks (\`\`\`) for pasting log snippets to keep them readable.

#### Example Bug Report

> **Title:** Highlights do not render after restarting
>
> **Steps to reproduce:**
> 1. Start a new game with the mod enabled.
> 2. Drop an item, see the highlight
> 3. Hold H then exit.
> 4. Save the game and exit to the main menu.
> 5. Reload the save.
>
> **Expected:** Press H again, see the highlight and "on" message
> **Actual:** The message says off but the highlight is on.
>
> **Environment:** Game v1.21.., Mod v0.1.0, Windows 11, Only reprocuible with MiniHud installed.

### Requesting a Feature

Use the **Feature Request** issue template. Include:

- **What problem does this solve?** Describe the situation or pain point that motivates the request.
- **Your proposed solution.** Explain how you'd like it to work. Be as specific as you can, but don't worry about technical implementation details.
- **Alternatives you've considered.** Mention any workarounds you've tried or other approaches that could work.

Keep in mind that not every request will be implemented, but all feedback is valued and helps shape the mod's direction.

---

## Contributing Community Translations

We welcome translations into any language! Here's how to contribute:

1. **Check the existing translations** in the resources directory to see what's already been done and what format is used.
2. **Fork this repository** and create a new branch for your translation (e.g., `translation/french`).
3. **Copy the base English resource file** as your starting template. Do not modify the original keys — only translate the values.
4. **Submit a Pull Request** with your translated file. In the PR description, note the language and whether the translation is complete or partial.

### Translation Guidelines

- **Do not translate placeholders or variables** (e.g., `{player_name}`, `%s`, `$1`). These are filled in by the game at runtime.
- **Preserve formatting tags** such as color codes, line breaks, or markup exactly as they appear in the source.
- **Keep the tone consistent** with the original text. If the source is casual, keep the translation casual; if it's formal, keep it formal.
- **If a term has no good equivalent**, keep the original English term and add a brief note in your PR explaining why.

---

## General Etiquette

- **Be respectful and constructive.** Everyone here is volunteering their time.
- **One issue per post.** If you have multiple bugs to report, open separate issues for each so they can be tracked independently.
- **Don't bump issues.** Adding "any update?" comments without new information clutters the thread. Use reactions instead to show continued interest.
- **Use labels if available.** Applying the right label (bug, enhancement, translation, etc.) helps with triage.

---

## Useful Links

- [How to write a good bug report (GitHub Docs)](https://docs.github.com/en/issues)
- [Markdown formatting guide](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)

Thank you for helping improve the mod!
