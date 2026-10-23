# Upload to Thrungames

Uploads a game build to [Thrungames](https://thrungames.com) from CI, using the
[`thrungames` CLI](https://thrungames.com/cli). The build lands as a draft, or
goes straight to review with `submit: true`.

Create an upload token for your game (`thrungames tokens create`, or on the
game's page) and store it as a secret named `THRUNGAMES_TOKEN`.

## GitHub Actions

```yaml
- uses: infobits-io/thrungames-upload-action@v1
  with:
    token: ${{ secrets.THRUNGAMES_TOKEN }}
    path: dist
    version: ${{ github.ref_name }}
    submit: ${{ startsWith(github.ref, 'refs/tags/') }}
```

## Forgejo / Gitea Actions

```yaml
- uses: https://github.com/infobits-io/thrungames-upload-action@v1
  with:
    token: ${{ secrets.THRUNGAMES_TOKEN }}
    path: dist
```

## GitLab CI

```yaml
upload:
  image: { name: infobits/thrungames-cli:1, entrypoint: [""] }
  script: [thrungames upload dist --version "$CI_COMMIT_TAG" --submit]
  variables: { THRUNGAMES_TOKEN: $THRUNGAMES_TOKEN }
  rules: [{ if: $CI_COMMIT_TAG }]
```

## Inputs

| Input | Default | |
| --- | --- | --- |
| `token` | required | Upload token for the game. |
| `path` | game.json `root`, else the repository root | Folder or `.zip` to upload. |
| `version` | game.json `version` | Version name. Required unless game.json has a `version` (the image has no git). |
| `submit` | `false` | Send to review instead of saving a draft. |

The container's working directory must be the checkout. GitHub mounts it at
`/github/workspace` and sets it as the working directory for Docker actions, so
relative paths such as `dist` work.
