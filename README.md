# ci-recipes

Reusable GitHub Actions workflows for Rust projects under `tschk`.

## Usage

Call from your repo's `.github/workflows/ci.yml`:

```yaml
jobs:
  ci:
    uses: tschk/ci-recipes/.github/workflows/rust-ci.yml@main
    with:
      workspace: "."        # path to Cargo.toml dir
      features: "extended"  # optional: cargo features for tests
      rust-version: "stable"
```

## Workflows

| Workflow | What it does |
|----------|-------------|
| `rust-ci.yml` | fmt → clippy → test (with Rust cache) |
