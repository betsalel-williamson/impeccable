# Install Impeccable

This page shows how to put the Impeccable skill, and optionally its [design hook](../glossary/design-hook.md), into your coding agent. It is for anyone setting Impeccable up for the first time.

## The usual path

From your project root:

```bash
npx impeccable install
```

The installer looks for the coding tools you use, asks whether to install into this project or at the user level (your home directory), and offers the design hook where your tool supports one. Say yes to the hook: it is how the detector runs on every UI edit your agent makes. Reload your coding tool afterward, then type `/impeccable init` in the agent's chat, not in the terminal. On Codex the prefix is `$`: `$impeccable init`.

Node 18.19 or newer is needed for `npx` and nothing else. The skill calls a self-contained [engine binary](../glossary/engine.md) through a small [launcher](../glossary/launcher.md); the binary ships next to the skill or downloads once into `~/.impeccable/bin/` and is checked against a pinned checksum before it runs.

## Scripted installs

Pass `-y` to accept detected defaults without prompts:

```bash
npx impeccable install -y --providers=claude,codex --scope=project
```

| Flag | Effect |
|---|---|
| `--providers=<names>` | Comma-separated tools to install for (names below) |
| `--scope=project` or `--scope=global` | Where to install; `--project`, `--user` and `--global` are shorthands |
| `-y`, `--yes` | Accept defaults: project scope, and the hook unless this project already declined it |
| `--no-hooks` | Install the skill without hook manifests |
| `--force` | Replace an existing install |

Names `--providers` accepts: `claude`, `codex`, `cursor`, `gemini`, `github` (Copilot), `devin`, `antigravity`, `replit`, `opencode`, `kiro`, `trae`, `trae-cn`, `rovo-dev`, `qoder`, `vibe` (Mistral Vibe), `grok`, `hermes`, `dsh` (DeepSeek Harness), `pi` and `omp` (Oh My Pi). Codex and Replit Agent share the `.agents/` folder.

## One more step on some tools

Three tools hold the hook until you approve it:

- **Codex:** open `/hooks` and approve the project hook. Approve again after an update that changes it.
- **Grok Build:** run `/hooks-trust`, or launch with `--trust`.
- **Hermes Agent:** the hook is a plugin. Run this once:

```bash
hermes plugins enable impeccable-design-hook
```

On Cursor, check that hooks are enabled in its settings. On GitHub Copilot, the CLI picks up `.github/hooks/impeccable.json` once that file is committed to the repository's default branch.

## Other ways in

- **Claude Code plugin:** `/plugin marketplace add pbakaus/impeccable`, then install Impeccable from `/plugin`.
- **A pinned version for the whole team:** add this repository as a git submodule and run `npx impeccable link --source=<submodule path> --providers=claude,cursor`. Linking installs skills, not hooks.
- **No Node at all:** copy the prebuilt `skills/impeccable` folder for your tool. A manual copy gets no hook and no subagents.

The [README](../../README.md) has the folder table for manual copies and the per-tool notes for each alternative. The VS Code extension is described in [VSCODE-EXTENSION.md](../VSCODE-EXTENSION.md).

Next: [set up your project](set-up-a-project.md).
