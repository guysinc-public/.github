---
session: 2026-10-25
repo: .github
branch: feat/deploy-site
driver: CJ, with Claude Code
outcome: open
---

# A shared deploy for static sites

## Intent

Let the new guysinc.pub, and Dan's Meade Hub site, publish to the NameCrane hosting account on every merge, without anyone outside Guys Inc getting access to the host.

## What changed

- `.github/workflows/deploy-site.yml`: a reusable workflow. It reads the site's key and host from 1Password with the organisation's read-only service account (ADR 0027), then pipes the web root over SSH as a tar. It also does rollback and deploys from a build artifact. The host, user and port are masked in the log.
- `deploy-site/deploy-receive`: the receiver, rewritten from guysinc-web's. Each key is locked to one domain in `authorized_keys`, and keys are static-only unless their line says otherwise. Links are refused, deploys take a lock, and rollback swaps the previous version back. It is installed on the host. Both existing guysinc.pub keys were locked to guysinc.pub, because the old receiver let any key deploy any domain.
- `docs/reference/deploy-site.md`.
- Verified end to end from `guysinc-public/website` on a temporary branch against a throwaway domain: from a path, from an artifact, rollback, and a wrong domain refused. No host, user or port appeared in the log. The test key, item and branch were removed.

## Rejected in session

- An enterprise-wide secret: GitHub has none, and a secret every repository can read can be printed by anyone who can push a workflow.
- The account password as the secret: it opens the panel, SSH and every site.
- Separate GitHub secrets per site: ADR 0027 keeps one Actions secret per organisation, the 1Password token.
- rsync: CageFS has none.

## Next

Tag `v1.0.0` after merge, then mark guysinc-public/website's deploy pull request ready.
