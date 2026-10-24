# Oft3r fork of VSCodroid

Personal fork of [rmyndharis/VSCodroid](https://github.com/rmyndharis/VSCodroid).
It builds and publishes everything from this repository, so it keeps working if
upstream stops.

## What differs from upstream

| Where | Change |
| --- | --- |
| `android/app/build.gradle.kts` | `applicationId` is `com.oft3r.vscodroid` (installs beside the Play build) and `BuildConfig.RELEASES_REPO` is `Oft3r/VSCodroid`. Both overridable with `-Pvscodroid.applicationId=` / `-Pvscodroid.releasesRepo=` |
| `setup/ToolchainRegistry.kt` | Ruby, Java and C/C++ packs download from `RELEASES_REPO` releases |
| `MainActivity.kt`, `branding/product.json` | Source, issue and licence links point at this fork |
| `scripts/fetch-vscode-oss.sh` | Server tarball comes from `$GITHUB_REPOSITORY`, else this fork |
| `scripts/deploy.sh`, `device-launch.sh`, `device-test.sh` | Default package is `com.oft3r.vscodroid.debug` |

Still fetched from upstream: the Python wheel index (numpy, pandas, psutil,
pydantic_core) at `rmyndharis.github.io/VSCodroid/wheels/`, used only by `pip`.

## Release flow

1. **Server tree**, once per VS Code version: Actions, *Build Code - OSS server*,
   Run workflow. Publishes the `server-<VSCODE_VERSION>` release here.
2. **App**: bump `versionCode` / `versionName` in `android/app/build.gradle.kts`,
   then `git tag vX.Y.Z && git push origin vX.Y.Z`. `release.yml` signs it with the
   `KEYSTORE_BASE64`, `KEYSTORE_PASSWORD`, `KEY_ALIAS` and `KEY_PASSWORD` secrets
   and publishes the APK and toolchain packs as the latest release.

## Following upstream

```bash
git fetch upstream
git merge upstream/main          # conflicts, if any, are in the files above
```

If upstream bumped `VSCODE_VERSION`, run step 1 before tagging a release.

## Moving VS Code forward without upstream

1. Set `VSCODE_VERSION` to the new tag and `VSCODE_COMMIT` to its SHA:
   `git ls-remote https://github.com/microsoft/vscode.git 'refs/tags/<tag>'`
2. Run *Patch drift* (Actions) to see which `patches/` no longer apply, and fix them.
3. Release flow above.
