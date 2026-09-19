# just-common

Shared [just](https://just.systems) recipes for all projects. Each project keeps a
**copy** in `.just/` (committed, so CI and other machines work) and pulls updates
with `just sync-common`.

| Module | Recipes | Project sets |
|---|---|---|
| `common.just` | `default`, `sync-common`, pwsh on Windows | – |
| `go.just` | `run build build-release test vet fmt fmt-check lint ci clean` | `BIN_NAME`, `BUILDINFO_PKG` (opt. `MAIN`) |
| `rust.just` | `run build build-release check test fmt fmt-check clippy/lint ci clean` | opt. `CARGO_SCOPE`, `RUN_PKG` |
| `android.just` | `emulator android apk apk-release test logcat clean doctor` | `APP_ID` (opt. `GRADLE_MODULE`, `ACTIVITY`, `AVD`, `APK_DIR`) |
| `docker.just` | `image push up down logs ps` | `IMAGE` (opt. `REGISTRY`, `DOCKERFILE`, `TARGET`, `COMPOSE`) |
| `release.just` | `version set-version release release-dry prerelease note changelog` (via `stamp`) | – |

## Adopt in a project

```sh
mkdir -p .just && cp ~/coding/just-common/{common,go,release}.just .just/
```

```just
import '.just/common.just'
import '.just/go.just'
import '.just/release.just'

BIN_NAME := "hop"
BUILDINFO_PKG := "hop/internal/buildinfo"
```

- Override a default variable: add `set allow-duplicate-variables`, then redefine it.
- Override a recipe (e.g. `ci` with extra steps): add `set allow-duplicate-recipes`, then redefine it.
- Project-specific recipes go straight into the project's `justfile`.

## Update

Edit the module here, commit, then run `just sync-common` in each project and commit
the changed `.just/` files there. Set `JUST_COMMON` if this checkout lives elsewhere.
