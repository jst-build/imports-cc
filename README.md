# Library `libgit2` for the `jst` build system

Target definitions for building `libgit2` from source. By default, this
library is built with the system toolchain and links against the system's
`zlib`/`ssl`. To use different ones see
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

## How to import this Repository

To import `libgit2` to your repository, add the following code to the
*imports section* of your `repos.in.json` and run `jst-lock` to generate the
final repository lock-file

```jsonc
"imports": [
  {
    "source": "git",
    "branch": "libgit2/v1.9.7",
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

- `ALL`/`INSTALL`: Installs the libgit2 library and its public headers into an installation-style tree, as well as the binaries:
    - `git2`, if `BUILD_CLI` is set (default)
    - `libgit2_tests`, if `BUILD_TESTS` is set (default)
- `git2`: The Git linkable library, for consumption as a build dependency.
- `git2_cli`: The `git2` command-line tool.
- `TESTS`: Runs the Clar test suite (3091 tests), split into one target per
  suite family (`merge`, `clone`, `checkout` etc.), so the test suites can run
  in parallel and become cacheable.

## General Configuration

|Variable|Description|
|-|-|
| `OS` | Operating system to build for. |
| `ARCH` | The underlying architecture. Taken as a default for `TARGET_ARCH`. |
| `HOST_ARCH` | The architecture on which the build actions are carried out. |
| `TARGET_ARCH` | The architecture for which to build the binary. |
| `TOOLCHAIN_CONFIG` | The toolchain configuration. Use field `FAMILY` to specify the compiler family. |
| `DEBUG` | Map enabling and configuring the debug version. Specify a map that is logically true (e.g., non-null, non-empty map) to enable debug mode. |
| `CC` | The C compiler to use. |
| `CFLAGS` | The C compiler flags to use. |
| `ADD_CFLAGS` | Additional C compiler flags. |
| `ADD_LDFLAGS` | Additional linker flags. |
| `BUILD_POSITION_INDEPENDENT` | Build as position independent code. |
| `AR` | The archiver to use. |
| `DWP` | The DWARF format packaging tool to use. Required by debug builds that enable debug fission. |
| `ENV` | Map from strings to strings. The build environment to be used for build actions. Typically used to include an unusual value of `PATH`. |
| `LOCALBASE` | Use this localbase for building against system libs (e.g., `"/usr"`). |
| `PKG_CONFIG_ARGS` | Additional `pkg-config` arguments (e.g. `"--define-prefix"` or `"--static"`) |
| `LIBGIT2_BUILD_SHARED` | Boolean. Default `false`. Build a shared (`*.so`) library instead of a static (`*.a`) one. |
| `BUILD_CLI` | Boolean. Default `true`. Build the `git2` command-line tool and add it to `INSTALL`. |
| `BUILD_TESTS` | Boolean. Default `true`. Build the `git2` test suite and add it to `INSTALL`. |
| `DEPRECATE_HARD` | Boolean. Default `true`. Exclude deprecated functions from the build entirely. |
| `ENABLE_WERROR` | Boolean. Default `false`. Treat compiler warnings as errors. |

> This list is generated — run `jst describe` for the always-current,
> authoritative version.

### Feature flags

libgit2 exposes a number of additional flags to select optional backends
and platform capabilities, mirroring upstream libgit2's own
`CMakeLists.txt` options. Most backends are compiled from a bundled copy
or link against the system; the rest need an *open binding*, and only
`zlib` and `ssl` are bound by default — for anything else, add the import
yourself and pass it as a remap (see
[Repository Remapping](#repository-remapping) below).

| Flag | Accepted values | Default | Required bindings |
|-|-|-|-|
| `EXPERIMENTAL_SHA256` | `true`, `false` | `false` | - |
| `USE_THREADS` | `true`, `false` | `true` | - |
| `USE_NSEC` | `true`, `false` | `false` | - |
| `USE_SSH` | `"libssh2"`, `"exec"`, `false` | `false` | `libssh2` (if `"libssh2"`) |
| `USE_HTTPS` | `true`, `"OpenSSL"`, `"mbedTLS"`, `"SecureTransport"`, `"WinHTTP"`, `false` | `true` (`"WinHTTP"` on Windows, else `"OpenSSL"`) | `ssl` (if `"OpenSSL"`), `mbedtls` (if `"mbedTLS"`), `secure_transport` (if `"SecureTransport"`) |
| `USE_SHA1` | `"CollisionDetection"`, `"HTTPS"`, or a `USE_HTTPS` provider | `"CollisionDetection"` | `ssl` (if `"OpenSSL"`), `mbedtls` (if `"mbedTLS"`) |
| `USE_SHA256` | `"Builtin"`, `"HTTPS"`, or a `USE_HTTPS` provider | `"HTTPS"`, or `"Builtin"` if `USE_HTTPS` is off | `ssl` (if `"OpenSSL"`), `mbedtls` (if `"mbedTLS"`) |
| `USE_GSSAPI` | `true`, `false` | `false` | `gssapi` (if `true`) |
| `USE_HTTP_PARSER` | `"http-parser"`, `"llhttp"`, `"builtin"` | `"builtin"` (bundled llhttp) | `http_parser` (if `"http-parser"`), `llhttp` (if `"llhttp"`) |
| `REGEX_BACKEND` | `"regcomp_l"`, `"regcomp"`, `"pcre"`, `"pcre2"`, `"builtin"` | `"builtin"` (bundled PCRE2) | `pcre` (if `"pcre"`), `pcre2` (if `"pcre2"`) |
| `USE_BUNDLED_ZLIB` | `true`\|`"Bundled"`, `false` | `false` | `zlib` (if `false`) |
| `USE_NTLMCLIENT` | `true`, `false` | `true` on non-Windows systems with `USE_HTTPS` enabled, else `false` | - |
| `USE_ICONV` | `true`, `false` | `false` | - |

> Run `jst describe` for the authoritative, always-current version of this
> list — including values that are recognized but unsupported.

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
