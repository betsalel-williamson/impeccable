# Add a command

The steps for adding a new [command](../glossary/command.md) under `/impeccable`, in the order that keeps the build and the oracle green. For contributors who already have maintainer approval for the command. Every command lives under the one `impeccable` skill; do not add a standalone skill, because each one adds an entry to the user's slash menu.

## Steps

1. **Write the reference.** Create `skill/reference/<command>.md`. This is the text the agent loads when the router matches the command. If native platforms need different guidance, add `skill/reference/<command>.native.md` as a native variant instead of putting platform notes in the web file.
2. **Add the router row.** Add a row to the Commands table in `skill/SKILL.src.md`: the command with its argument hint in backticks, a category, a one-line description, and a link to the reference file. The build counts commands from these rows.
3. **Add the metadata.** Add an entry with `description` and `argumentHint` to `skill/scripts/command-metadata.json`. The build reads it, and the engine's `pin` verb reads the copy beside the installed skill. The engine also compiles in a fallback copy at `crates/context/src/command-metadata.json`; nothing syncs the two, so copy the file there too.
4. **Add the category.** Map the command in `SKILL_CATEGORIES` in `scripts/lib/skill-categories.js`. A command left unmapped stays out of the generated argument hint.
5. **Teach `pin` the name.** Add it to `VALID_COMMANDS` in `crates/context/src/pin.rs` (the array's declared length changes with it), then record a pin and unpin case for the [oracle](../glossary/oracle.md) and review the golden.
6. **Decide on suggestions.** `IMPECCABLE_SUB_COMMANDS` in `scripts/lib/utils.js` fills the `{{available_commands}}` placeholder and lists only the commands the skill suggests by name. Add the command there if it belongs in that list. The no-argument menu is written in `skill/reference/routing.md`; update it if the command changes what the menu should recommend.
7. **Fix the counts.** Update every quoted command count. `generateCounts` in `scripts/build.js` checks `README.md`, `README.npm.md`, `AGENTS.md`, `.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json` against the router table and fails the build on a mismatch.
8. **Validate.**

   ```bash
   bun run build
   IMPECCABLE_BIN="$PWD/target/release/impeccable" bun run test
   ```

   If the command touches Setup or routing, also run `bun run test:skill-behavior` ([opt-in suites](opt-in-suites.md)).

The private site repository has its own steps for a new command (categories, relationships, the visualization and the homepage counts). [CLAUDE.md](../../CLAUDE.md) lists them for maintainers with access.

## Utility verbs are different

Maintenance tools such as `doctor`, `hooks` and `pin` are not design commands. Each gets a paragraph in `SKILL.src.md` below the table, plus a reference file when it needs one (`doctor.md`, `hooks.md`), and they stay out of the router table, `command-metadata.json`, `SKILL_CATEGORIES`, `IMPECCABLE_SUB_COMMANDS` and the `pin` list. They do not count toward the command total.

## Prose and placement rules

Command references follow the [prose rules](writing-prose.md) for `skill/`. Accessibility guidance belongs in `audit.md` only. Design guidance lives in the command references and `craft-floor.md`, loaded only when it applies; do not create a per-domain reference file such as one for typography or color.
