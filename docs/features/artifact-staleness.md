# Artifact staleness

This page states how Impeccable notices that files it wrote into a project no longer match what the current version reads, and what it does about each kind. It is for anyone reading a `CONTEXT_STALE` directive, running `impeccable doctor`, or changing an artifact's shape.

Impeccable writes `PRODUCT.md`, `DESIGN.md` and its sidecar, `.impeccable/config.json`, surface briefs and hook manifests into user projects. A released version has to cope with what an older one left. The term for that gap is [artifact drift](../glossary/artifact-drift.md).

## Three kinds of drift

| Kind | Example | Who handles it |
|---|---|---|
| Tool version | the installed skill is older than the published one | `impeccable context` prints `UPDATE_AVAILABLE` |
| Schema | a retired field, a missing expected field, a retired location | the staleness checks below, mechanically |
| Truth | the code moved on and the document no longer describes it | the `document` and `init` commands; doctor measures a proxy and says so |

## Two tiers

**Tier 1 runs at every boot**, inside `impeccable context` (`crates/context/src/staleness.rs`). It may spend only what a boot already spends: markdown already in memory, a bounded set of file stats, and the small JSON files the boot reads anyway. No directory walks, no git, no cross-workspace sweep. Adding an expensive check here taxes every session in every project.

**Tier 2 runs on demand**, behind `impeccable doctor` (`crates/context/src/staleness_deep.rs`): git history for `DESIGN.md` drift, a per-workspace sweep, ignore lists checked against the live rule registry, hook launcher resolution.

## Findings

Every finding is data: `{ id, artifact, path, severity, summary, fix }`, so the boot directive, the text report and `doctor --json` render one set. Severity says what should happen:

| Severity | Meaning |
|---|---|
| `auto` | a mechanical migration; applied on the next write to that file, never shown to the user |
| `mention` | stated once, then work carries on |
| `route` | names the command that owns the repair |

`impeccable doctor --fix` applies only `auto` findings.

## Guarantees

**One directive, rarely.** Tier 1 emits a single `CONTEXT_STALE` directive for the whole set. `mention` and `route` findings are throttled to once every seven days per project, cached in `~/.impeccable/staleness-check.json` (`crates/context/src/staleness_notice.rs`). `auto` findings are never throttled.

**Drift is never a side quest.** The directive tells the agent to end its reply with one fixed sentence pointing at `doctor`, and not to repair anything unless the user asks. The skill states the same rule in [`skill/SKILL.src.md`](../../skill/SKILL.src.md).

**Deprecated fields are binding.** A finding for a retired field tells the agent to treat it as absent, with the reason. The retired `PRODUCT.md` fields and their reasons are `PRODUCT_DEPRECATED_SECTIONS` in `crates/context/src/artifact_schema.rs`; today that is `## Register` ([Modes](modes.md)). A bare "deprecated" makes models keep a field just in case.

**Schema stamps are schema versions.** `PRODUCT.md` carries `<!-- impeccable:product-schema N -->`; `N` changes only when the shape does, not on every release. `DESIGN.md` carries no stamp, because it follows the external design.md spec; its signals are the sidecar's `schemaVersion`, the sidecar's age, section coverage and git drift.

**It can be turned off.** `"stalenessCheck": false` in the project config, or any non-empty `IMPECCABLE_NO_STALENESS_CHECK`, disables the boot check.

The oracle cases `context-staleness-throttle`, `context-no-staleness-check-env`, `context-no-staleness-check-config`, `doctor-fix-clean` and `doctor-fix-stamps-product` pin these behaviors. The verb-level contract is the staleness and doctor entries in Part 2 of [docs/CLI-CONTRACT.md](../CLI-CONTRACT.md).
