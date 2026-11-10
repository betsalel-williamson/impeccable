# Live mode

Live mode is the `/impeccable live` command: the user picks elements in their running app in the browser, chooses a design action, and the agent writes HTML and CSS variants that the dev server hot-swaps in place. Accepting a variant writes it back to source. `/impeccable generate` produces variants of a named element in the same browser session without manual picking. Live mode needs a local checkout and is web-only.

Not to be confused with the browser extension, which detects [anti-patterns](anti-pattern-rule.md) on any page but changes nothing. Also distinct from the `live-server` verb, the local server that live mode starts.

See [Live mode](../features/live-mode.md) and [Use live mode](../client/use-live-mode.md). The agent's procedure is in [live.md](../../skill/reference/live.md).
