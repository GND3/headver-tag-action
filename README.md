# HeadVer Tag Action

GitHub Action for generating semantic versions using HeadVer format and creating git tags automatically.

## Description

This action generates version numbers in the HeadVer format (`0.YYWW.BUILD`) where:
- `0`: Fixed HEAD version
- `YY`: Two-digit year (e.g., 25 for 2025)
- `WW`: Week number of the year (01-53)
- `BUILD`: Incremental build number for the week

## Inputs

- `identifier` (optional): Custom identifier prefix for tag separation. When provided, tags will be created as `{identifier}-{version}` instead of `v{version}`.
- `dry-run` (optional, default: `false`): If `true`, calculates version and tag without actually creating/pushing the tag. Useful for testing or previewing what version would be generated.

## Release Notes

This action automatically generates release notes by collecting commit messages between the previous tag and the current tag. The release notes include:

- Commit messages from the previous tag to the current HEAD
- Merged commits are excluded from the release notes
- If no previous tag exists, all commit messages are included
- Release notes are formatted in Markdown with bullet points

## Outputs

- `version`: Generated version in HeadVer format (e.g., `0.2532.1`)
- `tag`: Generated full tag name (e.g., `v0.2532.1` or `olym-api-0.2532.1`)
- `release_notes`: Release notes generated from commit messages between previous and current tag

## Usage

### Basic Usage (Single Project)
```yaml
- name: Generate HeadVer version and tag
  id: headver
  uses: GND3/headver-tag-action@v3
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

- name: Use generated version
  run: echo "Generated version: ${{ steps.headver.outputs.version }}"

- name: Use release notes
  run: echo "Release notes: ${{ steps.headver.outputs.release_notes }}"
```

### Multi-Project Usage (with custom identifier)
```yaml
- name: Generate HeadVer version and tag for olym-api
  id: headver
  uses: GND3/headver-tag-action@v3
  with:
    identifier: 'olym-api'
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

- name: Use generated version
  run: echo "Generated version: ${{ steps.headver.outputs.version }}"

- name: Use release notes
  run: echo "Release notes: ${{ steps.headver.outputs.release_notes }}"
```

## Tag Examples

- **Without identifier**: `v0.2532.1`, `v0.2532.2`, etc.
- **With identifier "olym-api"**: `olym-api-0.2532.1`, `olym-api-0.2532.2`, etc.
- **With identifier "olym-admin-api"**: `olym-admin-api-0.2532.1`, etc.

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
    uses: GND3/headver-tag-action@v3
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
      
  - name: Generate version for project
    id: headver
    uses: GND3/headver-tag-action@v3
    with:
      identifier: ${{ env.CUSTOM_IDENTIFIER }}  # e.g., "olym-api" or "olym-admin-api"
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

  - name: Build with version
    run: ./gradlew build -Pversion=${{ steps.headver.outputs.version }}
```

### Dry Run (Preview Version Without Creating Tag)
```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v4
    with:
      fetch-depth: 0
      fetch-tags: true

  - name: Preview version (dry run)
    id: headver
    uses: GND3/headver-tag-action@v3
    with:
      dry-run: 'true'

  - name: Show what would be created
    run: |
      echo "Would create version: ${{ steps.headver.outputs.version }}"
      echo "Would create tag: ${{ steps.headver.outputs.tag }}"
```