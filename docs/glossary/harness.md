# Harness

A harness is the AI coding tool that loads the skill and runs the agent: Claude Code, Cursor, Codex, Gemini CLI, GitHub Copilot and others. Each harness reads skills from its own directory and supports its own hook events, which is why the build emits one skill copy per harness.

Not to be confused with a provider. A provider is the build's configuration for one harness (its config directory, placeholder values and hook format), defined in `scripts/lib/transformers/providers.js`. One harness can have more than one provider: Codex has one for its user skills and one for repository skills.

See [Generated provider output](../developer/generated-provider-output.md) and [Install](../client/install.md). Per-harness capabilities are tracked in [HARNESSES.md](../HARNESSES.md).
