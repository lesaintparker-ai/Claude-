# Using Claude Code with OpenRouter

Claude Code talks to an Anthropic-compatible API. OpenRouter exposes one at
`https://openrouter.ai/api`, so you can point Claude Code at it with a few
environment variables.

Source: <https://openrouter.ai/docs/cookbook/coding-agents/claude-code-integration>

## 1. Get a key and add credits

1. Create a key at <https://openrouter.ai/settings/keys>.
2. Add credits at <https://openrouter.ai/settings/credits>. Anthropic models are
   paid per token, so any positive balance works. (The well-known "$10 top-up"
   is a different thing: it raises the daily request limit on OpenRouter's
   *free* `:free` models. It isn't a one-time unlock for Claude models.)

## 2. Set the environment variables

Option A: shell profile (`~/.bashrc`, `~/.zshrc`, ...)

```bash
export OPENROUTER_API_KEY="sk-or-v1-your-key"        # define this first
export ANTHROPIC_BASE_URL="https://openrouter.ai/api"
export ANTHROPIC_AUTH_TOKEN="$OPENROUTER_API_KEY"    # sent as Authorization: Bearer
export ANTHROPIC_API_KEY=""                          # must be explicitly empty
```

Reload with `source ~/.bashrc` (or open a new terminal).

Option B: project settings file. Copy the example and fill in your key:

```bash
cp .claude/settings.local.json.example .claude/settings.local.json
```

`.claude/settings.local.json` is gitignored, so your key stays out of the repo.

## 3. Clear any cached Anthropic login

If you were ever signed in to Claude Code with an Anthropic account, run
`/logout` inside Claude Code once, then quit and relaunch `claude`.

## 4. Verify

Inside Claude Code, run `/status`. You should see:

```
Auth token: ANTHROPIC_AUTH_TOKEN
Anthropic base URL: https://openrouter.ai/api
```

Requests also show up on <https://openrouter.ai/activity>.

## Optional: pin models

```bash
export ANTHROPIC_DEFAULT_OPUS_MODEL="~anthropic/claude-opus-latest[1m]"
export ANTHROPIC_DEFAULT_SONNET_MODEL="~anthropic/claude-sonnet-latest[1m]"
export ANTHROPIC_DEFAULT_HAIKU_MODEL="~anthropic/claude-haiku-latest"
```

Claude Code is tuned for Anthropic models. OpenRouter only guarantees
compatibility when it routes to the Anthropic first-party provider.

## Troubleshooting

- **Auth errors**: check that `ANTHROPIC_AUTH_TOKEN` holds the OpenRouter key and
  `ANTHROPIC_API_KEY` is `""`. A real Anthropic key in `ANTHROPIC_API_KEY` wins
  and sends traffic to Anthropic directly.
- **Empty token**: if `OPENROUTER_API_KEY` is defined *after* the
  `ANTHROPIC_AUTH_TOKEN` line, the token expands to an empty string.
- **Model-not-found**: usually a leftover cached login. Run `/logout` and relaunch.
- **Don't use a `.env` file**: the native installer doesn't read it.
- **Never commit your key.** If one leaks, revoke it at
  <https://openrouter.ai/settings/keys>.
