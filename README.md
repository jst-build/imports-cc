# Library `libssh2` for the `jst` build system

Target definitions for building libssh2 from source. By default, this
library is built with the system toolchain and links against the system's
`ssl`/`zlib`. To use different ones see
[Repository Remapping](#repository-remapping) below.

To obtain a local installation, run:
```
jst install -o .local
```

For a local installation with bundled dependencies (source-built
BoringSSL/zlib, no system TLS/compression libraries needed), run:
```
jst -C bundled.json install -o .local
```

> [!NOTE]
> Building against BoringSSL (system or bundled) automatically disables the
> Blowfish and CAST5 ciphers, since BoringSSL doesn't implement them - use
> OpenSSL if you need them.

## How to import this Repository

To import `libssh2` to your repository, add the following code to the
*imports section* of your `repos.in.json` and run `jst-lock` to generate
the final repository lock-file

```jsonc
"imports": [
  {
    "source": "git",
    "branch": "libssh2/v1.11.1",
    "url": "https://github.com/jst-build/imports-cc",
    "repos": [{"alias": "libssh2"}]
  },
  // ...
],
```

## Provided Targets

The provided targets are either producing an installation directory
(`{bin,lib,include}`) or a build dependency target, to be consumed via the
`deps` field of `//CC:binary` and `//CC:library` rules.

Available targets are:

- `ALL`/`INSTALL`: Installs the libssh2 library and its public headers
  into an installation-style tree.
- `libssh2`: Target for consuming the libssh2 linkable library as a
  build dependency.
- `TESTS`: Runs the test suite - local-only (no network I/O): a real SSH2
  handshake against a live server isn't reproducible inside a hermetic
  build sandbox.

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
| `AR` | The archiver to use. |
| `ENV` | Map from strings to strings. The build environment to be used for build actions. Typically used to include an unusual value of `PATH`. |
| `LOCALBASE` | Use this localbase for building against system libs (e.g., `"/usr"`). |
| `PKG_CONFIG_ARGS` | Additional `pkg-config` arguments (e.g. `"--define-prefix"` or `"--static"`) |
| `LIBSSH2_BUILD_SHARED` | Boolean. Default `false`. Build a shared (`*.so`) library instead of a static (`*.a`) one. |
| `LIBSSH2_ZLIB_COMPRESSION` | Boolean. Default `true`. Compile in `zlib` SSH compression support. Diverges from upstream's own default (OFF). |
| `LIBSSH2_DSA_ENABLE` | Boolean. Default `false`. Enable the deprecated `ssh-dss` (DSA) host/user key type. |
| `LIBSSH2_NO_DEPRECATED` | Boolean. Default `false`. Build without deprecated APIs. |
| `LIBSSH2_CLEAR_MEMORY` | Boolean. Default `true`. Clear sensitive memory (private keys, session secrets) before it is freed. |
| `LIBSSH2_DEBUG_LOGGING` | Boolean. Default `false`. Log execution with debug trace. |

> This list is generated — run `jst describe` for the always-current,
> authoritative version.

## Repository Remapping

This repository can be imported with its dependencies remapped:

- `toolchain`: The toolchain used to build libssh2. (default: [system
  toolchain](https://github.com/jst-build/toolchains-cc/blob/system/README.md))
- `zlib`: The zlib dependency.
- `ssl`: The SSL dependency (must expose an `ssl` and `crypto` target).

> Note that if you remap the dependencies with libraries built from source,
> it is recommended to build them with the same toolchain (see
> [bundled.in.json](./bundled.in.json)).

**Example:** Import with a different toolchain and zlib dependency:

```jsonc
"imports": [
  { // ...
    "repos": [{
      "alias": "libssh2",
      "map": {
        "toolchain": "my-custom-toolchain",
        "zlib": "my-zlib-built-with-mycustom-toolchain"
      }
    }]
  }
  // ...
]
```
