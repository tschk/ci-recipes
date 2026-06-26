# ci-recipes

Reusable GitHub Actions workflows for `tschk` projects.

## Workflows

| Workflow | What it does | Who uses it |
|----------|-------------|-------------|
| `inauguration-ci.yml` | Build + test + JIT conformance + self-host for the compiler | [`inauguration`](https://github.com/tschk/inauguration) |
| `space-ci.yml` | Compile kernel with `in`, QEMU boot check, SCI validation | [`space`](https://github.com/tschk/space) |
| `rust-ci.yml` | Generic fmt → clippy → test | Any Rust project |

---

## Inauguration CI

Canonical CI for the [inauguration](https://github.com/tschk/inauguration) compiler.
Covers fmt, clippy, test, protocol model generation, JIT conformance,
polyglot samples, self-hosting, and hybrid library crates.

```yaml
jobs:
  ci:
    uses: tschk/ci-recipes/.github/workflows/inauguration-ci.yml@master
    with:
      rust-version: "stable"
      in-cli-features: "extended"
      run-jit-conformance: true
      run-polyglot-samples: true
      run-self-host: true
      run-protocol-models: true
```

### Inputs

| Input | Default | Description |
|-------|---------|-------------|
| `rust-version` | `stable` | Rust toolchain |
| `in-cli-features` | `extended` | Cargo features for in-cli |
| `run-jit-conformance` | `true` | JIT conformance (macOS, aarch64) |
| `run-polyglot-samples` | `true` | Polyglot language samples |
| `run-self-host` | `true` | Self-host compiler compilation |
| `run-protocol-models` | `true` | Protocol model generation check |

---

## Space CI

CI for projects that use the `in` compiler to build `.in` source code.
Checks out [inauguration](https://github.com/tschk/inauguration), builds the
`in` binary, compiles the project's `.in` kernel, and runs QEMU boot tests.

```yaml
jobs:
  ci:
    uses: tschk/ci-recipes/.github/workflows/space-ci.yml@master
    with:
      inauguration-ref: master
      qemu-test: true
      sci-check: true
```

### Inputs

| Input | Default | Description |
|-------|---------|-------------|
| `inauguration-ref` | `master` | Git ref for inauguration checkout |
| `inauguration-path` | `inauguration` | Relative checkout path |
| `rust-version` | `stable` | Rust toolchain |
| `qemu-test` | `true` | QEMU boot verification |
| `sci-check` | `true` | SCI metadata validation |
| `run-network-test` | `false` | Network driver test |

---

## Rust CI

Generic Rust project CI.

```yaml
jobs:
  ci:
    uses: tschk/ci-recipes/.github/workflows/rust-ci.yml@master
    with:
      workspace: "."
      features: ""
      rust-version: "stable"
```
