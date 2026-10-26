# pr-review

One PR-Agent review workflow for all my repos. Each repo calls it with a
short file, so changing reviewer models happens here once.

## Use it in a repo

1. Copy `caller/pr-review.yml` to `.github/workflows/pr-review.yml`.
2. Add an `OPENROUTER_API_KEY` secret. Repos in the `varholdt` org get it from
   the org.
3. Put the repo's review rules under `## Review guidelines` in its AGENTS.md.
   Each reviewer gets that section as extra instructions. Codex's GitHub
   review reads the same section, so the rules live in one place.

Every reviewer runs in parallel on each push and posts or updates its own
comment. `/review` in a comment reruns them. `/describe` and `/ask <question>`
run only when someone asks. Nothing rewrites the PR body on its own.

## Change models

Edit the `reviewers` default in `.github/workflows/review.yml`. Every repo on
`@main` picks it up on its next PR. A repo can pass its own `reviewers` list
instead.

Each reviewer is an object:

| Field | Required | Meaning |
| --- | --- | --- |
| `id` | yes | Short name. Keys the PR comment, so keep it stable. |
| `label` | yes | Shown in the check name and comment heading |
| `model` | yes | LiteLLM model name, like `openrouter/deepseek/deepseek-v4.1-flash-1017` |
| `reasoning_effort` | no | Passed to OpenRouter |
| `max_input_tokens` | no | Default 900000 |
| `max_output_tokens` | no | Default 32768 |
| `provider_order` | no | JSON string like `"[\"z-ai\"]"`. A preference, not a hard limit. |

## Client code

`provider_order` doesn't stop OpenRouter from falling back to other providers.
For a client whose code can't go to just any provider, use only models the
client approved, and use an OpenRouter key whose account settings allow only
zero-data-retention providers. The repo's AGENTS.md lists the approved
providers.
