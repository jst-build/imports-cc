# Library `libgit2` for the `jst` build system

Target definitions for building `libgit2` from source. By default, this
library is built with the system toolchain and system dependencies `zlib`
and `ssl`. To use different ones see [Repository
Remapping](#repository-remapping) below.

To obtain a local installation, run:
```
jst install -o .local
```

For a local installation with bundled dependencies, run:
```
jst -C bundled.json install -o .local
```

## How to import this Repository

To import `libgit2` to your repository, add the following code to the
*imports section* of your `repos.in.json` and run `jst-lock` to generate the
final repository lock-file

```jsonc
"imports": [
  {
    "source": "git",
    "branch": "libgit2/v1.7.2",
    "url": "https://github.com/jst-build/imports-cc",
    "repos": [{"alias": "libgit2"}]
  },
  // ...
],
```

## Provided Targets

The provided targets are either producing an installation directory
(`{bin,lib,include}`) or a build dependency target, to be consumed via the
`deps` field of `//CC:binary` and `//CC:library` rules.

Available targets are:

- `ALL`/`INSTALL`: Installs the libgit2 library into an installation-style
  tree.
- `git2`: The Git linkable library, for consumption as a build
  dependency.

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
| `ADD_LDFLAGS` | Additional linker flags. |
| `AR` | The archiver to use. |
| `DWP` | The DWARF format packaging tool to use. Required by debug builds that enable debug fission. |
| `ENV` | Map from strings to strings. The build environment to be used for build actions. Typically used to include an unusual value of `PATH`. |
| `LOCALBASE` | Use this localbase for building against system libs (e.g., `"/usr"`). |
| `PKG_CONFIG_ARGS` | Additional `pkg-config` arguments (e.g. `"--define-prefix"` or `"--static"`) |
| `USE_SYSTEM_LIBS` | Boolean. Global switch consumed throughout this project's own targets: if set, optional dependencies (zlib, ssl, ...) are looked up under their system (`"system"`) name rather than their bundled/vendored (`"open"`) name. |

> This list is generated — run `jst describe` for the always-current,
> authoritative version.

### Feature flags

libgit2 exposes a number of additional `USE_*` flags to select optional
backends and platform capabilities (e.g. `USE_THREADS`, `USE_SSH`,
`USE_HTTPS`, `USE_GSSAPI`, `USE_ICONV`, `USE_BUNDLED_ZLIB`,
`REGEX_BACKEND`), mirroring upstream libgit2's own `CMakeLists.txt`
options.

## Repository Remapping

This repository can be imported with its dependencies remapped:

- `toolchain`: The toolchain used to build libgit2. (default: [system
  toolchain](https://github.com/jst-build/toolchains-cc/blob/system/README.md))
- `zlib`: The zlib dependency.
- `ssl`: The SSL dependency.

> Note that if you remap the dependencies with libraries built from source, it
> is recommended to build them with the same toolchain (see [bundled.in.json](./bundled.in.json)).

**Example:** Import with a different toolchain and zlib dependency:

```jsonc
"imports": [
  { // ...
    "repos": [{
      "alias": "libgit2",
      "map": {
        "toolchain": "my-custom-toolchain",
        "zlib": "my-zlib-built-with-mycustom-toolchain"
      }
    }]
  }
  // ...
]
```