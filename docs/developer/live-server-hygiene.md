# Live server hygiene in tests

Why a test that starts a [live mode](../glossary/live-mode.md) server needs extra teardown, and what this repo's harness does about it. For contributors writing or debugging a test that boots `impeccable live-server`.

## The problem

A live server does not die with the process that started it. A direct child survives its parent, and `impeccable live-server --background` detaches by design. An `after()` hook or a `finally` block only runs on exits JavaScript can observe, so a SIGKILL, a Ctrl-C or a wedged runner can leave servers holding the live suite's fixed ports long after the run.

## What a new test owes

Call `armLiveServerReaper()` from `tests/lib/live-servers.mjs` once at module scope in any test file that starts a live server. It stamps the process environment with a unique marker, installs exit and signal handlers, and spawns a detached reaper that holds a pipe to the test process. When that process dies for any reason, the pipe closes and the reaper kills every server carrying the marker. Wrap direct children in `trackServerChild()` so the common case is a plain `child.kill()`.

`tests/live-e2e/session.mjs`, `tests/oracle/lib.mjs` and `tests/live-agent-target.test.mjs` already arm it. The reaper is POSIX-only and is not armed on Windows. `IMPECCABLE_NO_TEST_REAPER=1` disables it.

## The runner guard

`scripts/run-tests.mjs` runs each suite command as its own process group. It ends that group on SIGINT, SIGTERM and SIGHUP and when the wall-clock cap fires, then looks for live servers still carrying the suite's run id. If it finds one, it kills it and fails the run, so the leak shows up in the run that caused it. `IMPECCABLE_SKIP_LEAK_CHECK=1` bypasses the check.

To sweep leftovers from an earlier run of this checkout:

```bash
bun run test:cleanup
```

`tests/live-server-leak.test.mjs` pins the guarantee against the real binary: it boots a live server, SIGKILLs the process that started it, and fails if the server outlives it.

## Rules that keep the sweep safe

- **Kill by marker only.** Every sweep is scoped by an environment marker this harness exported (`IMPECCABLE_TEST_RUN_ID`, `IMPECCABLE_TEST_REPO`), never by process name, port or path. A cleanup can then never reach a server that another checkout or the user's own session is running.
- **Keep marker values opaque.** Each value is a random token or a hash of the checkout path, drawn from `[A-Za-z0-9_-]`. Process listings flatten the environment into one whitespace-separated line, so a value with a space could hide the end of its own entry. `assertMarkerValue` in `scripts/lib/live-server-processes.mjs` refuses such a value. The readable checkout path travels separately in `IMPECCABLE_TEST_REPO_PATH`, which nothing matches on.
- **Keep the daemon inheriting the environment.** The markers reach the detached Rust server only because its spawn passes the parent's environment through. A change that scrubs or narrows that environment blinds the guard without any test failing first.
