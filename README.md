# Fork of parquet-site

This is a fork of [apache/parquet-site](https://github.com/apache/parquet-site) — the source for the [Apache Parquet website](https://parquet.apache.org/) — used to preview in-progress documentation changes.

## Previewing changes

Preview site: **https://alamb.github.io/parquet-site/**

To preview a branch:

1. Push the branch to this fork.
2. Go to **Actions** → **Preview on GitHub Pages** → **Run workflow**, then pick/enter the branch to build in the `ref` box (defaults to `alamb/update_v2`).

The workflow runs from `main` (where the workflow file lives) but checks out and builds the branch you name, then publishes it to the URL above (~90s). Your feature/PR branch never needs the workflow file.
