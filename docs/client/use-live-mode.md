# Use live mode

This page shows how to iterate on a running web app with [live mode](../glossary/live-mode.md): select an element in the browser, ask for a direction, and cycle through variants before one is written to your source. It is for web projects only.

## Before you start

You need a local checkout and either a dev server with hot reload (Vite, Next.js, SvelteKit, Astro, Nuxt and similar) or a static HTML page open in the browser. Start your dev server as you normally do. Live mode never runs against a deployed site, and you should not loosen a production Content Security Policy to make it work.

## Start a session

In your agent's chat:

```
/impeccable live
```

The agent boots a small helper server, injects a script into the HTML your app serves, and opens or asks for your app's URL. The helper listens on its own port, starting at 8400; it is separate from your dev server.

The first run in a project may need setup. The agent writes `.impeccable/live/config.json`, which names the files to inject into, and checks for a Content Security Policy. If your project has one that would block `http://localhost:8400`, the agent shows you a development-only patch and applies it only if you say yes. Decline, and live mode will not load until you add the allowance yourself.

## Work in the browser

1. Click an element on the page to select it.
2. Pick a direction (bolder, quieter, distill, polish, typeset, colorize, layout, adapt, animate, delight, overdrive) or type your own prompt. You can annotate the element with comments and strokes first.
3. Press Go. The variants appear in place, hot-swapped through your dev server.
4. Cycle through them and adjust any parameter knobs the agent added.
5. Accept one to write it into your source, or discard them all.

To skip the clicking, name the element and direction in one sentence:

```
/impeccable generate 3 bolder variants of the pricing cards
```

`generate` boots live mode, scrolls the page to the element, and delivers the variants. With no number it makes 3, and it caps at 8.

## Copy edits and Apply

You can also edit text on the page and press Apply. When your chat agent is polling the live session, Apply sends the staged edits to it. When nothing has polled for about a minute, Apply starts the `codex` CLI from your PATH instead (or `claude` when codex is missing or not signed in), with its approval prompts and sandbox turned off and your environment inherited. Set `IMPECCABLE_LIVE_COPY_AGENT=chat` in the shell that starts live mode to keep Apply in your chat session, or `off` to disable it. If your `package.json` defines an `impeccable:manual-edit-validate` script, it runs after copy edits with your permissions, so read it before using live mode in an unfamiliar repo.

## Stop

Say so in chat, close the tab, or press the exit button in the overlay. The agent stops the helper and removes the injected script. If a session was interrupted, ask the agent to resume it: unfinished work is kept in a journal under `.impeccable/live/sessions/` and replayed when the helper restarts.

Live mode writes its runtime files under `.impeccable/live/` and keeps them out of git; see [Project files and git](project-files-and-git.md). How variants are wrapped and accepted per framework is in the [live mode feature page](../features/live-mode.md).
