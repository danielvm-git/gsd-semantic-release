# npm publish from GitHub Actions (`EOTP`)

If `npm publish` fails in CI with **`npm error code EOTP`** / *This operation requires a one-time password*, the `NPM_TOKEN` repository secret is not a token type npm allows for **non-interactive** publishes.

## Fix

1. On [npmjs.com](https://www.npmjs.com/) go to **Access Tokens** → **Generate New Token** → **Classic token**.
2. Choose type **Automation** (intended for CI/CD; it can publish without an interactive OTP when 2FA is enabled).
3. Copy the token value.
4. In GitHub: **Settings → Secrets and variables → Actions →** update **`NPM_TOKEN`** with that value.

Alternatively, use a **Granular Access Token** with permission to publish `@danielvm/gsd-semantic-release` and publishing enabled for automation (per npm’s granular token UI).

## Retry after a failed Release `rc` job

The workflow bumps the pre-release tag (`1.37.0-rc.1`, then `rc.2`, …). After fixing the secret, re-run **Release** with **action `rc`** and the same **minor/major version** (e.g. `1.37.0`). Do **not** re-run `create` if the `release/1.37.0` branch already exists.

Example (fork repo):

```bash
gh workflow run Release -R danielvm-git/gsd-semantic-release \
  -f action=rc -f version=1.37.0 -f dry_run=false
```

When pre-releases look good, run **finalize** with the same version to publish `latest`.

## Local publish (optional)

If you publish from your machine with `npm publish --access public`, npm may prompt for **OTP** from your authenticator:

```bash
npm publish --access public --otp=123456
```
