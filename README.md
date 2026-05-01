# github-workflows
My custom reusable GitHub Actions workflows.

## Workflows

### `update-tool-versions.yml`

Fetches the latest stable Ruby and Node.js LTS versions from
[endoflife.date](https://endoflife.date) and updates all version files it finds,
then opens a pull request with the changes.

Supported files:
- **`.tool-versions`** (asdf/mise) — `ruby x.y.z` / `nodejs x.y.z` / `node x.y.z`
- **`mise.toml`** — `ruby = "x.y.z"` / `node = "x.y.z"` under `[tools]`
- **`Dockerfile*`** (all, recursive) — `FROM ruby:x.y.z` / `FROM node:x.y.z`; variant suffixes like `-alpine` are preserved

**Usage** — add this to `.github/workflows/update-tool-versions.yml` in each consuming repo:

```yaml
name: Update tool versions

on:
  schedule:
    - cron: "0 6 * * 1" # Every Monday at 06:00 UTC
  workflow_dispatch:

jobs:
  update:
    uses: davidwessman/github-workflows/.github/workflows/update-tool-versions.yml@main
    permissions:
      contents: write
      pull-requests: write
```

**Inputs** (all optional):

| Input        | Type    | Default | Description                          |
| ------------ | ------- | ------- | ------------------------------------ |
| `ruby`       | boolean | `true`  | Update Ruby to latest stable         |
| `nodejs`     | boolean | `true`  | Update Node.js to latest LTS         |
| `dockerfile` | boolean | `true`  | Update `FROM ruby:x.y.z` / `FROM node:x.y.z` in all `Dockerfile*` files |

The workflow supports both `nodejs` and `node` as tool names in `.tool-versions`.
Dockerfile image tags with variant suffixes (e.g. `-alpine`, `-slim`) are preserved;
only full `x.y.z` tags are updated.
