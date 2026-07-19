# Library `gtest` for the `jst` build system

Target definitions for building `gtest` (GoogleTest, including
GoogleMock) from source. By default, this library is built with the
system toolchain. To use a different toolchain see [Repository
Remapping](#repository-remapping) below.

To obtain a local installation, run:
```
jst install -o .local
```

## How to import this Repository

To import `gtest` to your repository, add the following code to the
*imports section* of your `repos.in.json` and run `jst-lock` to generate the
final repository lock-file

```jsonc
"imports": [
  {
    "source": "git",
    "branch": "gtest/v1.17.0",
    "url": "https://github.com/jst-build/imports-cc",
    "repos": [{"alias": "gtest"}]
  },
  // ...
],
```

## Provided Targets

The provided targets are either producing an installation directory
(`{bin,lib,include}`) or a build dependency target, to be consumed via the
`deps` field of `//CC:binary` and `//CC:library` rules.

Available targets are:

- `ALL`/`INSTALL`: Installs the GoogleTest libraries into an
  installation-style tree.
- `gtest`: The gtest C++ test framework, for consumption as a build
  dependency.
- `gtest_main`: gtest's default `main()` entry point. Link this in
  addition to `gtest` for test binaries that don't define their own
  `main()`.
- `gmock`: The gmock C++ mocking framework, built on top of gtest, for
  consumption as a build dependency.
- `gmock_main`: gmock's default `main()` entry point, which also
  initializes gtest. Link this in addition to `gmock` for test binaries
  that don't define their own `main()`.

## General Configuration

|Variable|Description|
|-|-|
| `OS` | Operating system to build for. |
| `ARCH` | The underlying architecture. Used as a default for `TARGET_ARCH` if that is not set. |
| `TARGET_ARCH` | The architecture for which to build the binary. |
| `DEBUG` | Map enabling and configuring the debug version. Specify a map that is logically true (e.g., non-null, non-empty map) to enable debug mode. |
| `TOOLCHAIN_CONFIG` | The toolchain configuration. Use field `FAMILY` to specify the compiler family. |
| `CXX` | The C++ compiler to use. |
| `CXXFLAGS` | The C++ compiler flags to use. |
| `ADD_CXXFLAGS` | Additional C++ compiler flags. |
| `AR` | The archiver to use. |
| `ENV` | Map from strings to strings. The build environment to be used for build actions. Typically used to include an unusual value of `PATH`. |
| `LOCALBASE` | Use this localbase for building against system libs (e.g., `"/usr"`). |
| `PKG_CONFIG_ARGS` | Additional `pkg-config` arguments (e.g. `"--define-prefix"` or `"--static"`) |
| `GTEST_BUILD_SHARED` | Boolean. Build shared (`*.so`) libraries instead of static (`*.a`) ones. Default `false`; mirrors upstream's own `BUILD_SHARED_LIBS` CMake option. |

> This list is generated — run `jst describe` for the always-current,
> authoritative version.

> **Example:** Build as shared libraries instead of the static default:
> ```sh
> jst install -D'{"GTEST_BUILD_SHARED": true}' -o .local
> ```

## Repository Remapping

The toolchain for building this library can be changed by remapping the
`toolchain` repository during the import.

**Example:** Import with different toolchain:

```jsonc
"imports": [
  { // ...
    "repos": [{
      "alias": "gtest",
      "map": {"toolchain": "my-custom-toolchain"}
    }]
  }
  // ...
]
```
