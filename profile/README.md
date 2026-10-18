<img src="guysinc-public-banner.svg" alt="Guys Inc" width="100%">

Small, well-made software from Guys Inc. Most of it is given away here.

## What lives here

- Public products: the things Guys Inc releases, for anyone who wants to run them.
- `website`, the one private repository here: the Guys Inc site at guysinc.pub.
- Not here: the platform behind them (GICloud), the family's software (The House), forks of other people's work (Guys Inc Forks).

## Start here

| Repository | What it is |
|---|---|
| [archivist](https://github.com/guysinc-public/archivist) | Turn a directory of .deb files into a signed, verifiable apt repository on your own domain: one static binary, S3-compatible storage, no SaaS. |
| [Brazier](https://github.com/guysinc-public/Brazier) | A companion iPhone app for self-hosted Grafana: real push for firing and resolved alerts, sign-in the way self-hosters run Grafana, dashboards on a phone. |
| [cync-homeassistant](https://github.com/guysinc-public/cync-homeassistant) | A drop-in replacement for Home Assistant's built-in Cync integration: sign-in that survives token rotation, and discovery of shared homes. |

## How a repository gets in

A repository is created, moved or archived by a pull request to [guysinc-cloud/estate](https://github.com/guysinc-cloud/estate), in `orgs/guysinc-public/repos.yaml`. Nothing is created by hand; one made in GitHub's interface is drift, and the weekly plan reports it.

## The other organisations

| Organisation | Holds |
|---|---|
| [GICloud](https://github.com/guysinc-cloud) | The platform |
| [The House](https://github.com/guysinc-home) | Family production |
| [Guys Inc Labs](https://github.com/guysinc-labs) | What has no production yet |
| [Guys Inc Forks](https://github.com/guysinc-forks) | Forks, kept as upstream has them |
| [Guys Inc Attic](https://github.com/guysinc-attic) | The retired: the old `GuysInc` organisation, every repository archived |

`GuysInc` is reserved: it becomes the login of Guys Inc (`guysinc-public`) in the last phase of the restructure (ADR 0026), once the attic no longer answers to it.

Runbooks, onboarding and anything that spans repositories are in [Meridian](https://meridian.gicloud.org).
