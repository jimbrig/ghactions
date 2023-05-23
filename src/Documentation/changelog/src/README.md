# Update Changelog GitHub Action - `changelog.yml`

[![](https://img.shields.io/badge/Action-changelog-blue?style=social&logo=github)](https://img.shields.io/badge/Action-changelog-blue)

> **Note**
> This action automatically updates the repository's `CHANGELOG.md` using [`git-cliff`](https://github.com/orhun/git-cliff) and a
> [`cliff.toml`](cliff.toml) configuration file.


## Inputs Variables

- `config`: Path to the configuration file (default: `"cliff.toml"`)
- `args`: Additional [arguments]() passed to `git-cliff` (default: `"-v"`)

## Output Variables

- `changelog`: Output file that contains the generated changelog.
- `content`: Content of the changelog.

## Environment variables

- `OUTPUT`: Output file. (Default: `"git-cliff/CHANGELOG.md"`)

## Examples

### Simple

The following example fetches the whole Git history (`fetch-depth: 0`), generates a changelog in `./CHANGELOG.md`, and prints it out.

```yml
jobs:
  changelog:
    name: Generate changelog
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - name: Generate a changelog
        uses: orhun/git-cliff-action@v2
        id: git-cliff
        with:
          config: cliff.toml
          args: --verbose
        env:
          OUTPUT: CHANGELOG.md

      - name: Print the changelog
        run: cat "${{ steps.git-cliff.outputs.changelog }}"
```

### Advanced

The following example generates a changelog for the latest pushed tag and sets it as the body of the release.

It uses [svenstaro/upload-release-action](https://github.com/svenstaro/upload-release-action) for uploading the release assets.

```yml
jobs:
  changelog:
    name: Generate changelog
    runs-on: ubuntu-latest
    outputs:
      release_body: ${{ steps.git-cliff.outputs.content }}
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - name: Generate a changelog
        uses: orhun/git-cliff-action@v2
        id: git-cliff
        with:
          config: cliff.toml
          args: -vv --latest --strip header
        env:
          OUTPUT: CHANGES.md

      # use release body in the same job
      - name: Upload the binary releases
        uses: svenstaro/upload-release-action@v2
        with:
          file: binary_release.zip
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          tag: ${{ github.ref }}
          body: ${{ steps.git-cliff.outputs.content }}

  # use release body in another job
  upload:
    name: Upload the release
    needs: changelog
    runs-on: ubuntu-latest
    steps:
      - name: Upload the binary releases
        uses: svenstaro/upload-release-action@v2
        with:
          file: binary_release.zip
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          tag: ${{ github.ref }}
          body: ${{ needs.changelog.outputs.release_body }}
```

## Credits

This action is based on [lycheeverse/lychee-action](https://github.com/lycheeverse/lychee-action) and uses [git-cliff](https://github.com/orhun/git-cliff).
