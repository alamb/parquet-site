# Fork of parquet-site

This is a fork of [apache/parquet-site](https://github.com/apache/parquet-site) — the source for the [Apache Parquet website](https://parquet.apache.org/) — used to preview in-progress documentation changes.

## Previewing changes

Index of all previews: **https://alamb.github.io/parquet-site/**

Each branch is published into its own directory, so several branches can be previewed at the same time:

| branch | preview URL |
| --- | --- |
| `alp-blog` | https://alamb.github.io/parquet-site/alp-blog/ |
| `alamb/foo` | https://alamb.github.io/parquet-site/alamb/foo/ |

The `/parquet-site/` segment is fixed by GitHub Pages — a project site is always served from `https://<user>.github.io/<repo>/` — so the branch name goes after it, not before.

To preview a branch:

1. Push the branch to this fork.
2. Go to **Actions** → **Preview on GitHub Pages** → **Run workflow**, then pick/enter the branch to build in the `ref` box.

The workflow runs from `main` (where the workflow file lives) but checks out and builds the branch you name, then publishes it to `/<branch>/` (~90s). Your feature/PR branch never needs the workflow file.

Previews accumulate on the `gh-pages` branch and are never deleted automatically. To drop one, delete its directory and its line in `previews.txt` on `gh-pages`.

### One-time setup in a fork

1. **Actions** tab → enable workflows (forks disable them by default).
2. **Settings** → **Pages** → **Build and deployment** → Source: **Deploy from a branch**, branch `gh-pages`, folder `/ (root)`.
