# Library `curl` for the `jst` build system

Target definitions for building `curl` from source. By default, this
library is built with the system toolchain and system dependencies
`zlib`, `ssl`, and `c-ares`. To use different ones see [Repository
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

To import `curl` to your repository, add the following code to the
*imports section* of your `repos.in.json` and run `jst-lock` to generate the
final repository lock-file

```jsonc
"imports": [
  {
    "source": "git",
    "branch": "curl/v8.6.0",
    "url": "https://github.com/jst-build/imports-cc",
    "repos": [{"alias": "curl"}]
  },
  // ...
],
```

## Provided Targets

The provided targets are either producing an installation directory
(`{bin,lib,include}`) or a build dependency target, to be consumed via the
`deps` field of `//CC:binary` and `//CC:library` rules.

Available targets are:

- `ALL`/`INSTALL`: Installs the curl library into an installation-style
  tree.
- `curl`: Target for consuming curl as a build dependency.

## General Configuration

|Variable|Description|
|-|-|
| `OS` | Operating system to build for. |
| `ARCH` | The underlying architecture. Used as a default for `TARGET_ARCH` if that is not set. |
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
| `ENV` | Map from strings to strings. The build environment to be used for build actions. Typically used to include an unusual value of `PATH`. |
| `LOCALBASE` | Use this localbase for building against system libs (e.g., `"/usr"`). |
| `PKG_CONFIG_ARGS` | Additional `pkg-config` arguments (e.g. `"--define-prefix"` or `"--static"`) |
| `USE_SYSTEM_LIBS` | Boolean. Global switch consumed throughout this project's own targets: if set, optional dependencies (ares, zstd, nghttp2, ...) are looked up under their system (`"system"`) name rather than their bundled/vendored (`"open"`) name. |

> This list is generated — run `jst describe` for the always-current,
> authoritative version.

### Protocol and feature flags

curl exposes a large number of additional `CURL_DISABLE_*`/`ENABLE_*`/
`USE_*` flags to select which protocols, authentication methods, and
optional features (e.g. `ENABLE_ARES`, `USE_ZLIB`, `CURL_ENABLE_SSL`,
`CURL_DISABLE_FTP`, `CURL_CA_BUNDLE`) are compiled in, mirroring upstream
curl's own `CMakeLists.txt` options.

## Repository Remapping

This repository can be imported with its dependencies remapped:

- `toolchain`: The toolchain used to build curl. (default: [system
  toolchain](https://github.com/jst-build/toolchains-cc/blob/system/README.md))
- `zlib`: The zlib dependency.
- `ssl`: The SSL dependency.
- `cares`: The c-ares dependency.

> Note that if you remap the dependencies with libraries built from source, it
> is recommended to build them with the same toolchain (see [bundled.in.json](./bundled.in.json)).

**Example:** Import with a different toolchain and zlib dependency:

```jsonc
"imports": [
  { // ...
    "repos": [{
      "alias": "curl",
      "map": {
        "toolchain": "my-custom-toolchain",
        "zlib": "my-zlib-built-with-mycustom-toolchain"
      }
    }]
  }
  // ...
]
```