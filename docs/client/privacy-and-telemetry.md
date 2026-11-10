# Privacy and telemetry

This page lists every network request Impeccable makes from your machine, what each carries, and how to turn off the parts you can. It is for anyone who has to answer "what does this tool send?" for their team.

Impeccable never sends your project name, files, code or `PRODUCT.md` to impeccable.style. The detector and the design hook work on local files only.

## What goes over the network

| Request | When | Goes to |
|---|---|---|
| Engine download | During `npx impeccable install` or `update`, or the first run of a version when no binary is already present | GitHub Releases for the Impeccable repository |
| Skill bundle | `npx impeccable install`, `update` and `check` | impeccable.style |
| Command list | `npx impeccable help` | impeccable.style |
| Version check | At most once a day, at the start of an agent session | impeccable.style |
| Direction roll | When a new design starts and Impeccable deals visual directions | impeccable.style |
| Choice ping | After you pick one of those directions | impeccable.style |
| URL scan | `npx impeccable detect <url>` | The URL you named, in your local browser |
| Image generation | When the agent makes a comp image through the API fallback because it has no image tool of its own, and `OPENAI_API_KEY` is set | The OpenAI Images API, or `OPENAI_BASE_URL`, with your key and a prompt the agent wrote |

The engine download is checked against a checksum list that ships with the skill and is refused when it does not match. Both the launcher and the npm package fail closed on a missing or wrong checksum.

## Telemetry: the roll and the choice

When you start new design work, the skill fetches a set of visual directions from the impeccable.style catalog (the [concept seed](../glossary/concept-seed.md)). That request is the feature, so it happens whether telemetry is on or off.

Telemetry is the anonymous record of which directions were dealt and which one you picked. The roll request and the choice ping carry a random seed key, the scope, the [mode](../glossary/mode.md), the round, the catalog revision, the kind and catalog id of the winning card, and the client version. They never carry your project's names or code. Both requests name the client version in an `X-Impeccable-Client` header.

With telemetry off, the roll still runs and tells the server not to record it (the `X-Impeccable-Telemetry: off` header), and no choice ping is sent.

The raw seed key stays in `.impeccable/questions/`, which ignores itself in git. Files you commit, such as a surface brief, hold only a salted hash of it. The full list of what is sent is on the [privacy page](https://impeccable.style/privacy).

## Turn telemetry off

Any one of these turns it off, and nothing turns it back on over another:

```bash
npx impeccable telemetry off
```

- `npx impeccable telemetry off` writes `"telemetry": false` to `~/.impeccable/config.json`, for every project on this machine. `telemetry on` removes it.
- `DO_NOT_TRACK` or `IMPECCABLE_NO_TELEMETRY` set to any value other than empty, `0` or `false`.
- `"telemetry": false` in a project's `.impeccable/config.json` turns it off for everyone who works in that project. Put it in `.impeccable/config.local.json` to turn it off only for you.

`npx impeccable telemetry status` says whether telemetry is on and, when it is off, which setting turned it off.

## Turn the version check off

Set `"updateCheck": false` in `.impeccable/config.json`, or `IMPECCABLE_NO_UPDATE_CHECK=1` in the environment. You then hear about new versions only when you run `npx impeccable check`.

How the roll and the ledger behind it work is in the [telemetry feature page](../features/telemetry.md).
