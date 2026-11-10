# Update and troubleshoot

This page shows how to keep Impeccable current and what to do when a session, the hook, or the engine misbehaves. It is for the person who installed Impeccable in the project.

## Update

```bash
npx impeccable check
npx impeccable update
```

`check` says whether the installed skill is current. `update` refreshes the skill files in every harness folder where it finds them and repairs stale hook entries. When the skill is installed both in the project and at the user level, it updates the project; pass `--user` for the other one. `--no-hooks` leaves hook manifests alone and `--force` rewrites the skill files even when they look current. A Claude Code plugin install updates through the plugin manager instead.

Once a day at most, the first command of a session checks for a newer version. If one exists, your agent mentions it once and carries on with the task. It does not update in the same turn, because the update rewrites the files the session is reading and only takes effect in the next session. Ask for the update in a later turn, or run the command yourself. To stop the check, see [Privacy and telemetry](privacy-and-telemetry.md).

After an update, reload your coding tool and approve the hook again where your tool asks (Codex does after a hook change).

## Project files written by an older version

An update can change what Impeccable reads from PRODUCT.md, DESIGN.md, `.impeccable/config.json`, surface briefs and the hook. `update` ends with a report of anything that drifted and tells you to run doctor in your agent:

```
/impeccable doctor
```

Each finding carries a severity that says what happens next:

| Severity | What the agent does |
|---|---|
| `auto` | Applies the mechanical fix without asking, with `impeccable doctor --fix` |
| `mention` | Tells you once, with the offered fix |
| `route` | Names the command that owns the repair, usually `init` or `document`, and runs it only if you ask |

A deprecated section such as `## Register` in PRODUCT.md is read by nothing; doctor offers to delete it. A finding that DESIGN.md has many commits since its last edit is a count, not proof the document is wrong. See [artifact drift](../glossary/artifact-drift.md) for the three kinds of "out of date".

Sessions also print a short `CONTEXT_STALE` notice, at most once a week per project, when the cheap subset of these checks finds something. Your agent reports it and does not repair it in the middle of a design task. Turn it off with `"stalenessCheck": false` in `.impeccable/config.json`, or `IMPECCABLE_NO_STALENESS_CHECK=1` for one session; doctor still works.

## The hook does not fire

1. Run `/impeccable hooks status`. It names each harness and whether its hook entry is current.
2. If an entry is missing or stale, run `/impeccable hooks on`.
3. Reload the coding tool, then finish the approval step your tool needs (see [Install](install.md)).

The hook skips native projects on purpose; see [Native apps](native-apps.md).

## The engine will not start

The launcher prints the reason. The usual ones:

- **No network or no write access** on first run. Run the launcher once in a terminal that has both, set `IMPECCABLE_HOME` to a writable folder for the cache, or set `IMPECCABLE_BIN` to an engine binary you already have.
- **Checksum mismatch or no checksum.** The launcher refuses the download. Do not work around it; reinstall the skill.
- **A binary skipped for a version mismatch.** A stale binary next to the skill is ignored with a warning; delete it.
- **A TLS-inspecting proxy.** Downloads trust your system certificate store. Set `SSL_CERT_FILE` or `SSL_CERT_DIR` to use a specific CA bundle.
- **A GitHub outage.** The message says so. Retry later.

## Live mode will not load

A Content Security Policy that blocks `http://localhost:8400` is the common cause. If you declined the patch during setup, delete `cspChecked` from `.impeccable/live/config.json` and run `/impeccable live` again; setup asks once more.

For anything else, open an [issue](https://github.com/pbakaus/impeccable/issues).
