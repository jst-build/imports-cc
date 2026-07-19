# System library `protobuf` for the `jst` build system

Target definitions for building against `protobuf` from the local system.
These targets perform `pkg-config` lookups and do not compile anything.

> The `protoc` compiler binary is not provided here — `pkg-config` only
> resolves libraries, not executables. Ensure `protoc` is available on
> `PATH` if you need it.

## How to use this Repository

To import `protobuf` to your repository, add the following code to the
*imports section* of your `repos.in.json` and run `jst-lock` to generate the
final repository lock-file

```jsonc
"imports": [
  {
    "source": "git",
    "branch": "protobuf/system",
    "url": "https://github.com/jst-build/imports-cc",
    "repos": [{"alias": "protobuf"}]
  },
  // ...
],
```

## General Configuration

|Variable|Description|Default Value|
|-|-|-:|
| `ENV` | The environment for any generated action. May contain a colon-separated `PKG_CONFIG_PATH` for looking up `.pc` files; this variable, as well as `PATH`, is prefixed by the values provided in the toolchain's own defaults | *(unset)* |
| `LOCALBASE` | Use this localbase for building against system libs (e.g., `"/usr"`). | *(unset)* |
| `PKG_CONFIG_ARGS` | Additional `pkg-config` arguments (e.g. `"--define-prefix"` or `"--static"`). | `[]` |

> Note that you must set `ENV["PATH"]` if `pkg-config` is not available in the
> default system paths.