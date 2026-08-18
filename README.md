# Library `boringssl` for the `jst` build system

Target definitions for building `boringssl` from source. By default, this
library is built with the system toolchain. To use a different toolchain
see [Repository Remapping](#repository-remapping) below.

To obtain a local installation, run:
```
jst install -o .local
```

## How to import this Repository

To import `boringssl` to your repository, add the following code to the
*imports section* of your `repos.in.json` and run `jst-lock` to generate the
final repository lock-file

```jsonc
"imports": [
  {
    "source": "git",
    "branch": "boringssl/v0.20260803.0",
    "url": "https://github.com/jst-build/imports-cc",
    "repos": [{"alias": "boringssl"}]
  },
  // ...
],
```

## Provided Targets

The provided targets are either producing an installation directory
(`{bin,lib,include}`) or a build dependency target, to be consumed via the
`deps` field of `//CC:binary` and `//CC:library` rules.

Available targets are:

- `ALL`/`INSTALL`: Installs the BoringSSL libraries into an
  installation-style tree.
- `crypto`: BoringSSL's `libcrypto` — cryptographic primitives (hashing,
  ciphers, ASN.1, X.509, ...) underlying `libssl`, for consumption as a
  build dependency.
- `ssl`: BoringSSL's `libssl` — the TLS/SSL protocol implementation,
  built on top of `crypto`, for consumption as a build dependency.

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
| `BORINGSSL_BUILD_SHARED` | Boolean. Build shared (`*.so`) libraries instead of static (`*.a`) ones. Default `false`. |

> This list is generated — run `jst describe` for the always-current,
> authoritative version.

> **Example:** Build as shared libraries instead of the static default:
> ```sh
> jst install -D'{"BORINGSSL_BUILD_SHARED": true}' -o .local
> ```

## Repository Remapping

The toolchain for building this library can be changed by remapping the
`toolchain` repository during the import.

**Example:** Import with different toolchain:

```jsonc
"imports": [
  { // ...
    "repos": [{
      "alias": "boringssl",
      "map": {"toolchain": "my-custom-toolchain"}
    }]
  }
  // ...
]
```