## Build Command

```nu
cargo build --profile slim --no-default-features
```

### Output command

Note: Replace the `1` below with the nth profile

```nu
tar caf slim-1.tar.xz ./target/slim/nu
ls -la ./target/slim/nu slim-1.tar.xz | select size name | to md --pretty
```

## Profile 1

```toml
[profile.slim]
inherits = "release"
opt-level = "z" # Optimize for size, but also turn off loop vectorization
strip = true
lto = true
panic = "abort"
```


| size    | name           |
| ------- | -------------- |
| 16.3 MB | target/slim/nu |
| 5.5 MB  | slim-1.tar.xz  |

## Profile 2

```toml
[profile.slim]
inherits = "release"
opt-level = "z" # Optimize for size, but also turn off loop vectorization
strip = true
lto = true
panic = "abort"
codegen-units = 1
```

| size    | name           |
| ------- | -------------- |
| 15.5 MB | target/slim/nu |
| 5.3 MB  | slim-2.tar.xz  |

## Profile 3

```nu
(
  RUSTFLAGS="-Zlocation-detail=none" cargo +nightly build
    -Z build-std=std,panic_abort
    -Z build-std-features="optimize_for_size"
    --profile dist
    --features (["network", "plugin", "rustls-tls", "sqlite"] | str join ',')
    --no-default-features
  ls target/dist
)
```

| size    | name           |
| ------- | -------------- |
| 14.3 MB | target/slim/nu |
| 4.9 MB  | slim-3.tar.xz  |

## Profile 4

```toml
[profile.slim]
inherits = "release"
opt-level = "z" # Optimize for size, but also turn off loop vectorization
strip = true
lto = true
# No abort on panic
# panic = "abort"
codegen-units = 1
```

| size    | name           |
| ------- | -------------- |
| 18.7 MB | target/slim/nu |
| 6.3 MB  | slim-4.tar.xz  |


## Notes:

[`upx` compression](https://github.com/johnthagen/min-sized-rust#compress-the-binary):

```nu
upx --best --lzma nu-nightly --force-macos
tar caf slim-4.tar.xz ./nu-nightly
ls slim-4.tar.xz nu-nightly | select name size | to md -p
```

Size seems to have been unchanged:

| name          | size   |
| ------------- | ------ |
| slim-4.tar.xz | 4.3 MB |
| nu-nightly    | 4.3 MB |

---

This document is a copy of https://github.com/nushell/nushell/pull/18949
