# ci-recipes

Reusable GitHub Actions workflows for projects using the Inauguration compiler.

## Generic Inauguration CI

A single workflow for ANY project that uses the `in` compiler.
Checks out the compiler, builds `in`, runs your build command (for example `command in build ...`), and optionally runs tests.

```yaml
jobs:
  ci:
    uses: tschk/ci-recipes/.github/workflows/inauguration-ci.yml@master
    with:
      build-command: "command in build --path src/main.in"
      test-command: "cargo test"
```

### Minimal — build project with `in`

> `in` is a bash keyword (`case...in...esac`). Use `command in` to bypass it.

```yaml
jobs:
  ci:
    uses: tschk/ci-recipes/.github/workflows/inauguration-ci.yml@master
    with:
      build-command: "command in build --path src/main.in"
```

### Multi-step build (nasm + in compile)

```yaml
jobs:
  ci:
    uses: tschk/ci-recipes/.github/workflows/inauguration-ci.yml@master
    with:
      install-deps: "nasm"
      build-command: |
        command nasm -f bin boot/multiboot.asm -o /tmp/trampoline.bin
        command in compile --path kernel/kernel-root.in --entry kernel_entry --emit boot \
          --trampoline /tmp/trampoline.bin \
          --target native --target-triple x86_64-unknown-none --linkage static-lib \
          --out /tmp/kernel.bin
```

### With QEMU/system tests

```yaml
jobs:
  ci:
    uses: tschk/ci-recipes/.github/workflows/inauguration-ci.yml@master
    with:
      build-command: |
        command in build --path src/main.in
      test-command: |
        bash scripts/check-boot.sh
        bash scripts/check-integration.sh
      install-deps: "qemu-system-x86-64 nasm"
```

### With compiler clippy/fmt checks

```yaml
jobs:
  ci:
    uses: tschk/ci-recipes/.github/workflows/inauguration-ci.yml@master
    with:
      build-command: "command in build --path src/main.in"
      run-clippy: true
```

### All inputs

| Input | Default | Description |
|-------|---------|-------------|
| `inauguration-repo` | `tschk/inauguration` | GitHub repo with the compiler |
| `inauguration-ref` | `main` | Git ref for the compiler (pin a tag or commit SHA for reproducible builds) |
| `inauguration-path` | `inauguration` | Checkout path for compiler source |
| `rust-version` | `stable` | Rust toolchain |
| `build-command` | `""` | Project build command, e.g. `command in build ...` (set this, `test-command`, or both) |
| `test-command` | `""` | Shell command to run after build |
| `install-deps` | `""` | APT packages (space-separated) |
| `run-clippy` | `false` | Run clippy/fmt on the compiler |
| `extra-build-features` | `extended` | Cargo features for compiler build |

### How it works

1. Fails if both `build-command` and `test-command` are empty, since the job would otherwise test nothing
2. Checks out your project (the calling repo)
3. Checks out the `in` compiler from `inauguration-repo` at `inauguration-ref`
4. Builds the `in` binary with `cargo build --release`
5. Adds `in` to `$PATH`
6. Optionally runs `run-clippy` (clippy + fmt on compiler)
7. Installs APT deps if `install-deps` is set
8. Runs `build-command` (anything, typically `command in build ...`)
9. Runs `test-command` (anything, typically test scripts)

## Rust CI

Generic Rust fmt → clippy → test for non-Inauguration Rust projects.

```yaml
jobs:
  ci:
    uses: tschk/ci-recipes/.github/workflows/rust-ci.yml@master
    with:
      workspace: "."
      features: ""        # optional; empty runs cargo test with the crate's default features
      rust-version: "stable"
```
