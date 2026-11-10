# Set up a project

This page walks you through `init` and `document`, the two commands that give every later command something true to work from. Run them once per project, after [installing](install.md).

## Run init

In your agent's chat:

```
/impeccable init
```

`init` reads your code before it asks anything: product copy, package and config files, routes, roles, assets, and signs of the delivery platform. Then it asks only what the repository cannot answer, at most three questions a round. Expect questions about who uses the product, what it makes possible, and what any future change must preserve. It does not ask about colors, fonts, or style. Those belong to the visual system, not the product record.

When the interview is done it writes [PRODUCT.md](../glossary/product-md.md) at the project root. That file holds users, purpose, positioning, constraints, evidence you actually have, and a `## Platform` value of `web`, `ios`, `android` or `adaptive`. Every command reads it through `impeccable context` at the start of a session.

Two optional steps follow:

- **Build path.** If your agent can generate images, `init` asks once whether new landing pages and showcase surfaces should start from a generated comp or go straight to code, and saves your answer as `buildPath` in `.impeccable/config.json`. App screens and documents are built in code either way. See [build path](../glossary/build-path.md).
- **Live mode.** On a runnable web project it may write `.impeccable/live/config.json` so [live mode](use-live-mode.md) works later. If your project has a Content Security Policy, it shows you the dev-only change it wants to make and waits for your yes.

At the end it tells you which files to commit. [Project files and git](project-files-and-git.md) has the full list.

Running `init` again updates the existing PRODUCT.md instead of starting over. It asks what is stale or missing and leaves confirmed answers alone.

## Capture the visual system

If the project already has a look worth keeping, run:

```
/impeccable document
```

It reads your tokens and components, drafts the colors, type, spacing and component patterns, and asks you to confirm the descriptive language before it writes [DESIGN.md](../glossary/design-md.md). The file follows the Google Stitch DESIGN.md format. Anything that format cannot hold goes into the `.impeccable/design.json` sidecar.

Skip `document` on an empty project. A missing DESIGN.md is fine: when you ask for a new page, Impeccable decides whether to keep, extend, or replace the visual world your code already has.

## Without init

Narrow refinements still work on a project with no PRODUCT.md. `/impeccable polish the header` proceeds on the code you have and offers `init` afterward. A request for a new page or a full redesign routes through `init` first.

Next: [run a command](run-a-command.md).
