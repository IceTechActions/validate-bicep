# validate-bicep

Validates Bicep templates by running `az bicep build` (which catches syntax and type errors) on `main.bicep` and all files in `modules/`. Does not deploy anything.

By default it validates files in the calling workflow's workspace. Use the `bicep_root` input to override (e.g. when running inside an action repo's own CI after checkout).

## Prerequisites

- Azure CLI with Bicep extension available on the runner (`az bicep build`)
- No Azure login required — validation is purely a local compile step

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `bicep_root` | No | `${{ github.workspace }}` | Directory containing `main.bicep` and an optional `modules/` subdirectory |

## Usage

### Validate a calling repo's Bicep templates

```yaml
- uses: IceTechActions/validate-bicep@v1
  # bicep_root defaults to the workspace, finds main.bicep at the repo root
```

### Validate files in an action repo's own CI (after checkout)

```yaml
- uses: actions/checkout@v4
- uses: IceTechActions/validate-bicep@v1
  with:
    bicep_root: ${{ github.workspace }}
```

## Behaviour

1. Builds `$bicep_root/main.bicep` — fails on any error or warning
2. Builds each `$bicep_root/modules/*.bicep` file — reports each result and fails if any fail
3. If no `modules/` directory exists, step 2 exits cleanly with a skip message
