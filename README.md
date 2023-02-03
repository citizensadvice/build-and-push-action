# Build & Push To ERC

Build and push a Docker image to the CA public ECR in one action.

By default it will push the image with three different tags:

- **Commit sha** as documented [here](https://git-scm.com/docs/git-rev-parse#Documentation/git-rev-parse.txt---shortlength). e.g. `e1b18d15d2b5558d275920ab5b5650a5c36bb1ee`
- **Branch and/or Tag name**, e.g. `main`, `v1.0.0`
- **latest**

Please note that the ECR repository must be created beforehand. Repository creation is not currently within the scope of this Action.

## Inputs

| Input | Description | Required | Default |
|---|---|---|---|
| `aws_access_key` | Access key for a user with permission to create and update images in the CA public ECR | Yes | |
| `aws_secret_key` | Secret key for the access key | Yes | |
| `dockerfile_context` | The path to the Dockerfile context. Will default to the root of the project | No | `.`
| `repository_name` | The name of the image repository. Is used to name the docker image. Must may contain lowercase and uppercase letters, digits, underscores, periods and dashes only. | Yes | |
| `auth_token` | A token with permission to clone the repository. Will usually be GITHUB_TOKEN | Yes | |

As all CA repositories have access to a `PUBLIC_PUSH_ECR_AWS_KEY` and `PUBLIC_PUSH_ECR_AWS_SECRET`, they can be used without any issues.

## Outputs

| Name | Description |
|---|---|
| `image_id` | The ID of the built image |
| `image_digest` | The digest of the built image |
| `image_tags` | A CSV list of the image labels and tags |


## Example usage

Located in `.github/workflows/build-and-push.yml`:

```yaml
name: Build & Push Image To ECR
on:
  push:
    branches:
      - main
      - "[0-9]*"
      - "v[0-9]*"
    tags:
      - v*

jobs:
  build:
    name: Publish image
    runs-on: ubuntu-22.04
    steps:
      - name: Build and push to ECR
        uses: citizensadvice/build-and-push-action@v1
        with:
            aws_access_key: ${{ secrets.PUBLIC_PUSH_ECR_AWS_KEY }}
            aws_secret_key: ${{ secrets.PUBLIC_PUSH_ECR_AWS_SECRET }}
            dockerfile_context: '.'
            repository_name: <REPOSITORY NAME HERE>
            auth_token: ${{ secrets.GITHUB_TOKEN }}
```

The `on` conditions and branch matching can be changes to whatever suites your team best. If you require assistance implementing a custom solution, please send a message to `#devops-support` in Slack.
