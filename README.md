# HeadVer Tag Action

GitHub Action for generating semantic versions using HeadVer format and creating git tags automatically.

## Description

This action generates version numbers in the HeadVer format (`0.YYWW.BUILD`) where:
- `0`: Fixed HEAD version
- `YY`: Two-digit year (e.g., 25 for 2025)
- `WW`: Week number of the year (01-53)
- `BUILD`: Incremental build number for the week

## Inputs

- `api-name` (optional): API name prefix for tag separation. When provided, tags will be created as `{api-name}-v{version}` instead of `v{version}`.

## Outputs

- `version`: Generated version in HeadVer format (e.g., `0.2532.1`)

## Usage

### Basic Usage (Single Project)
```yaml
- name: Generate HeadVer version and tag
  id: headver
  uses: GND3/headver-tag-action@v2
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

- name: Use generated version
  run: echo "Generated version: ${{ steps.headver.outputs.version }}"
```

### Multi-Project Usage (with API name)
```yaml
- name: Generate HeadVer version and tag for olym-api
  id: headver
  uses: GND3/headver-tag-action@v2
  with:
    api-name: 'olym-api'
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

- name: Use generated version
  run: echo "Generated version: ${{ steps.headver.outputs.version }}"
```

## Tag Examples

- **Without api-name**: `v0.2532.1`, `v0.2532.2`, etc.
- **With api-name "olym-api"**: `olym-api-v0.2532.1`, `olym-api-v0.2532.2`, etc.
- **With api-name "olym-admin-api"**: `olym-admin-api-v0.2532.1`, etc.

## Requirements

- `fetch-depth: 0` and `fetch-tags: true` in checkout action
- `GITHUB_TOKEN` environment variable for creating tags

## Example Workflows

### Single Project
```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v4
    with:
      fetch-depth: 0
      fetch-tags: true
      
  - name: Generate version
    id: headver
    uses: GND3/headver-tag-action@v2
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      
  - name: Build with version
    run: ./gradlew build -Pversion=${{ steps.headver.outputs.version }}
```

### Multi-Project Repository
```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v4
    with:
      fetch-depth: 0
      fetch-tags: true
      
  - name: Generate version for API project
    id: headver
    uses: GND3/headver-tag-action@v2
    with:
      api-name: ${{ env.API_NAME }}  # e.g., "olym-api" or "olym-admin-api"
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      
  - name: Build with version
    run: ./gradlew build -Pversion=${{ steps.headver.outputs.version }}
```