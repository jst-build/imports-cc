# Library `microsoft_gsl` for the `jst` build system

Target definitions for building `microsoft_gsl` from source. By default,
this library is built with the system toolchain. To use a different
toolchain see [Repository Remapping](#repository-remapping) below.

> This is a header-only library with no `system` variant: since it ships
> no `.pc` file upstream, there is no `pkg-config` lookup to perform.

To obtain a local installation, run:
```
jst install -o .local
```

## How to import this Repository

To import `microsoft_gsl` to your repository, add the following code to the
*imports section* of your `repos.in.json` and run `jst-lock` to generate the
final repository lock-file

```jsonc
"imports": [
  {
    "source": "git",
    "branch": "microsoft_gsl/v4.0.0",
    "url": "https://github.com/jst-build/imports-cc",
    "repos": [{"alias": "microsoft_gsl"}]
  },
  // ...
],
```

## Provided Targets

The provided targets are either producing an installation directory
(`{bin,lib,include}`) or a build dependency target, to be consumed via the
`deps` field of `//CC:binary` and `//CC:library` rules.

Available targets are:

- `ALL`/`INSTALL`: Installs the Microsoft GSL library into an
  installation-style tree.
- `gsl`: Target for consuming Microsoft GSL as a build dependency.
  Header-only; nothing is compiled when consumed.

## General Configuration

|Variable|Description|
|-|-|
| `OS` | Operating system to build for. |
| `ARCH` | The underlying architecture. Used as a default for `TARGET_ARCH` if that is not set. |
| `TARGET_ARCH` | The architecture for which to build the binary. |
| `TOOLCHAIN_CONFIG` | The toolchain configuration. Use field `FAMILY` to specify the compiler family. |
| `ENV` | Map from strings to strings. The build environment to be used for build actions. Typically used to include an unusual value of `PATH`. |
| `LOCALBASE` | Use this localbase for building against system libs (e.g., `"/usr"`). |
| `PKG_CONFIG_ARGS` | Additional `pkg-config` arguments (e.g. `"--define-prefix"` or `"--static"`) |

> This list is generated — run `jst describe` for the always-current,
> authoritative version.

## Repository Remapping

The toolchain for building this library can be changed by remapping the
`toolchain` repository during the import.

**Example:** Import with different toolchain:

```jsonc
"imports": [
  { // ...
    "repos": [{
      "alias": "microsoft_gsl",
      "map": {"toolchain": "my-custom-toolchain"}
    }]
  }
  // ...
]
```