# claude-recipes

Copy-paste recipes for automating things with Claude.

Each recipe is a self-contained markdown file: what it does, what you need, the preferences you have to decide, and the prompt to paste in. Placeholders are marked `«LIKE_THIS»` — fill them with your own answers and the recipe is yours.

## Recipes

| Recipe | What it does |
|---|---|
| [Weekly music discovery playlist](recipes/weekly-music-discovery-playlist.md) | Scans music press every Friday, builds a Spotify playlist from the picks, and verifies what actually shipped |

## Repo layout

```
recipes/    Step-by-step setups you fill in and paste into Claude
skills/     Reusable Claude skills (SKILL.md + supporting files)
prompts/    Standalone prompts worth keeping
plugins/    Packaged Claude plugins
```

Folders appear as they get their first occupant.

## How to use a recipe

1. Read the **What you need** section — most recipes want a connector or two enabled.
2. Work through the preference choices. They're written as real options with tradeoffs, not defaults to accept blindly.
3. Fill your answers into the `«placeholders»` in the prompt block.
4. Run it once live before scheduling it. Calibration on a real result beats guessing at config.

## Contributing

Fork it, add a recipe under `recipes/`, open a PR. A good recipe:

- **States its prerequisites.** Which connectors, which account tier.
- **Makes preferences explicit.** Every choice a reader might make differently is a placeholder, not a hardcoded assumption.
- **Explains tradeoffs.** "Pick A or B" is less useful than "A is faster, B catches more."
- **Names its limitations.** If a tool can't do something you'd expect, say so before someone discovers it the hard way.
- **Is self-contained.** The prompt block should work pasted into a fresh session with no prior context.

## License

MIT — see [LICENSE](LICENSE).
