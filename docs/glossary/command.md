# Command

A command is one of the design tasks under the single `impeccable` skill, run as `/impeccable <command>` (for example `/impeccable polish` or `/impeccable audit`). The Commands table in `skill/SKILL.src.md` lists 24 of them, and each loads its own file from `skill/reference/` when it runs. `pin`, `hooks` and `doctor` are utilities outside that table and do not count toward the total.

Not to be confused with an [engine](engine.md) verb such as `impeccable context` or `impeccable detect`. A verb is a subcommand of the binary that the skill text runs on the agent's behalf; a command is what the user asks the agent for.

See [Commands](../client/commands.md) and [Run a command](../client/run-a-command.md). The router table itself is in [SKILL.src.md](../../skill/SKILL.src.md).
