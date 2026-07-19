# System library `lzma` for the `jst` build system

Target definitions for building against `lzma` (package name `liblzma`)
from the local system. This target performs a `pkg-config` lookup and does
not compile anything.

## How to use this Repository

To import `lzma` to your repository, add the following code to the *imports
section* of your `repos.in.json` and run `jst-lock` to generate the final
repository lock-file

```jsonc
"imports": [
  {
    "source": "git",
    "branch": "lzma/system",
    "url": "https://github.com/jst-build/imports-cc",
    "repos": [{"alias": "lzma"}]
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