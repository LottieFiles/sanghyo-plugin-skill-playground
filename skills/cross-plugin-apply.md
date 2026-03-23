---
name: cross-plugin-apply
description: |
  Apply a reference plugin implementation to other plugins. Use when:
  (1) User says "cross-plugin apply" or "apply to other plugins",
  (2) User wants to propagate a feature from one plugin to others,
  (3) User says "check cross-plugin status" or "cross-plugin status".
---

# Cross-Plugin Apply

Propagate a reference implementation from one LottieFiles plugin to others, adapting to each plugin's structure, UI system, and theme.

## Plugin Profiles

| Plugin | UI System | Theme | GQL Structure | Notes |
|--------|-----------|-------|---------------|-------|
| plugin-aftereffects | LF Design System | Dark | src/gql/ | Adobe CEP environment |
| plugin-figma | LF Design System | Light | ui/src/gql/ | UI under ui/ subdirectory |
| plugin-framer | LF Design System | Light | src/gql/ | |
| plugin-canva | Canva Design SDK | Dark & Light (tokens) | src/queries/ | Canva SDK color tokens |
| plugin-webflow | Custom color tokens | Dark | src/gql/ | Plugin-defined tokens |

### UI Adaptation Rules

- **LF Design System plugins** (AE, Figma, Framer): UI components can be largely reused from reference, adjusting theme (dark/light) only.
- **Canva**: GQL/logic layer only from reference. UI must follow Canva Design SDK patterns and token system.
- **Webflow**: GQL/logic layer only from reference. UI must follow plugin's own component patterns and color tokens.

## Tracker File

Location: `~/workspace/docs/cross-plugin-tracker.md`

All feature registration and status tracking is persisted here. Read this file at the start of every invocation.

## Modes

### 1. Register a Feature (`--register`)

```
/cross-plugin-apply --register <feature-name> --ref <plugin> --guide "<path>" [--target <plugin>]
```

**Steps:**

1. Read the reference plugin's implementation files related to the feature
2. Read the guide document
3. Identify and list the key files involved (queries, hooks, components, types)
4. Generate a one-line summary of the core pattern
5. Append a new feature section to the tracker file:
   - Feature name, ref plugin, guide path, registered date
   - `ref-files:` list with file path + purpose
   - `summary:` one-line pattern description
   - Status table with all 5 plugins (ref plugin as `✅ ref`, others as `⬚ pending`)
6. If `--target` is provided, proceed to Apply flow

### 2. Apply to Target (`--target`)

```
/cross-plugin-apply <feature-name> --target <plugin>
```

**Steps:**

1. **Context Load**
   - Read feature entry from tracker (ref-files, summary, guide path)
   - Read actual code from reference plugin files
   - Read guide document

2. **Target Analysis**
   - Read target plugin's CLAUDE.md for project-specific rules
   - Explore target plugin's GQL folder structure, component patterns, import conventions
   - Check Plugin Profiles table for UI system, theme, and GQL structure differences
   - Identify existing similar patterns in the target to follow

3. **Conversion Plan** (MUST get user approval before coding)
   - List files to create/modify in the target plugin
   - Explain key differences from reference (folder structure, UI system, theme)
   - Note any adaptation needed (e.g., dark→light theme, LF DS→Canva SDK)
   - Present to user and WAIT for approval

4. **Implement + Verify**
   - Write code after approval
   - Run build verification (`npm run build` or `tsc --noEmit`)
   - Follow target plugin's CLAUDE.md coding standards

5. **Tracker + Linear Update**
   - Update tracker: status → `✅ done`, add Linear ID, PR number
   - Create Linear issue:
     - **Project:** Plugins (PLU)
     - **Label:** `<plugin-name>` (lowercase, without "plugin-" prefix, e.g., `figma`)
     - **Title:** `[Plugin] Implement <feature-name>` (e.g., `[Figma] Implement DAM upload`)
     - **Description:** Reference plugin, guide path, scope summary from approved plan
     - **Language:** Always English
   - On PR creation: attach PR link to Linear issue, record PR number in tracker

### 3. Check Status (`--status`)

```
/cross-plugin-apply --status                    # All features summary
/cross-plugin-apply <feature-name> --status     # Specific feature detail
```

**All features:**
```
DAM-upload: 2/5 done, 1 in-progress, 2 pending
GQL-v2-migration: 0/5 done, 5 pending
```

**Specific feature:** Show the full status table from tracker.

## Status Values

| Status | Meaning |
|--------|---------|
| `✅ ref` | Reference implementation |
| `✅ done` | Applied and build-verified |
| `🔄 in-progress` | Currently being applied |
| `⬚ pending` | Not yet started |
| `⛔ skip` | Not applicable for this plugin |

## Critical Rules

1. **Never write code without showing the conversion plan first** — each plugin has structural differences
2. **Follow target plugin's existing patterns** — don't force reference plugin's patterns onto the target
3. **GQL/logic is portable, UI is not** — for Canva/Webflow, only reuse the logic layer
4. **Always run build verification** before marking as done
5. **Linear issues are always in English**
6. **Read target plugin's CLAUDE.md** before implementation — it may have project-specific rules
