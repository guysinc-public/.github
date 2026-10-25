# .github
The Guys Inc organisation profile, and the files every public repository here inherits.

## Shared workflows

Reusable workflows that a repository in any Guys Inc organisation can call (Branding-Standards ADR 0023). Callers pin a `vX.Y.Z` tag of this repository.

| Workflow | What it does | Reference |
|---|---|---|
| `deploy-site` | Publishes a static website over SSH to a host receiver that locks each key to one domain, and rolls it back. | [docs/reference/deploy-site.md](docs/reference/deploy-site.md) |
