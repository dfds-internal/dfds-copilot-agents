# GitHub Actions – Build & Push Docker Image to AWS ECR

Create a reusable GitHub Actions workflow that builds and pushes a Docker image to AWS ECR.

## Workflow

```yaml
name: Build and Push to ECR

env:
  AWS_REGION: eu-central-1
  DOCKERFILE_PATH: ./ChangeThisToYourDockerfilePath
  BUILD_CONTEXT: ./ChangeThisToYourBuildContext
  ECR_REGISTRY: ChangeThisToYourEcrRegistry
  ECR_REPOSITORY: ChangeThisToYourEcrRepository

on:
  push:
    branches:
      - main
      # Optionally enable additional patterns:
      # - feature/*
      # - release/*

jobs:
  build-and-push:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.ChangeThisToYOURECRACCESSKEY }}
          aws-secret-access-key: ${{ secrets.ChangeThisToYOURECRSECRETKEY }}
          aws-region: ${{ env.AWS_REGION }}

      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2

      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          context: ${{ env.BUILD_CONTEXT }}
          file: ${{ env.DOCKERFILE_PATH }}
          push: true
          tags: |
            ${{ env.ECR_REGISTRY }}/${{ env.ECR_REPOSITORY }}:latest
            ${{ env.ECR_REGISTRY }}/${{ env.ECR_REPOSITORY }}:sha-${{ github.sha }}
```

## Setup Instructions

1. **Replace the `env` variables** at the top of the workflow with your actual values:
   - `AWS_REGION` – update if your ECR repository is in a different region (default: `eu-central-1`)
   - `DOCKERFILE_PATH` – path to your `Dockerfile` (e.g. `./src/MyService/Dockerfile`)
   - `BUILD_CONTEXT` – Docker build context (e.g. `./src/MyService`)
   - `ECR_REGISTRY` – your ECR registry URI (e.g. `123456789012.dkr.ecr.eu-central-1.amazonaws.com`)
   - `ECR_REPOSITORY` – your ECR repository name (e.g. `my-service`)

2. **Add the following GitHub Secrets** to your repository (**Settings → Secrets and variables → Actions**):
   - `ChangeThisToYOURECRACCESSKEY` – AWS access key ID with ECR push permissions (requires `ecr:GetAuthorizationToken`, `ecr:BatchCheckLayerAvailability`, `ecr:PutImage`, and related permissions)
   - `ChangeThisToYOURECRSECRETKEY` – AWS secret access key

   > **Security tip**: For production workloads consider using [OIDC federation](https://docs.github.com/en/actions/security-for-github-actions/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services) with `role-to-assume` instead of long-lived access keys.

3. **Enable additional branch triggers** (optional): Uncomment the `feature/*` and/or `release/*` entries under `branches` if you want the workflow to run on those branches as well.

## Notes

- The image is tagged with both `latest` and the full Git commit SHA (`sha-<sha>`) to support immutable image references.
- AWS credentials are never hardcoded; they are always read from GitHub Secrets.
- Uses official, maintained actions:
  - [`aws-actions/configure-aws-credentials`](https://github.com/aws-actions/configure-aws-credentials)
  - [`aws-actions/amazon-ecr-login`](https://github.com/aws-actions/amazon-ecr-login)
  - [`docker/build-push-action`](https://github.com/docker/build-push-action)
