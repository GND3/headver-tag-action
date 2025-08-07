# HeadVer Tag Action

GitHub Action for generating semantic versions using HeadVer format and creating git tags automatically.

## Description

This action generates version numbers in the HeadVer format (`0.YYWW.BUILD`) where:
- `0`: Fixed HEAD version
- `YY`: Two-digit year (e.g., 25 for 2025)
- `WW`: Week number of the year (01-53)
- `BUILD`: Incremental build number for the week

## Usage

```yaml
- name: Generate HeadVer version and tag
  id: headver
  uses: GND3/headver-tag-action@v1
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

- name: Use generated version
  run: echo "Generated version: ${{ steps.headver.outputs.version }}"
```

## Outputs

- `version`: Generated version in HeadVer format (e.g., `0.2532.1`)

## Requirements

- `fetch-depth: 0` and `fetch-tags: true` in checkout action
- `GITHUB_TOKEN` environment variable for creating tags

## Example Workflow

```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v4
    with:
      fetch-depth: 0
      fetch-tags: true
      
  - name: Generate version
    id: headver
    uses: GND3/headver-tag-action@v1
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      
  - name: Build with version
    run: ./gradlew build -Pversion=${{ steps.headver.outputs.version }}
```