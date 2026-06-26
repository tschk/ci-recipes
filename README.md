# ci-recipes

Reusable GitHub Actions workflows for projects using the Inauguration compiler.

## Generic Inauguration CI

A single workflow for ANY project that uses the `in` compiler.
Checks out the compiler, builds `in`, runs your `in build` command, and optionally runs tests.

```yaml
jobs:
  ci:
    uses: tschk/ci-recipes/.github/workflows/inauguration-ci.yml@master
    with:
      build-command: "in build --path src/main.in"
      test-command: "cargo test"
```

### Minimal — just build `in` and run a command

```yaml
jobs:
  ci:
    uses: tschk/ci-recipes/.github/workflows/inauguration-ci.yml@master
    with:
      build-command: "in build --path kernel/kernel-root.in --module-id MyProject"
```

### With QEMU/system tests

```yaml
jobs:
  ci:
    uses: tschk/ci-recipes/.github/workflows/inauguration-ci.yml@master
    with:
      build-command: "in build --path src/main.in"
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
      build-command: "in build --path src/main.in"
      run-clippy: true
```

### All inputs

| Input | Default | Description |
|-------|---------|-------------|
| `inauguration-repo` | `tschk/inauguration` | GitHub repo with the compiler |
| `inauguration-ref` | `master` | Git ref for the compiler |
| `inauguration-path` | `inauguration` | Checkout path for compiler source |
| `rust-version` | `stable` | Rust toolchain |
| `build-command` | `""` | `in build` command to run |
| `test-command` | `""` | Shell command to run after build |
| `install-deps` | `""` | APT packages (space-separated) |
| `run-clippy` | `false` | Run clippy/fmt on the compiler |
| `extra-build-features` | `extended` | Cargo features for compiler build |

### How it works

1. Checks out your project (the calling repo)
2. Checks out the `in` compiler from `inauguration-repo` at `inauguration-ref`
3. Builds the `in` binary with `cargo build --release`
4. Adds `in` to `$PATH`
5. Optionally runs `run-clippy` (clippy + fmt on compiler)
6. Installs APT deps if `install-deps` is set
7. Runs `build-command` (anything, typically `in build ...`)
8. Runs `test-command` (anything, typically test scripts)

## Rust CI

Generic Rust fmt → clippy → test for non-Inauguration Rust projects.

```yaml
jobs:
  ci:
    uses: tschk/ci-recipes/.github/workflows/rust-ci.yml@master
    with:
      workspace: "."
      features: ""
      rust-version: "stable"
```
