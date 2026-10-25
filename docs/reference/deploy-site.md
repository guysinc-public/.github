---
type: reference
owner: CJ
reviewed: 2026-10-25
review: every release of deploy-site
---

# deploy-site

The shared deploy for a static website: [`.github/workflows/deploy-site.yml`](../../.github/workflows/deploy-site.yml) on the runner, [`deploy-site/deploy-receive`](../../deploy-site/deploy-receive) on the host. How the pieces fit, adding a site, and what to do when a deploy fails are on Meridian, GICloud › *Websites on NameCrane deploy through one locked receiver*.

## Signature

```yaml
jobs:
  deploy:
    uses: guysinc-public/.github/.github/workflows/deploy-site.yml@v1.0.0
    with:
      domain: example.com
      item: op://<vault>/<repo> - NameCrane deploy
      path: site
    secrets:
      OP_SERVICE_ACCOUNT_TOKEN: ${{ secrets.OP_SERVICE_ACCOUNT_TOKEN }}
```

The calling organisation's Actions allow-list must name `guysinc-public/.github/.github/workflows/deploy-site.yml@*` and `1password/load-secrets-action@*` (both are covered by `guysinc-public/*` and `1password/*`).

## Parameters

| Name | Type | Required | Default | Meaning |
|---|---|---|---|---|
| `domain` | string | yes | | The hostname to deploy. The item's key is locked to it on the host. |
| `item` | string | yes | | The 1Password item, as `op://<vault>/<item>`. |
| `path` | string | no | `site` | The folder in the repository that is the web root. Unused when `artifact` is set. |
| `artifact` | string | no | `""` | An artifact uploaded earlier in the same run that holds a built web root. |
| `rollback` | boolean | no | `false` | Swap the previous version back instead of deploying. A second rollback swaps forward again. |
| `OP_SERVICE_ACCOUNT_TOKEN` | secret | yes | | The organisation's read-only 1Password service account. |

The job runs in the caller's `production` environment, so each deploy is listed under the repository's Deployments with a link to the site.

## The 1Password item

An **SSH Key** item, created with `--ssh-generate-key ed25519` so the private key exists nowhere else. Every value is masked in the log, the host and user included.

| Field | Holds |
|---|---|
| `private key` | Read as `?ssh-format=openssh`. |
| `public key` | The line's key in the host's `~/.ssh/authorized_keys`. |
| `hostname` | The SSH host. |
| `port` | The SSH port. |
| `username` | The hosting account. |
| `known_hosts` | The pinned host key line, `[host]:port ssh-ed25519 AAAA…`. A changed host key fails the deploy. |

## The host side

One line per key in `~/.ssh/authorized_keys`. The options are set there and never by the caller.

```text
command="/home/<user>/bin/deploy-receive --domain example.com",restrict ssh-ed25519 AAAA… <repo>
```

| Option | Effect |
|---|---|
| `--domain <fqdn>` | Required. The only site the key can deploy, roll back or list. `~/domains/<fqdn>` must already exist (DirectAdmin creates it). |
| `--allow-htaccess` | The site may ship `.ht*` files. An `.htaccess` can switch on PHP, so only for repositories nobody outside Guys Inc writes. |
| `--allow-server-code` | The site may ship PHP, CGI, SSI, `.user.ini` and a `cgi-bin/`. Every site on the account runs as one Unix user, so this is access to all of them. |

Always refused: symlinks, hard links, device files and an archive with no files. The swap keeps one version back at `public_html.prev`; a DirectAdmin `cgi-bin/` is carried over unless the site ships its own. Files are made world-readable and writable only by the account.

## Returns

```text
Shipping 13 files to guysinc.pub
deployed guysinc.pub: 13 files live; the previous version is public_html.prev
```

## Errors

| Message | Cause |
|---|---|
| `this key deploys A, not 'B'` | `domain` does not match the key's `--domain`. |
| `refused, this key may not ship .htaccess files` | The web root holds a `.ht*` file and the key has no `--allow-htaccess`. |
| `refused, this key may not ship server-side code` | A `*.php*`, `*.phtml`, `*.cgi`, `*.pl`, `*.shtml`, `*.asp*`, `*.jsp*`, `.user.ini`, `php.ini` or `cgi-bin/`. The name is listed. |
| `refused, links and special files are never deployed` | A symlink or hard link in the web root. |
| `refused, the archive holds no files` | `path` or the artifact is empty. |
| `domain not provisioned in DirectAdmin` | `~/domains/<fqdn>` does not exist on the host. |
| `no previous version … to roll back to` | No `public_html.prev` yet. |
| `Permission denied (publickey)` | The key's line is missing from `authorized_keys`. DirectAdmin's SSH Keys page can rewrite the file. |
| `Host key verification failed` | The host's key changed; compare it before updating `known_hosts`. |
| `no web root at …` | `path` does not exist in the checkout. |

## Changed in

- v1.0.0: first release. Replaces guysinc-web's own deploy, whose key could deploy any domain on the account.
