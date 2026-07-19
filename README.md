# Library `bzip2` for the `jst` build system

Target definitions for building `bzip2` from source. By default, this
library is built with the system toolchain. To use a different toolchain
see [Repository Remapping](#repository-remapping) below.

To obtain a local installation, run:
```
jst install -o .local
```

## How to import this Repository

To import `bzip2` to your repository, add the following code to the
*imports section* of your `repos.in.json` and run `jst-lock` to generate the
final repository lock-file

```jsonc
"imports": [
  {
    "source": "git",
    "branch": "bzip2/v1.0.8",
    "url": "https://github.com/jst-build/imports-cc",
    "repos": [{"alias": "bzip2"}]
  },
  // ...
],
```

## Provided Targets

The provided targets are either producing an installation directory
(`{bin,lib,include}`) or a build dependency target, to be consumed via the
`deps` field of `//CC:binary` and `//CC:library` rules.

Available targets are:

- `ALL`/`INSTALL`: Installs the BZip2 libraries and command-line tools
  into an installation-style tree.
- `libbz2`: Target for consuming the BZip2 linkable compression library as
  a build dependency.
- `bzip2`: The bzip2 command-line compression tool.
- `bzip2recover`: The bzip2recover data-recovery tool for damaged `.bz2`
  files.
- `bunzip2`: The bzip2 binary under its traditional decompress-mode name.
- `bzcat`: The bzip2 binary under its traditional decompress-to-stdout
  name.

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
| `AR` | The archiver to use. |
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
      "alias": "bzip2",
      "map": {"toolchain": "my-custom-toolchain"}
    }]
  }
  // ...
]
```