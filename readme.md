# GitHub Action: Build and deploy front-end

An easy-to-use action to build and deploy a new version of an application front end. Once set up, any tag starting with the letter `v` will trigger an attempt to build and deploy the project. Tags should be [SemVer](https://semver.org)-compliant, e.g. `v1.2.3`.

Assumptions:

- Your application uses Yarn
- The `build` script in `package.json` builds the project for production

## Setting up

In your project, create a new workflow file in `/.github/` (e.g. `/.github/release.yml`) and populate it with this:

```yaml
name: Release
on:
  push:
    tags:
      - "v*"

jobs:
  build-deploy:
    runs-on: ubuntu-20.04
    steps:
      - name: Build and deploy 🚀
        uses: kabal-com/action-deploy-frontend@v5
        with:
          app-name: <YOUR-APP-NAME>
          aws-access-key-id: ${{ secrets.<SOME-SECRET-KEY-ID> }}
          aws-access-key-secret: ${{ secrets.<SOME-SECRET-KEY-SECRET> }}
          aws-s3-bucket: <NAME-OF-S3-BUCKET>
```

## Configuration

You need to replace `<VARS>` in the code above with actual values. Set up two GitHub secrets in your repository with the S3 key ID (e.g. `AWS_ACCESS_KEY_ID`) and secret (e.g. `AWS_ACCESS_KEY_SECRET`), and use those names above.

Some optional input keys:

- `aws-region`: The AWS region where the S3 bucket; defaults to `eu-west-1`
- `built-files`: Path to the files to be copied once the project builds, relative to the repository root. Defaults to `dist/`
- `project-dir`: Path to the front-end project, where `package.json` is located. Defaults to `.` (i.e. the repository root)
- `version`: A version to label the deployment with. This should be the letter `v` followed by a SemVer-compliant version string, e.g. `v1.2.3` or `v2.0.0-beta`. Defaults to the tag name being pushed. Don't change this unless you know what you're doing

The built files are placed in the S3 bucket under `/<YOUR-APP-NAME>/<version>/`.
