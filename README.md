# ci-recipes

Reusable GitHub Actions workflows for `tschk` projects.

## Inauguration CI

Canonical CI for the [inauguration](https://github.com/tschk/inauguration) compiler project.
Covers fmt, clippy, test, protocol model generation, JIT conformance,
polyglot samples, self-hosting, and hybrid library crates.

```yaml
jobs:
  ci:
    uses: tschk/ci-recipes/.github/workflows/inauguration-ci.yml@main
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

### Jobs

| Job | Runner | What |
|-----|--------|------|
| `in-cli` | ubuntu | fmt → clippy → test |
| `rust-driver` | ubuntu | fmt → clippy → test (hybrid-cli) |
| `hybrid-libs` | ubuntu | test hybrid-pipeline, hybrid-sil, hybrid-core, hybrid-scheduler |
| `protocol-models` | ubuntu | build protocol-gen, check generated models |
| `jit-conformance` | macos | build `in` binary, run conformance suite (54 tests) |
| `polyglot-samples` | macos | build `in` binary, test .in/.icore/Swift samples |
| `self-host` | macos | JIT-compile compiler source, check diff |

## Rust CI

Generic Rust project CI with fmt, clippy, and test.

```yaml
jobs:
  ci:
    uses: tschk/ci-recipes/.github/workflows/rust-ci.yml@main
    with:
      workspace: "."
      features: ""
      rust-version: "stable"
```
