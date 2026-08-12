# Library `grpc` for the `jst` build system

Target definitions for building `grpc` from source. By default, this
library is built with the system toolchain and system dependencies
`ssl`, `re2`, `absl`, `zlib`, and `cares`. To use different ones
see [Repository Remapping](#repository-remapping) below.

> [!NOTE]
> gRPC always bundles Protobuf as it needs to link some of its internals
> (`libprotoc`) for plugin support.

To obtain a local installation, run:
```sh
jst install -o .local                   # only grpc
jst --main protobuf install -o .local   # also protoc
```

For a local installation with all dependencies bundled, run:
```sh
jst -C bundled.json install -o .local                   # only grpc
jst -C bundled.json --main protobuf install -o .local   # also protoc
```

## How to import this Repository

To import `grpc` to your repository, add the following code to the
*imports section* of your `repos.in.json` and run `jst-lock` to generate the
final repository lock-file

```jsonc
"imports": [
  {
    "source": "git",
    "branch": "grpc/v1.83.0",
    "url": "https://github.com/jst-build/imports-cc",
    "repos": [{"alias": "grpc"}]
  },
  // ...
],
```

## Provided Targets

The provided targets are either producing an installation directory
(`{bin,lib,include}`) or a build dependency target, to be consumed via the
`deps` field of `//CC:binary` and `//CC:library` rules.

Available targets are:

- `ALL`/`INSTALL`: Installs gRPC and its protoc plugin into an
  installation-style tree.
- `grpc`: The gRPC core library (secure build, C API).
- `gpr`: gRPC's portability layer (platform/OS abstraction utilities).
- `grpc++`: The gRPC C++ API, built on top of the gRPC core library.
- One target per language-specific `protoc` plugin (C++, C#, Node.js, Obj-C,
  PHP, Ruby) for generating gRPC service stubs from `.proto` files.
  (e.g. `grpc_cpp_plugin`, `grpc_csharp_plugin`, `grpc_node_plugin`,
  `grpc_objective_c_plugin`, `grpc_php_plugin`, `grpc_ruby_plugin`)

## General Configuration

|Variable|Description|
|-|-|
| `OS` | Operating system to build for. |
| `ARCH` | The underlying architecture. Used as a default for `TARGET_ARCH` if that is not set. |
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
| `BUILD_POSITION_INDEPENDENT` | Build as position independent code. |
| `GRPC_BUILD_SHARED` | Boolean. Build shared (`*.so`) grpc libraries instead of static (`*.a`) ones. Default `false`. |
| `PROTOBUF_BUILD_SHARED` | Boolean. Build shared (`*.so`) protobuf libraries instead of static (`*.a`) ones. Default `false`. |
| `AR` | The archiver to use. |
| `ENV` | Map from strings to strings. The build environment to be used for build actions. Typically used to include an unusual value of `PATH`. |
| `LOCALBASE` | Use this localbase for building against system libs (e.g., `"/usr"`). |
| `PKG_CONFIG_ARGS` | Additional `pkg-config` arguments (e.g. `"--define-prefix"` or `"--static"`) |

> This list is generated — run `jst describe` for the always-current,
> authoritative version.

## Repository Remapping

This repository can be imported with its dependencies remapped:

- `toolchain`: The base toolchain used to build gRPC and Protobuf. (default: [system
  toolchain](https://github.com/jst-build/toolchains-cc/blob/system/README.md))
- `protobuf`: The bundled Protobuf dependency, providing `protoc`,
  `libprotobuf`, and `libprotoc`.
- `ssl`: The SSL dependency.
- `absl`: The Abseil dependency.
- `zlib`: The zlib dependency.
- `re2`: The RE2 dependency.
- `cares`: The c-ares dependency.

> [!IMPORTANT]
> Note that if you remap the dependencies with libraries built from source, it
> is recommended to build them with the same toolchain (see [bundled.in.json](./bundled.in.json)).

**Example:** Import with a different toolchain and absl dependency:

```jsonc
"imports": [
  { // ...
    "repos": [{
      "alias": "grpc",
      "map": {
        "toolchain": "my-custom-toolchain",
        "absl": "my-absl-built-with-mycustom-toolchain"
      }
    }]
  }
  // ...
]
```
