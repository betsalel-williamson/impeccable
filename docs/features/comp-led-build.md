# Comp-led build

This page states how a build follows an approved [comp](../glossary/comp.md), which gates `impeccable build-phase` enforces, and who may waive them. It is for anyone reading a comp-led run, its state files or its gate messages.

A comp-led build turns an approved comp image into code under measurement. Models believe their HTML, CSS and SVG recreation of an image succeeded when it did not, so the build runs as a state machine on disk whose gates compare the rendered screen with the comp. The procedure the agent follows is section 6 of [`skill/reference/new-work.md`](../../skill/reference/new-work.md).

## When a build is comp-led

The [build path](../glossary/build-path.md) decides it. Operate and Read surfaces are code-led whatever the project records ([Modes](modes.md)). For Persuade and Experience surfaces, `buildPath` in `.impeccable/config.json` (or `config.local.json`) sets the default; with nothing recorded the round is comp-led whenever image generation exists. Without image generation every build is code-led. The decision page's toggle can flip one round, and locking a comp card on that page makes it the approved comp. The skill text also asks the agent to say so and take the code-led path when the model in hand is below the frontier tier.

## The phases

`build-phase start` writes `.impeccable/build/state.json`. Only `build-phase advance` moves it, one phase at a time, in the order `PHASES` sets in `crates/comp-verbs/src/build_phase.rs`:

| Phase | The gate closes when |
|---|---|
| comps | the comp round produced its options and one carries approval |
| spec | `comp-spec` measured the comp into `.impeccable/build/spec.json`, every salient region named, the lead text region measured and ranked by `font-match` |
| plates | every raster region has a plate that decodes, is large enough and scores against its comp crop |
| plan and asset review | the user accepted the plates and the plan for code-drawn regions ([docs/PLAN-REVIEW.md](../PLAN-REVIEW.md)) |
| hero | the first-viewport reproduction scores at least 0.72 against the comp, with no hard veto |
| sections, motion, responsive | each phase's own checks pass; responsive reads at 0.65 |
| review | the finish review |

Exit 2 means a gate failed and printed why.

## Guarantees

**The spec is the inventory.** Anything not named in the spec does not exist on the page. `comp-spec` refuses a code-drawn region larger than a quarter of the comp, and refuses a code kind whose note describes painted material.

**Plates are generated, never cropped.** A plate is regenerated from its comp crop at asset resolution. The plates gate refuses a resample of the comp region and scores each plate against its crop (minimum 0.4 overall and 0.4 on structure).

**Some failures are vetoes at any score.** A missing region, a contradicted plate or text region, an inline-SVG illustration, a clipped plate or invented ink fails the hero whatever the overall number. Above the bar, the numeric readings are advisory.

**Only the user can downgrade the comp.** `build-phase advance --force` requires a reason that quotes the user's own words downgrading the comp's authority. Generic delegation or a brief line is refused, and a waiver binds to the exact asset state it covered. The parser cannot authenticate a quote, so the harness must keep the real answer; [docs/COMP-GATE-INTEGRITY.md](../COMP-GATE-INTEGRITY.md) sets out this limit and the plate receipts.

**Review approvals carry forward only while nothing changed.** The plan and asset review pins the spec digest and plate hashes; a changed spec or replaced plate needs a new round. Approval of the assembled first viewport ends component review for that build, and `build-phase start --reset` starts a new review journey.

## The verbs

`comp-spec`, `comp-diff`, `font-match` and `build-phase` live in `crates/comp-verbs` over the pure image libraries in `crates/comp`. `comp-diff` scores structure, color and detail per region and writes paired crops and a side-by-side. The `comp-*` and `build-*` oracle cases and the integrity tests in `crates/comp-verbs/src/build_phase/integrity_tests.rs` pin the gates. Native capture of the build is described in [docs/NATIVE-CAPTURE.md](../NATIVE-CAPTURE.md); the measurement history behind the thresholds is in [docs/COMP-FIDELITY.md](../COMP-FIDELITY.md).
