# Cloud Deploy and Skaffold Example

This document demonstrates how to use Google Cloud Deploy in combination with Skaffold to deploy your application to Google Cloud.

## Prerequisites

Before proceeding, ensure you have the following:

- Google Cloud SDK installed ([Installation Guide](https://cloud.google.com/sdk/docs/install)).
- A Google Cloud project with billing enabled.
- The Cloud Deploy API enabled for your project:
  ```bash
  gcloud services enable clouddeploy.googleapis.com
  ```
- Skaffold installed ([Installation Guide](https://skaffold.dev/docs/install/)).

## Steps

### 1. Register the Delivery Pipeline and Targets

Replace `<project>` with your Google Cloud project ID.

Apply the configuration:

```bash
export PROJECT=<project>
gcloud deploy apply --file=clouddeploy.yaml --region=us-central1 --project=$PROJECT
```

### 2. Create a Release

Once your image is built and pushed, create a release using Cloud Deploy:

```bash
gcloud deploy releases create test-release-005 \
  --project=$PROJECT \
  --region=us-central1 \
  --delivery-pipeline=my-run-demo-app-1 \
  --images=my-app-image=us-docker.pkg.dev/cloudrun/container/hello@sha256:4a856b6f1c3ce723a456ddc2adfbb794cbfba93f727e2d96fcf6540bd0d6fff4
```

Replace `<digest>` with the SHA256 digest of the image built by Skaffold.

### 3. Promote the Release

Promote the release to the desired target (e.g., `prod`):

```bash
gcloud deploy rollouts create --release=test-release-001 --project=$PROJECT --region=us-central1 --target=prod
```

## Additional Notes

- You can view the progress of your deployments in the [Google Cloud Console](https://console.cloud.google.com/deploy).
- For detailed information about Cloud Deploy, refer to the [official documentation](https://cloud.google.com/deploy/docs).
- Skaffold documentation is available at [https://skaffold.dev](https://skaffold.dev).

## Troubleshooting

- Ensure your project is correctly set:
  ```bash
  gcloud config set project $PROJECT
  ```
- Verify your service account has sufficient permissions:
  ```bash
  gcloud projects get-iam-policy $PROJECT
  ```
- If deployment fails, use the `--verbosity=debug` flag to get detailed logs.

