# nushell-ci

Semi-automated repo providing nushell binaries optimized for CI using [`dist`](https://axodotdev.github.io/cargo-dist/)


## Details

The nushell artifacts in this repo try to limit the download artifact size as much as possible while making reasonable compromises:

* omit default feature flags such as `lsp`, `mcp`, and `trash-support` that are unlikely to be used in CI:
  https://github.com/mkatychev/nushell-ci/blob/a77aa872ed8528450b102d3aa481074f84bb129b/dist-workspace.toml#L20-L23
* limit release artifact to just the `nu` binary, avoiding bundling of large plugins (such as `nu_plugin_polars`) in the same tarball/zipfile as `nu`:
  ```sh-session
  $ http get https://github.com/nushell/nushell/releases/download/0.115.1/nu-0.115.1-aarch64-unknown-linux-musl.tar.gz
    | save nu-0.115.1-aarch64-unknown-linux-musl.tar.gz
  $ tar xf ./nu-0.115.1-aarch64-unknown-linux-musl.tar.gz
  $ ls ./nu-0.115.1-aarch64-unknown-linux-musl/ | reject modified | to md --pretty
  | name                                                             | type | size     |
  | ---------------------------------------------------------------- | ---- | -------- |
  | nu-0.115.1-aarch64-unknown-linux-musl/LICENSE                    | file | 1.0 kB   |
  | nu-0.115.1-aarch64-unknown-linux-musl/README.txt                 | file | 331 B    |
  | nu-0.115.1-aarch64-unknown-linux-musl/nu                         | file | 66.3 MB  |
  | nu-0.115.1-aarch64-unknown-linux-musl/nu_plugin_custom_values    | file | 12.7 MB  |
  | nu-0.115.1-aarch64-unknown-linux-musl/nu_plugin_example          | file | 13.2 MB  |
  | nu-0.115.1-aarch64-unknown-linux-musl/nu_plugin_formats          | file | 13.1 MB  |
  | nu-0.115.1-aarch64-unknown-linux-musl/nu_plugin_gstat            | file | 13.4 MB  |
  | nu-0.115.1-aarch64-unknown-linux-musl/nu_plugin_inc              | file | 8.0 MB   |
  | nu-0.115.1-aarch64-unknown-linux-musl/nu_plugin_polars           | file | 114.7 MB |
  | nu-0.115.1-aarch64-unknown-linux-musl/nu_plugin_query            | file | 18.8 MB  |
  | nu-0.115.1-aarch64-unknown-linux-musl/nu_plugin_stress_internals | file | 731.0 kB |
  ```
* use a build profile that is optimized for size:
  https://github.com/mkatychev/nushell-ci/blob/a77aa872ed8528450b102d3aa481074f84bb129b/.cargo/config.toml

