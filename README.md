# Inferoute Documentation

This repository contains the source for the **Inferoute documentation** published on GitBook.

- **GitBook:** Content can be synced via GitBook’s GitHub Sync or via the **Sync to GitBook** workflow (Git Import API; see below).
- **API Reference:** Generated from the OpenAPI 3 spec published by the [inferoute-node](https://github.com/sentnl/inferoute-node) CI; add the “OpenAPI Reference” block in GitBook and point it at that spec.

## Structure

- `overview.md` — What is Inferoute
- `provider-client/` — Provider client docs (introduction, installation, configuration, setup guides, FAQ, REST API)

## Sync to GitBook (API, no GitHub Sync app)

The workflow `.github/workflows/gitbook-sync.yml` runs on push to `main` and triggers a GitBook Git import so the space content stays in sync. No GitBook GitHub app is required.

**In this repo (inferoute-docs) → Settings → Secrets and variables → Actions:**

- **Secrets:** `GITBOOK_TOKEN` (create at [app.gitbook.com/account/developer](https://app.gitbook.com/account/developer)).
- **Variables:** `GITBOOK_SPACE_ID` — in GitBook, open your space; the URL is `https://app.gitbook.com/o/<org_id>/s/<space_id>`. Use the **space_id** (the part after `/s/`).

You can also run the workflow manually from the Actions tab (“Sync to GitBook” → Run workflow).