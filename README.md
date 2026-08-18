# Library `protobuf` for the `jst` build system

Target definitions for building `protobuf` from source. By default, this
library is built with the system toolchain and system dependencies `zlib`
and `absl`. To use different ones see [Repository
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

To import `protobuf` to your repository, add the following code to the
*imports section* of your `repos.in.json` and run `jst-lock` to generate the
final repository lock-file

```jsonc
"imports": [
  {
    "source": "git",
    "branch": "protobuf/v35.1",
    "url": "https://github.com/jst-build/imports-cc",
    "repos": [{"alias": "protobuf"}]
  },
  // ...
],
```

## Provided Targets

The provided targets are either producing an installation directory
(`{bin,lib,include}`) or a build dependency target, to be consumed via the
`deps` field of `//CC:binary` and `//CC:library` rules.

Available targets are:

- `ALL`/`INSTALL`: Installs `protoc` and the protobuf libraries into an
  installation-style tree.
- `protoc`: The protobuffer compiler.
- `libprotoc`: The protobuf compiler support library (`libprotoc.a`).
- `libprotobuf`: The protobuf runtime library (`libprotobuf.a`).
- `libprotobuf_lite`: The lite (reduced-footprint) protobuf runtime
  library (`libprotobuf-lite.a`).

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
| `ENV` | Map from strings to strings. The build environment to be used for build actions. Typically used to include an unusual value of `PATH`. |
| `LOCALBASE` | Use this localbase for building against system libs (e.g., `"/usr"`). |
| `PKG_CONFIG_ARGS` | Additional `pkg-config` arguments (e.g. `"--define-prefix"` or `"--static"`) |
| `PROTOBUF_BUILD_SHARED` | Boolean. Build shared (`*.so`) libraries instead of static (`*.a`) ones. Default `false`. |

> This list is generated — run `jst describe` for the always-current,
> authoritative version.

> **Example:** Build as shared libraries instead of the static default:
> ```sh
> jst install -D'{"PROTOBUF_BUILD_SHARED": true}' -o .local
> ```

## Repository Remapping

This repository can be imported with its dependencies remapped:

- `toolchain`: The toolchain used to build protobuf. (default: [system
  toolchain](https://github.com/jst-build/toolchains-cc/blob/system/README.md))
- `zlib`: The zlib dependency.
- `absl`: The Abseil dependency.

> Note that if you remap the dependencies with libraries built from source, it
> is recommended to build them with the same toolchain (see [bundled.in.json](./bundled.in.json)).

**Example:** Import with a different toolchain and zlib dependency:

```jsonc
"imports": [
  { // ...
    "repos": [{
      "alias": "protobuf",
      "map": {
        "toolchain": "my-custom-toolchain",
        "zlib": "my-zlib-built-with-mycustom-toolchain"
      }
    }]
  }
  // ...
]
```