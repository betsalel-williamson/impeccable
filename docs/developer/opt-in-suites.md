# Opt-in test suites

Which test suites stay out of `bun run test`, what each one needs, and which change owes a local run of it. A reference for contributors whose change touches an area the default suite does not cover. The canonical mapping is the `triggers` list of each suite in `scripts/test-suites.mjs`; this page names the cases that need a person to start the run.

## The suites

| Script | Covers | Needs |
|---|---|---|
| `bun run test:live-e2e` | Full [live mode](../glossary/live-mode.md) cycle (pick, generate, accept, cleanup) against every framework fixture with a `runtime` block. | Playwright Chromium (`npx playwright install chromium` once), real `npm install` per fixture, an engine binary. |
| `bun run test:new-work-e2e` | The decision page and review pages, plus the offline fake image generator. | Playwright; no API cost. |
| `bun run test:live-e2e-accept-cleanup` | Post-accept cleanup with a real model. | Playwright and a provider API key; billed. |
| `bun run test:live-svelte-adapter-deepseek` | The Svelte adapter browser sweep. | Playwright and `DEEPSEEK_API_KEY`; billed. |
| `bun run test:skill-behavior` | Whether the skill text drives an agent's setup and routing, asserted on the tool-call trace. | Provider keys; billed. |
| `bun run test:skill-activation` | Whether the skill description triggers on UI work and stays quiet otherwise. | Anthropic and Google keys; billed. |
| `bun run test:skill-workflow` | Completed end-to-end workflows in a real browser. | Playwright and a provider key; billed. |

`bun run test:cli-remote-e2e` is registered but holds no tests.

## What a change owes

| You touched | Run |
|---|---|
| `skill/scripts/live-browser*.js` | `test:live-e2e` |
| `skill/SKILL.src.md` Setup, routing, or a Setup-adjacent reference file | `test:skill-behavior` |
| The frontmatter `description` in `skill/SKILL.src.md` | `test:skill-activation` |
| `ENGINE_VERSION` | `test:live-e2e`, `test:new-work-e2e`, `test:live-e2e-accept-cleanup`, `test:live-svelte-adapter-deepseek`, `test:skill-behavior` |
| `plugin/`, `skill/agents/`, `scripts/build.js`, the plugin manifest validator | `test:plugin-e2e` (already in the default suite; needs the `claude` CLI on PATH) |

CI runs some of these when their triggers match and the secrets exist, but the skill-behavior job never runs on a pull request. Run the matching suite yourself before you ask for review.

## Iterate on one case

```bash
IMPECCABLE_E2E_ONLY=<fixture> bun run test:live-e2e       # one or more fixtures, comma separated
IMPECCABLE_E2E_DEBUG=1 bun run test:live-e2e              # page DOM and dev-server tail on failure
IMPECCABLE_SKILL_BEHAVIOR_MODELS=<model> bun run test:skill-behavior
IMPECCABLE_SKILL_BEHAVIOR_VERBOSE=1 bun run test:skill-behavior   # per-scenario trace JSON
```

Live E2E uses a deterministic fake agent by default. `IMPECCABLE_E2E_AGENT=llm` swaps in the model-backed agent in `tests/live-e2e/agents/llm-agent.mjs`; `IMPECCABLE_E2E_LLM_PROVIDER`, `IMPECCABLE_E2E_LLM_MODEL` and `IMPECCABLE_E2E_LLM_EFFORT` choose the provider, model and reasoning effort.

## Keys

Provider keys live in `~/.config/impeccable/dev.env` (mode 0600), or the file `IMPECCABLE_DEV_KEYS` names; a real environment variable wins. A provider whose key is missing skips instead of failing. The model lineup for skill-behavior is `DEFAULT_MODELS` in `tests/skill-behavior/providers.mjs`, and its scenarios and baseline are in [tests/skill-behavior/README.md](../../tests/skill-behavior/README.md). Update that table in the same change as the scenarios.

New framework fixtures follow [tests/framework-fixtures/README.md](../../tests/framework-fixtures/README.md).
