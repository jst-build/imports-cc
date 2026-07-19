# Library `absl` for the `jst` build system

Target definitions for building `absl` (Abseil) from source.  By default, this
library is built with the system toolchain. To use a different toolchain see
[Repository Remapping](#repository-remapping) below.

To obtain a local installation, run:
```
jst install -o .local
```

## How to import this Repository

To import `absl` to your repository, add the following code to the
*imports section* of your `repos.in.json` and run `jst-lock` to generate the
final repository lock-file

```jsonc
"imports": [
  {
    "source": "git",
    "branch": "absl/v20240722.0",
    "url": "https://github.com/jst-build/imports-cc",
    "repos": [{"alias": "absl"}]
  },
  // ...
],
```

## Provided Targets

The provided targets are either producing an installation directory
(`{bin,lib,include}`) or a build dependency target, to be consumed via the
`deps` field of `//CC:binary` and `//CC:library` rules.

There is one target per Abseil library (roughly 100 in total). A few of
the most commonly consumed ones:

- `ALL`/`INSTALL`: Installs all publicly-consumed Abseil libraries and
  headers into an installation-style tree.
- `base`: Low-level runtime support (call_once, cycleclock, spinlock,
  thread identity).
- `strings`: String utilities (StrCat, StrSplit, StrJoin, numbers, ascii,
  etc.).
- `synchronization`: `absl::Mutex`, `Notification`, and related
  primitives.
- `time`: `absl::Time` and `absl::Duration` with time-zone support.
- `status`: `absl::Status` error-carrying type.
- `flags`: `absl::Flag` definition and access (`ABSL_FLAG`).
- `log`: The `LOG` family of logging macros.
- `hash`: The `absl::Hash` framework and hashing of common types.
- ...and many more — see [`targets/TARGETS`](./targets/TARGETS) for the
  complete, always-current list of target names and their `doc`-strings.

## General Configuration

|Variable|Description|
|-|-|
| `OS` | Operating system to build for. |
| `ARCH` | The underlying architecture. Taken as a default for `HOST_ARCH` and `TARGET_ARCH`. |
| `HOST_ARCH` | The architecture on which the build actions are carried out. |
| `TARGET_ARCH` | The architecture for which to build the binary. |
| `TOOLCHAIN_CONFIG` | The toolchain configuration. Use field `FAMILY` to specify the compiler family. |
| `DEBUG` | Map enabling and configuring the debug version. Specify a map that is logically true (e.g., non-null, non-empty map) to enable debug mode. |
| `CC` | The C compiler to use. |
| `CFLAGS` | The C compiler flags to use. |
| `ADD_CFLAGS` | Additional C compiler flags. |
| `CXX` | The C++ compiler to use. |
| `CXXFLAGS` | The C++ compiler flags to use. |
| `ADD_CXXFLAGS` | Additional C++ compiler flags. |
| `AR` | The archiver to use. |
| `DWP` | The DWARF format packaging tool to use. Required by debug builds that enable debug fission. |
| `PATCH` | The patch tool used to apply the bundled `options.h` patch. |
| `ENV` | Map from strings to strings. The build environment to be used for build actions. Typically used to include an unusual value of `PATH`. |
| `LOCALBASE` | Use this localbase for building against system libs (e.g., `"/usr"`). |
| `PKG_CONFIG_ARGS` | Additional `pkg-config` arguments (e.g. `"--define-prefix"` or `"--static"`) |
| `USE_SYSTEM_LIBS` | If set, prefer system libraries. This project reads it in the random seed generator to link the system `bcrypt` (Windows only) instead of a bundled dependency. Has no effect on other platforms. |

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
      "alias": "absl",
      "map": {"toolchain": "my-custom-toolchain"}
    }]
  }
  // ...
]
```
