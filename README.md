# hermes-together-provider

A [Hermes Agent](https://hermes-agent.nousresearch.com) model-provider plugin
for [Together AI](https://www.together.ai) — an OpenAI-compatible inference
API with 270+ serverless open-weight models.

## Install

```bash
hermes plugins install pepijndevos/hermes-together-provider
```

Or manually: drop `__init__.py` and `plugin.yaml` into
`$HERMES_HOME/plugins/together/` (flat, matching the `kind: model-provider`
discovery rule — see the [Model Provider Plugins](https://hermes-agent.nousresearch.com/docs/developer-guide/model-provider-plugin)
guide).

Then set your key in `~/.hermes/.env`:

```
TOGETHER_API_KEY=your-key-here
```

Get a key at <https://api.together.ai/settings/api-keys>.

## What it adds

- `together` provider (aliases `together-ai`, `togetherai`) selectable via
  `hermes model`, `--provider together`, or `/model <model-id> --provider together`.
- Live model-catalog discovery from Together's `/v1/models` endpoint — this
  plugin overrides `fetch_models()` specifically so model-switch validation
  routes through the shape-aware catalog fetch instead of a generic probe
  that assumes the OpenAI `{"data": [...]}` envelope (Together's `/v1/models`
  returns a bare JSON array; see upstream
  [hermes-agent#141782](https://github.com/NousResearch/hermes-agent/issues/141782)).
- A curated `fallback_models` list (current-generation, good price/performance
  picks) used when the live catalog is unreachable.

## Disclosure

- Network calls: only to `https://api.together.xyz/v1` (inference) and its
  `/models` endpoint (catalog discovery), both using the API key you provide.
- No credentials are read from disk directly — the API key is resolved
  through Hermes's own provider-credential routes (`TOGETHER_API_KEY` /
  `TOGETHER_BASE_URL` env vars), the same mechanism every built-in provider
  (OpenRouter, Fireworks, GMI, ...) uses.
- No telemetry, no background processes, no self-updating code.
- No core overrides — registers through the public `register_provider()` /
  `ProviderProfile` API only.

## Why standalone, not in-tree

hermes-agent's contribution policy treats third-party vendor model-provider
integrations as plugins, not core-tree additions (see
[CONTRIBUTING.md](https://github.com/NousResearch/hermes-agent/blob/main/CONTRIBUTING.md#third-party-product-integrations-ship-as-a-standalone-plugin)
and the prior [in-tree PR, closed as not-planned](https://github.com/NousResearch/hermes-agent/pull/66116)).
This repo ships it the way the policy asks for.

## License

MIT
