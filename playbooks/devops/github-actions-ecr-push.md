# 🚀 GitHub Actions — Build & Push Docker Image to AWS ECR

Create a reusable (dynamic) GitHub Actions workflow that builds and pushes a Docker image to AWS ECR.

**Goal:**
- Works across many repositories.
- Users **must** only modify values in the `env` block at the top.
- No hardcoded credentials.
- Clean, minimal, enterprise-ready.

---

## ⚙️ TOP-LEVEL ENV

> **⚠️ EDIT ONLY THIS SECTION** - **Must** be defined once at the top:

- `AWS_REGION=ChangeThisToYourAwsRegion` (default eu-central-1)
- `DOCKERFILE_PATH=./ChangeThisToYourDockerfilePath`
- `BUILD_CONTEXT=./ChangeThisToYourBuildContext`
- `ECR_REGISTRY=ChangeThisToYourEcrRegistry`
- `ECR_REPOSITORY=ChangeThisToYourEcrRepository`
- `AWS_ACCESS_KEY_ID_SECRET_NAME=ChangeThisToYourAccessKeySecretName`
- `AWS_SECRET_ACCESS_KEY_SECRET_NAME=ChangeThisToYourSecretKeySecretName`

---

## 🔔 TRIGGERS

- push to main
- include commented examples for:
  - feature/*
  - release/*

---

## 🔐 AUTHENTICATION

- Use `aws-actions/configure-aws-credentials@v4`
- Use dynamic secret lookup **exactly like this:**

```yaml
aws-access-key-id: ${{ secrets[env.AWS_ACCESS_KEY_ID_SECRET_NAME] }}
aws-secret-access-key: ${{ secrets[env.AWS_SECRET_ACCESS_KEY_SECRET_NAME] }}
aws-region: ${{ env.AWS_REGION }}
```

---

## 📦 ACTIONS

- `actions/checkout@v4`
- `aws-actions/configure-aws-credentials@v4`
- `aws-actions/amazon-ecr-login@v2`
- `docker/build-push-action@v5`

---

## 🏷️ TAGGING

- `latest`
- `sha-<shortSha>` (first 7 characters of GITHUB_SHA)
- Derive shortSha using a separate step and GITHUB_OUTPUT.

---

## 🐳 BUILD & PUSH

- context: `${{ env.BUILD_CONTEXT }}`
- file: `${{ env.DOCKERFILE_PATH }}`
- push to:
  `${{ env.ECR_REGISTRY }}/${{ env.ECR_REPOSITORY }}`
- **Fail fast** on errors.

---

## 📤 OUTPUT

1) Full YAML for:
   `.github/workflows/push-to-ecr.yml`

2) After the YAML, output **ONLY** a short markdown section titled:

## Validation Checklist

The checklist **must** include exactly these items:

- [ ] ECR repository requested/created (DFDS wiki: https://wiki.dfds.cloud/en/playbooks/supporting-services/ecr/request_a_new_ecr_repository)
- [ ] AWS ECR push credentials obtained (DFDS wiki: https://wiki.dfds.cloud/en/playbooks/supporting-services/ecr/obtain-aws-ecr-push-credentials)
- [ ] GitHub Secrets created and names match:
      AWS_ACCESS_KEY_ID_SECRET_NAME and AWS_SECRET_ACCESS_KEY_SECRET_NAME
- [ ] AWS_REGION matches the ECR registry region
- [ ] DOCKERFILE_PATH points to the correct Dockerfile
- [ ] BUILD_CONTEXT points to the correct build context
- [ ] Push to main branch triggered successfully
- [ ] Confirm image tags exist in ECR: latest and sha-<shortSha>

---

> **⚠️ IMPORTANT:**
> - Do **NOT** include any README section.
> - Do **NOT** include any external URLs inside the workflow YAML.
> - Only output YAML + the final Validation Checklist section.
