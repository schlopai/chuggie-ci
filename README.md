# chuggie-ci

Publishes `ghcr.io/schlopai/chuggie-toolchain` — the package name differs from the repo name on
purpose; see the note in `.github/workflows/publish.yml`.

The toolchain image [schlopai/chuggie](https://github.com/schlopai/chuggie)'s CI runs on.

```
ghcr.io/schlopai/chuggie-toolchain
```

Contains libmgba 0.10.5 (built headless), the pinned Rust nightly with `rust-src` and
`thumbv4t-none-eabi`, stable with rustfmt and clippy, `agb-gbafix`, Node, and the apt packages the
verifier scripts use.

It exists because chuggie's CI matrix is ~90 jobs, and each one used to spend minutes on apt, a
libmgba source build, a rustup toolchain and `cargo install agb-gbafix` before it ran anything —
none of which changes between runs.

## Using it

Pin the content-hash tag, never `latest`:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    container: ghcr.io/schlopai/chuggie-toolchain:<hash>
```

The tag is the first 12 characters of `sha256sum Dockerfile`, so the image a tag names can never
change. The publish workflow prints the tag to pin as a job notice.

## Changing it

Edit the `Dockerfile` and open a PR. The PR builds the image without publishing, so a broken
Dockerfile fails here. On merge to main it publishes under the new hash, and consumers move to it
deliberately by bumping their pin.

## Versions are part of a contract

chuggie's verifiers assert on exact frames and exact audio, so the emulator and compiler versions
here are not interchangeable — a distro libmgba or a floating nightly changes ROM codegen and shifts
frame-exact timings. Every version in the `Dockerfile` is pinned on purpose.

## License

MIT
