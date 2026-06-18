# Documentation style guide

This repo is the source for [Inferoute documentation on GitBook](README.md). Pages are **user guides** — written for people using the dashboard, API, or provider client.

## Examples

Use generic, fictional names so examples do not look like someone's private account:

- Clusters: **inferoute-cluster1**, **inferoute-cluster2**
- Models: real public model IDs are fine (e.g. `Qwen/Qwen2.5-7B-Instruct`)
- Prefix with **“For example,”** when illustrating a scenario

Do not use real cluster names, emails, or API keys in published docs.

## Voice

- Address the reader as **you**
- Bold dashboard labels: **Clusters**, **Settings**, **Save**
- Prefer short steps and tables over long prose

## Scope by folder

| Folder | Purpose |
|--------|---------|
| `consumer/` | Spending caps, consumer API usage |
| `provider/` | Provider dashboard (clusters, pricing, settings) |
| `provider-client/` | Installing and running the provider client |

Keep implementation details (database tables, microservice names) out of these guides. Technical architecture belongs in **inferoute-node** `documentation/technical.md`.

## New pages

1. Add the file under the right folder.
2. Add an entry to `SUMMARY.md` (GitBook navigation).
3. Link related pages at the bottom of the article.

## Cursor agents

Editor rules for this repo live in `.cursor/rules/1000-documentation-style.mdc`.
