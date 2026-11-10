# Telemetry

This page states every network call the engine makes on its own, what each one sends, and how each is turned off. It is for anyone deciding whether to run Impeccable in a sensitive environment, or changing code that can reach impeccable.style.

Without a signed-in account, three features talk to the service, and only one of them exists to measure anything.

| Call | When | Sends | Off switch |
|---|---|---|---|
| Roll (`/api/roll`) | `concept-seed` with no local catalog | the seed key, scope, mode and roll inputs; the roll is the feature | none needed: when telemetry is off the roll still happens and carries `X-Impeccable-Telemetry: off`, and the server records nothing for it |
| Choice ping (`/api/chosen`) | after a decision round on an API-dealt roll | the seed key, scope, mode, round, pool revision, and the card kind and catalog id that won; never project names or code | telemetry off |
| Update check (`/api/version`) | `impeccable context`, cached | nothing but the request | `"updateCheck": false` in project config, or `IMPECCABLE_NO_UPDATE_CHECK` |

A signed-in account adds its own calls (sign-in, Pro dispatch, and design-health counts after a full `detect`), described in [docs/RUNTIME-ENV.md](../RUNTIME-ENV.md). The design-health upload honors the same telemetry opt-outs.

## Guarantees

**Opting out never breaks a roll.** Telemetry is off when `DO_NOT_TRACK` or `IMPECCABLE_NO_TELEMETRY` is non-empty and not `0` or `false`, or when `"telemetry": false` appears in `~/.impeccable/config.json` or in the project's `.impeccable/config.json` or `config.local.json` (`opt_out_reason` in `crates/context/src/telemetry.rs`). A project may opt everyone out; nothing opts back in over an off anywhere. With telemetry off, the engine sends and stores nothing that exists only for telemetry. `impeccable telemetry off`, `on` and `status` edit and report the user setting.

**Only dealt rolls ping.** A roll from a local catalog or a degraded roll prints no telemetry instruction and sends no choice ping.

**Every request is labeled.** Roll and choice requests carry `X-Impeccable-Client: impeccable/<version>[ <channel>]`, where the version is the installed skill's. `IMPECCABLE_TELEMETRY_CHANNEL` adds a channel word; this repository's tests and CI set it to `test`, and anything new that can reach the API keeps that tagging.

**One choice, one ping.** The decision page sends the ping itself for every resolved round, re-rolls included (`kind: "reroll"`), and marks the round in the local ledger under `.impeccable/questions/seeds/`. The agent's later `concept-seed --kind` rerun then prints `choice already recorded` instead of sending a second ping. Ledger writes are locked per file, and a round claimed by the page as `pending` makes the agent's rerun wait for it.

**Seed keys never reach a commit.** A new key is 128 bits from the OS random source, and keys live only in self-ignored runtime files under `.impeccable/questions/`. Committed files hold a `seed:` reference: 16 hex of a SHA-256 over the key and a per-user salt from `~/.impeccable/seed-salt` (`impeccable_common::seed`). Without the salt file the reference cannot be computed from a key, so the telemetry dataset alone cannot locate a committed brief. With no salt available there is no reference at all, never an unsalted one. When git already tracks runtime files, the roll warns once and writes its record to `roll.local.json`.

**Requests are short.** All API calls in one run share one budget, `IMPECCABLE_API_TIMEOUT` (default 4000 ms); `IMPECCABLE_API_URL` points them elsewhere.

The `seed-chosen-telemetry-off`, `seed-kind-assigned-telemetry-off` and `seed-chosen-api-unreachable` oracle cases pin the opt-out paths, and the unit tests in `crates/context/src/telemetry.rs` pin the header and the ledger. `impeccable telemetry status` names what is sent and links the privacy statement at `https://impeccable.style/privacy`.
