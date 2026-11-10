# Commands

This page lists every command you can type after `/impeccable` (`$impeccable` on Codex), with its one-line purpose. Use it to find the command for a job; [Run a command](run-a-command.md) covers the syntax.

The skill has 24 commands. The table follows the order and categories of the Commands table in [SKILL.src.md](../../skill/SKILL.src.md).

| Command | Category | What it does |
|---|---|---|
| `craft [feature]` | Build | Deprecated alias for a plain build request; adds no behavior |
| `shape [feature]` | Build | Plans the UX and UI through a short interview and produces a design brief you confirm, before any code |
| `init` | Build | Writes PRODUCT.md, the durable product record every command reads |
| `document` | Build | Writes DESIGN.md from the visual system already in your code |
| `extract [target]` | Build | Pulls repeated tokens and components into the design system |
| `critique [target]` | Evaluate | Scored UX review against usability heuristics, with ranked fixes |
| `audit [target]` | Evaluate | Technical quality report on accessibility, performance, theming and responsive behavior, rated P0 to P3 |
| `polish [target]` | Refine | Final pass on alignment, spacing, consistency and detail before shipping |
| `bolder [target]` | Refine | Gives a safe or bland design more impact |
| `quieter [target]` | Refine | Tones down a loud or overstimulating design |
| `distill [target]` | Refine | Strips a design to what it needs |
| `harden [target]` | Refine | Error states, i18n, text overflow and edge cases |
| `onboard [target]` | Refine | First-run flows, empty states and activation |
| `animate [target]` | Enhance | Purposeful transitions and interaction feedback |
| `colorize [target]` | Enhance | Brings color to a gray or monochrome interface |
| `typeset [target]` | Enhance | Fonts, type hierarchy, sizing and readability |
| `layout [target]` | Enhance | Spacing, rhythm and visual hierarchy |
| `delight [target]` | Enhance | Personality and memorable touches |
| `overdrive [target]` | Enhance | Technically ambitious effects such as shaders, spring physics and scroll-driven reveals |
| `clarify [target]` | Fix | Rewrites unclear copy, labels and error messages |
| `adapt [target]` | Fix | Adapts a design to screen sizes, devices and input modes |
| `optimize [target]` | Fix | Diagnoses and fixes loading and rendering performance |
| `live` | Iterate | Pick an element in your browser and cycle through generated variants |
| `generate [n] [direction] [element]` | Iterate | Variants of a named element in the live browser, without picking it by hand |

## Aliases

- `teach` runs `init`.
- `craft` behaves like describing what you want built. It stays for old habits.

## Native variants

On a project whose `## Platform` is `ios`, `android` or `adaptive`, `audit` and `adapt` load native playbooks instead of the web ones. `live` and `generate` are web-only and decline on native projects. See [Native apps](native-apps.md).

## Utility commands

These manage Impeccable rather than your design, and are not counted among the 24:

| Command | What it does |
|---|---|
| `pin <command>` / `unpin <command>` | Adds or removes a standalone shortcut such as `/audit` |
| `hooks <on\|off\|status\|ignore-rule\|ignore-file\|ignore-value\|reset>` | Manages the design hook and detector ignores for this project |
| `doctor` | Reports and repairs project files written by an older version |

Any of the 24 can be pinned, `craft` included. Per-command pages with examples are at [impeccable.style/docs](https://impeccable.style/docs).
