# Artifact drift

Artifact drift is the gap between a file Impeccable wrote into a project and what the current version expects. There are three kinds. Tool version drift means a newer skill is published, reported as `UPDATE_AVAILABLE`. Schema drift means a file carries retired fields, lacks expected ones or sits in a retired location; `impeccable context` reports a cheap subset at boot as one `CONTEXT_STALE` directive, and `/impeccable doctor` runs the full check. Truth drift means the code moved on and the document no longer describes it; `init` and `document` own that rewrite.

Not to be confused with design drift, which the [detector](anti-pattern-rule.md) flags in code that departs from the project's design system.

See [Artifact staleness](../features/artifact-staleness.md). Doctor's procedure is in [doctor.md](../../skill/reference/doctor.md).
