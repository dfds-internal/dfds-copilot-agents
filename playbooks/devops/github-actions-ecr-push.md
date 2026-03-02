Create a production-ready GitHub Actions workflow that builds and pushes a Docker image to AWS ECR.

The workflow must:

GENERAL
- Be clean, minimal, and enterprise-ready.
- Not hardcode any credentials or environment-specific values.
- Use environment variables at the top of the file for easy modification.

ENV VARIABLES (must be defined at top of workflow):
- AWS_REGION=eu-central-1
- DOCKERFILE_PATH=./ChangeThisToYourDockerfilePath
- BUILD_CONTEXT=./ChangeThisToYourBuildContext
- ECR_REGISTRY=ChangeThisToYourEcrRegistry
- ECR_REPOSITORY=ChangeThisToYourEcrRepository

TRIGGER
- Run only on push to main branch.
- Optionally allow commented examples for:
  - feature/*
  - release/*

AUTHENTICATION
- Use GitHub Secrets (do not hardcode):
  - ChangeThisToYOURECRACCESSKEY
  - ChangeThisToYOURECRSECRETKEY
- Configure AWS credentials using aws-actions/configure-aws-credentials.

ACTIONS TO USE
- actions/checkout
- aws-actions/configure-aws-credentials
- aws-actions/amazon-ecr-login
- docker/build-push-action

BUILD & PUSH REQUIREMENTS
- Build using:
  - context: ${{ env.BUILD_CONTEXT }}
  - file: ${{ env.DOCKERFILE_PATH }}
- Push image to:
  ${{ env.ECR_REGISTRY }}/${{ env.ECR_REPOSITORY }}
- Tag image with:
  - latest
  - sha-<shortSha>
- Derive shortSha from the first 7 characters of GITHUB_SHA inside the workflow.
- Fail fast on errors.

OUTPUT
- Generate the full YAML file for:
  .github/workflows/push-to-ecr.yml

Also include a short README snippet explaining:
- Which environment variables must be replaced.
- Which GitHub Secrets must be configured.
- That AWS credentials must have ECR push permissions.
- Do not include external URLs inside the workflow YAML.
