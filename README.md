# docs-upload-action GitHub action

This GitHub action upload documentation build output to the storage.

## Inputs

- `github-token` (required) - GitHub access token for authentication and authorization
- `storage-bucket` (required) - The name of the storage bucket where the built documentation will be stored. After the documentation is successfully built, it will be uploaded to this specified bucket.
- `storage-endpoint` (required) - The endpoint URL of the storage service.
- `storage-access-key-id` (required) - The access key ID associated with the account that has permission to access and upload to the specified storage bucket.
- `storage-secret-access-key` (required) - The secret access key associated with the account.
- `storage-region` (required) - The region where the specified storage bucket is located.
- `stage` (default: `stable`) - Deployment environment for the documentation

## Usage

Create a file named `.github/workflows/post-build.yml` in your repo.

This workflow must run on completed [build workflow](https://github.com/diplodoc-platform/docs-build-action) and performs the following:
- Uploads build output to storage if the build log does not include errors.
- Adds a comment to the pull request with the build log or preview link.

```yaml
name: Upload & Message

on:
  workflow_run:
    workflows: [Build]
    types:
      - completed

jobs:
  post-build:
    permissions: write-all
    runs-on: ubuntu-latest
    steps:
      - name: Upload
        uses: diplodoc-platform/docs-upload-action@v3
        if: github.event.workflow_run.conclusion == 'success'
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          storage-endpoint: ${{ vars.DIPLODOC_STORAGE_ENDPOINT }}
          storage-region: ${{ vars.DIPLODOC_STORAGE_REGION }}
          storage-bucket: ${{ vars.DIPLODOC_STORAGE_BUCKET }}
          storage-access-key-id: ${{ secrets.DIPLODOC_ACCESS_KEY_ID }}
          storage-secret-access-key: ${{ secrets.DIPLODOC_SECRET_ACCESS_KEY }}

      - name: Comment message
        uses: diplodoc-platform/docs-message-action@v3
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          project-link: ${{ vars.DIPLODOC_PROJECT_LINK }}
```
