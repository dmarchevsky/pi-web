# Fork releases (dmarchevsky/pi-web)

This fork builds its own releases with `.github/workflows/fork-release.yml`. Upstream's npm and Docker publishing (`publish.yml`) and its demo site (`demo-pages.yml`) are turned off in this fork's Actions settings.

## How a release happens

1. Push to `main`, for example after merging a branch or syncing upstream.
2. `CI` (`ci.yml`) runs lint, typecheck, unit tests and e2e.
3. If CI passes, **Fork release** builds the package and creates a GitHub Release with:
   - `agegr-pi-web-<version>.tgz`
   - `pi-web.tgz` (the same file under a fixed name, so `releases/latest/download/pi-web.tgz` always points at the newest build)

You can also start a release by hand from the Actions tab (Fork release → Run workflow). A commit that already has a `dm-v*` tag is skipped.

## Versions

- Version: `<upstream version>-dm.<N>`, for example `0.15.0-dm.3`. N starts at 1 for each upstream version and goes up with every build.
- Tag: `dm-v<version>`, for example `dm-v0.15.0-dm.3`.
- `package.json` in git keeps upstream's version; CI sets the `-dm.N` version only while building. Never commit a fork version, or every upstream release will conflict.
- The app shows the `-dm.N` version. Because it is a prerelease version, pi-web's check against npm never reports an upstream update.

## Install

```bash
npm uninstall -g @agegr/pi-web   # if upstream's package is installed
npm install -g https://github.com/dmarchevsky/pi-web/releases/latest/download/pi-web.tgz
pi-web
```

To update, run the same `npm install -g` again. Settings and sessions in `~/.pi/agent/` are kept.

Don't run `pi-web update` or `npx @agegr/pi-web@latest`: both install upstream's package from npm.

## Syncing upstream

```bash
git fetch upstream
git checkout main && git merge upstream/main
git push origin main          # no --tags
```

Don't push with `--tags`. Upstream's `v*` tags would match `publish.yml` if it were turned back on.

## One-time setup

```bash
gh workflow disable publish.yml -R dmarchevsky/pi-web
gh workflow disable demo-pages.yml -R dmarchevsky/pi-web
```

GitHub turns off Actions on new forks until you enable them in the repository's Actions tab.
