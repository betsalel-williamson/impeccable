# About this guide

This page says who the client guide is written for and where to look when your question is not a user question.

## Who it is for

You build or design frontends with a coding agent: Claude Code, Cursor, Codex CLI, Gemini CLI, GitHub Copilot, or one of the other tools the installer supports. You want Impeccable in your project so you can type `/impeccable polish the settings page` and get a better page back, run the slop detector in CI, or try design variants in the browser.

You do not need to know Rust, the build system, or how the skill text is generated. You do need a terminal, a project with a frontend in it, and an agent that can run shell commands.

## What it covers

Every page here answers one task: installing, setting up a project, running a command, scanning for anti-patterns, working in live mode, using Impeccable on a native app, controlling what goes over the network, and fixing a setup that went wrong. [Commands](commands.md) is the one lookup table.

## Where else to look

- **What the product does and guarantees**, such as how the detector decides a finding or what live mode writes to your source: the [features guide](../features/overview.md).
- **A term you are not sure of**, such as [mode](../glossary/mode.md) or [surface brief](../glossary/surface-brief.md): the glossary.
- **Contributing to Impeccable itself**: the [developer guide](../developer/repo-layout.md).
- **Every flag and exit code of every verb**: [the CLI contract](../CLI-CONTRACT.md). It is written for maintainers, so start here and go there only for the edge case.

Per-command pages with examples live at [impeccable.style/docs](https://impeccable.style/docs). This guide links there when the site holds the longer walk-through.

Start with [Install Impeccable](install.md).
