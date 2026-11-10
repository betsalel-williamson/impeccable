# Run a command

This page shows how to ask Impeccable for work inside your coding agent: the command syntax, what happens when you type nothing after it, and how to make a favorite command its own shortcut.

## Name a command and a target

Everything goes through one skill, `impeccable`. Type the [command](../glossary/command.md), then say what to work on:

```
/impeccable critique the onboarding flow
/impeccable quieter the landing page
/impeccable harden checkout
```

On Codex, use `$impeccable` instead of `/impeccable`, or open `/skills`. The full list of commands is in [Commands](commands.md).

A narrow target gets a better result. One section reviewed closely beats a whole page at a glance, so name the component, route or file you mean.

You can also skip the command name and describe the job: `/impeccable redo this hero section`. Impeccable treats it as general design work, picks the playbook that fits, and asks once if two commands fit equally well.

## Type it alone for a menu

`/impeccable` with nothing after it never runs anything. It shows a menu that leads with two or three recommendations for this project, each with a one-line reason. The reasons come from the project's state: no PRODUCT.md yet puts `init` first, a project that was never critiqued gets `critique`, and recent changes to one surface get an `audit` or `polish` scoped to those files. On a web project it may also run the detector over your changed files and fold the hits into its picks.

## What happens on the first command

The first command in a session runs `impeccable context` once. That loads [PRODUCT.md](../glossary/product-md.md), [DESIGN.md](../glossary/design-md.md), the matching [surface brief](../glossary/surface-brief.md), and the iOS or Android guidance when your platform is native. It may also print a one-line notice that a newer version exists or that a project file is out of date; neither stops the task. See [Update and troubleshoot](update-and-troubleshoot.md).

The agent then loads the command's playbook and reads the target before editing it. Reviews such as `critique` and `audit` report findings and change nothing until you ask.

## Pin a shortcut

Pin a command you use often to get it as a standalone shortcut:

```
/impeccable pin audit
```

After a reload, `/audit` (or `$audit` on Codex) runs `/impeccable audit`. `/impeccable unpin audit` removes it. Pinning writes a small skill folder next to the installed `impeccable` skill in every harness folder of the project; it skips a folder that already holds a skill of that name it did not create.

## Utility commands

Two commands manage Impeccable itself and are not design commands:

- `/impeccable hooks <on|off|status|...>` manages the design hook for this project. See [Run the detector](run-the-detector.md).
- `/impeccable doctor` finds and repairs project files written by an older version. See [Update and troubleshoot](update-and-troubleshoot.md).
