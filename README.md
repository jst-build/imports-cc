# Library `zstd` for the `jst` build system

Target definitions for building Zstandard (`zstd`) from source. By default,
this library is built with the system toolchain. To use a different
toolchain see [Repository Remapping](#repository-remapping) below.

To obtain a local installation, run:
```
jst install -o .local
```

## How to import this Repository

To import `zstd` to your repository, add the following code to the
*imports section* of your `repos.in.json` and run `jst-lock` to generate the
final repository lock-file

```jsonc
"imports": [
  {
    "source": "git",
    "branch": "zstd/v1.5.7",
    "url": "https://github.com/jst-build/imports-cc",
    "repos": [{"alias": "zstd"}]
  },
  // ...
],
```

## Provided Targets

The provided targets are either producing an installation directory
(`{bin,lib,include}`) or a build dependency target, to be consumed via the
`deps` field of `//CC:binary` and `//CC:library` rules.

Available targets are:

- `ALL`/`INSTALL`: Installs the Zstandard library, headers, and
  command-line tool into an installation-style tree.
- `zstd`: Target for consuming the Zstandard linkable compression library
  as a build dependency.
- `zstd_cli`: The `zstd` command-line compression tool.
- `TESTS`: Runs the test suite

> `zstd_cli` is not built with `.gz`/`.xz`/`.lz4` passthrough support:
> matches upstream's own graceful fallback when `zlib`/`liblzma`/`liblz4`
> aren't found at configure time, since this branch doesn't wire up those
> optional dependencies.

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
| `LDFLAGS` | The linker flags to use. |
| `ADD_LDFLAGS` | Additional linker flags. |
| `BUILD_POSITION_INDEPENDENT` | Build as position independent code. |
| `ZSTD_BUILD_SHARED` | Boolean. Default `false`. Build a shared (`*.so`) library instead of a static (`*.a`) one. |
| `AR` | The archiver to use. |
| `ENV` | Map from strings to strings. The build environment to be used for build actions. Typically used to include an unusual value of `PATH`. |
| `LOCALBASE` | Use this localbase for building against system libs (e.g., `"/usr"`). |
| `PKG_CONFIG_ARGS` | Additional `pkg-config` arguments (e.g. `"--define-prefix"` or `"--static"`) |
| `ZSTD_LEGACY_SUPPORT` | Boolean. Default `true`. Decode legacy zstd formats (v0.1 through v0.5; matches upstream's own default legacy level of 5). |
| `ZSTD_MULTITHREAD_SUPPORT` | Boolean. Default `true`. Enable multi-threaded compression support. |
| `ZSTD_BUILD_COMPRESSION` | Boolean. Default `true`. Include the compression module. |
| `ZSTD_BUILD_DECOMPRESSION` | Boolean. Default `true`. Include the decompression module. |
| `ZSTD_BUILD_DICTBUILDER` | Boolean. Default `true`. Include the dictionary-builder module. |
| `ZSTD_BUILD_DEPRECATED` | Boolean. Default `false`. Include the deprecated buffered-API wrappers. |

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
      "alias": "zstd",
      "map": {"toolchain": "my-custom-toolchain"}
    }]
  }
  // ...
]
```
