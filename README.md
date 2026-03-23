# Plugin Skill Playground

Claude Code skills for cross-plugin feature propagation across LottieFiles plugin repos.

## What is this?

When we implement a feature in one plugin (e.g., AE) and need to apply it to others (Figma, Canva, Webflow, Framer...), this skill automates the process — adapting to each plugin's UI system, theme, and GQL structure.

## How it works

1. Implement feature in one plugin (reference)
2. Register with `/cross-plugin-apply --register <feature> --ref <plugin> --guide "<doc>"`
3. Apply to others with `/cross-plugin-apply <feature> --target <plugin>`
4. Claude reads the reference code + guide doc, analyzes the target plugin's structure, and presents a conversion plan before implementing

The skill knows each plugin's differences:

| Plugin | UI System | Theme | GQL Structure |
|--------|-----------|-------|---------------|
| plugin-aftereffects | LF Design System | Dark | src/gql/ |
| plugin-figma | LF Design System | Light | ui/src/gql/ |
| plugin-framer | LF Design System | Light | src/gql/ |
| plugin-canva | Canva Design SDK | Dark & Light (tokens) | src/queries/ |
| plugin-webflow | Custom color tokens | Dark | src/gql/ |

## Skill file

See [`skills/cross-plugin-apply.md`](skills/cross-plugin-apply.md) for the full skill definition.

## Distribution options — feedback wanted

How should we share this across the team?

**Option A: Per-repo** — Put the skill in each plugin repo's `.claude/skills/`. Anyone who clones the repo gets it automatically. Downside: 5 copies to keep in sync.

**Option B: Shared repo** — Keep skills in one repo, each team member symlinks to `~/.claude/skills/`. Single source of truth. Downside: extra setup step per person.

Which approach works better for our team? Drop your thoughts in the thread.

## Setup (for trying it out)

Copy the skill to your Claude config:

```bash
cp skills/cross-plugin-apply.md ~/.claude/skills/
```

Then use `/cross-plugin-apply` in any plugin repo.
