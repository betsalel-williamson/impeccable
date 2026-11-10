# Run the detector

This page shows the three places the [anti-pattern](../glossary/anti-pattern-rule.md) detector runs and how to quiet a finding you have decided to keep. The detector has 62 deterministic rules, needs no LLM and no API key, and works on web code only.

## In your agent: the design hook

The [design hook](../glossary/design-hook.md) scans every UI file your agent edits and hands findings back before the agent calls the work done. Without it, the detector runs only when someone remembers. `npx impeccable install` offers it; see [Install](install.md) for the per-tool approval steps.

Manage it from the agent's chat:

| Command | Effect |
|---|---|
| `/impeccable hooks status` | Shows whether the hook is on, which harnesses are wired, and the active ignores |
| `/impeccable hooks off` | Stops the hook for this project (`hook.enabled: false` in `.impeccable/config.json`) |
| `/impeccable hooks on` | Turns it back on and repairs missing hook entries |
| `/impeccable hooks reset` | Removes the hook settings and detector ignores from both config files, deletes the hook cache, and removes the hook entries it installed |

The hook reports mechanical problems (broken images, overflow, contrast, gradient text, drift from your design system) right after an edit, and saves the rest of the rule set for one pass when the agent finishes, on tools that support that pass. It never blocks an edit. It runs the engine with your agent's environment and network access, outside any sandbox, so review installed hooks before unattended runs.

## In your terminal or CI

The CLI needs no agent:

```bash
npx impeccable detect src/
npx impeccable detect http://localhost:5173
npx impeccable detect --json .
npx impeccable detect --github src/
npx impeccable detect --list-rules
```

Source files (JSX, TSX, Vue, Svelte, CSS) get the pattern rules. Rules that need a rendered page, such as low contrast and tiny text, run only on a URL scan, so point the CLI at your dev server too. URL scans use an installed Chrome, Chromium, Edge or Brave; set `IMPECCABLE_BROWSER` to the executable if it is somewhere unusual. `--viewport 390x844` scans at phone width.

Exit codes: `0` no primary findings (advisories may still print), `2` findings, `1` a target could not be scanned. A partial scan exits `1` even when other targets had findings. The full flag list is in [the CLI contract](../CLI-CONTRACT.md).

## In your browser

The [Chrome extension](https://chromewebstore.google.com/detail/impeccable/bdkgmiklpdmaojlpflclinlofgjfpabf) overlays the same findings on any page you open.

## Keep a finding on purpose

Pick the narrowest exception. Each of these writes the shared `.impeccable/config.json`, which the hook and the CLI both read:

```bash
npx impeccable ignores add-value overused-font Inter --reason "Brand font"
npx impeccable ignores add-value side-tab "*" --file "src/widgets/Tab.tsx"
npx impeccable ignores add-file "src/legacy/**"
npx impeccable ignores list
```

A value ignore allows one value of one rule. A `"*"` value with `--file` turns one rule off in matching files. A file ignore silences every rule for those files, including rules added later. Add `--local` to keep an exception in your untracked `.impeccable/config.local.json`. Inside the agent, `/impeccable hooks ignore-value`, `ignore-file` and `ignore-rule` do the same.

For a file that leaves the repository, put the waiver in the file: `<!-- impeccable-disable overused-font: exported brand doc -->` covers the whole file, and `impeccable-disable-line` or `impeccable-disable-next-line` cover one line, in any comment syntax. `--no-config` scans with no ignores, inline comments or DESIGN.md applied.

How rules decide is described in the [detector feature page](../features/detector.md).
