# Design hook

The design hook runs the detector automatically after the agent edits a design-relevant file (`.tsx`, `.css`, `.html`, `.vue`, `.svelte` and similar). On each edit it reports only the immediate tier of [rules](anti-pattern-rule.md); a deeper pass over every UI file touched runs when the session stops. `/impeccable hooks on|off|status` manages it per project through `.impeccable/config.json`. It skips its scan when PRODUCT.md declares a native [platform](platform.md).

Not to be confused with the detector itself (`impeccable detect`), which runs only when someone calls it and scans whatever it is pointed at. The hook is one automatic caller of the same rules.

See [Design hook](../features/design-hook.md). Harness-specific wiring is in [hooks.md](../../skill/reference/hooks.md).
