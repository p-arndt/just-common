# just-common

Shared [just](https://just.systems) recipes for all projects. Each project keeps a
**copy** in `.just/` (committed, so CI and other machines work) and pulls updates
from this repo with `just sync-common` — on macOS, Linux and Windows, no local
checkout needed.

| Module | Recipes | Project sets |
|---|---|---|
| `common.just` | `default`, `sync-common`, pwsh on Windows | – |
| `go.just` | `run build build-release test vet fmt fmt-check lint ci clean` | `BIN_NAME`, `BUILDINFO_PKG` (opt. `MAIN`) |
| `rust.just` | `run build build-release check test fmt fmt-check clippy/lint ci clean` | opt. `CARGO_SCOPE`, `RUN_PKG` |
| `android.just` | `emulator android apk apk-release test logcat clean doctor` | `APP_ID` (opt. `GRADLE_MODULE`, `ACTIVITY`, `AVD`, `APK_DIR`) |
| `docker.just` | `image push up down logs ps` | `IMAGE` (opt. `REGISTRY`, `DOCKERFILE`, `TARGET`, `COMPOSE`) |
| `release.just` | `version set-version release release-dry prerelease note changelog` (via `stamp`) | – |

## Adopt in a project

Download the modules the project needs into `.just/`:

```sh
mkdir -p .just
for m in common go release; do curl -fsSL "https://raw.githubusercontent.com/p-arndt/just-common/main/$m.just" -o ".just/$m.just"; done
```

```powershell
New-Item -ItemType Directory -Force .just | Out-Null
foreach ($m in 'common','go','release') { Invoke-WebRequest -UseBasicParsing "https://raw.githubusercontent.com/p-arndt/just-common/main/$m.just" -OutFile ".just/$m.just" }
```

Then the project's `justfile`:

```just
import '.just/common.just'
import '.just/go.just'
import '.just/release.just'

BIN_NAME := "hop"
BUILDINFO_PKG := "github.com/p-arndt/hop/internal/buildinfo"
```

- Override a default variable: add `set allow-duplicate-variables`, then redefine it.
- Override a recipe (e.g. `ci` with extra steps): add `set allow-duplicate-recipes`, then redefine it.
- Project-specific recipes go straight into the project's `justfile`.

## Update

Change a module here, commit, push. Then run `just sync-common` in each project
and commit the changed `.just/` files there.

- `JUST_COMMON=/path/to/just-common just sync-common` copies from a local
  checkout instead, to try changes before pushing them.
- `JUST_COMMON_REF=<tag or branch>` pins what is downloaded (default `main`).
