
# Deployment Process and Workflow Guide

This document outlines the deployment process for our application using **GitHub Actions** and **Vercel** for cloud hosting. Follow this guide to ensure smooth deployments across environments.

---

## Deployment Workflow Overview

Our application uses three environments for deployment:
1. **Development**: Automatically deploys from PRs and merged code.
2. **Staging**: Deployments are triggered via workflow dispatch for testing pre-releases.
3. **Production**: Deployments are triggered via workflow dispatch for releases.

### Workflow Summary:
| Action                        | Trigger                                  | Deployment Target | GitHub Artifact |
|-------------------------------|------------------------------------------|-------------------|-----------------|
| PR Creation                   | On PR open/synchronize/reopen           | Vercel Preview    | None            |
| PR Merged                     | On PR merge                             | Development       | None            |
| Staging Deployment            | Workflow dispatch + Pre-release trigger | Staging           | Pre-release     |
| Production Deployment         | Workflow dispatch + Release trigger     | Production        | Release         |
| Production (Preview) Deployment | Workflow dispatch      | Vercel Preview        | None         |
| Staging (Preview) Deployment    | Workflow dispatch      | Vercel Preview        | None         |

---

## Deployment Instructions

### 1. **Preview Deployment (on PR creation)**

- **When:** Every time a new PR is created, synchronized, or reopened.
- **What Happens:**
  - A **Vercel Preview Deployment** is triggered to the **Development environment**.
  - The deployment URL is posted as a comment in the PR for testing.

#### Steps:
1. Create a new branch for your feature or bug fix.
2. Push your changes to the branch and open a PR.
3. Wait for the GitHub Action to trigger a **preview deployment**.
4. Check the deployment URL in the PR comments and test your changes.

---

### 2. **Development Deployment (on PR merge)**

- **When:** When a PR is merged into the default branch (`main`).
- **What Happens:**
  - A **Vercel Development Deployment** is triggered to update the **Development environment**.

#### Steps:
1. Once the feature or bug fix is tested and reviewed, merge the PR into `main`.
2. Wait for the GitHub Action to trigger the development deployment.
3. Verify the updated **Development environment** in Vercel.

---

### 3. **Staging Deployment**

- **When:** You need to test a **pre-release** on the staging environment.
- **Trigger:** Manually trigger the **Create Release** workflow via the GitHub Actions UI.

#### Workflow Options:
- **`is_draft = true`:** Creates a draft pre-release and triggers a **preview deployment** to staging.
- **`is_draft = false`:** Creates a published pre-release and triggers a **staging deployment**.

#### Steps:
1. Go to the **Actions** tab in your repository.
2. Select the **Create Release** workflow from the list.
3. Click **Run Workflow** and fill in:
   - **`ref`:** Specify the branch or tag you want to deploy.
   - **`release_type`:** Choose `major`, `minor`, or `patch` for the pre-release version.
   - **`is_prerelease` (checkbox):** Ensure this is checked for staging deployments.
   - **`is_draft` (true/false):** Decide if the pre-release should be a draft or published.
4. Monitor the workflow logs and verify:
   - Draft or published pre-release created.
   - Deployment URL (preview or staging) is available for testing.

---

### 4. **Production Deployment**

- **When:** You are ready to publish a new **release** to the production environment.
- **Trigger:** Manually trigger the **Create Release** workflow via the GitHub Actions UI.

#### Recommended Workflow:
1. Start by creating and testing a **pre-release** in the **Staging environment**.
2. Once the pre-release is validated, mark it as a **Release** via the GitHub UI.

#### Workflow Options:
- **`is_prerelease = false`:** Ensure this is unchecked for production deployments.
- **`is_draft = true`:** Creates a draft release and triggers a **preview deployment** to production.
- **`is_draft = false`:** Creates a published release and triggers a **production deployment**.

#### Steps:
1. Go to the **Actions** tab in your repository.
2. Select the **Create Release** workflow from the list.
3. Click **Run Workflow** and fill in:
   - **`ref`:** Specify the branch or tag you want to deploy.
   - **`release_type`:** Choose `major`, `minor`, or `patch` for the release version.
   - **`is_prerelease` (checkbox):** Ensure this is unchecked for production deployments.
   - **`is_draft` (true/false):** Decide if the release should be a draft or published.
4. Monitor the workflow logs and verify:
   - Draft or published release created.
   - Deployment URL (preview or production) is available for testing.

---

### 5. **Hotfix for Staging or Production**

- **When:** You need to fix a critical issue in staging or production.
- **Trigger:** Create a feature branch for the fix and use the **Create Release** workflow.

#### Steps:
1. Pull the latest changes from the main branch.
   ```bash
   git fetch 
   ```
2. Checkout the tag of the release you want to fix.
   ```bash
   git checkout <tag_name>
   ```
3. Create a feature branch for the hotfix:
   ```bash
   git checkout -b hotfix/<descriptive_name>
   ```
4. Push your newly created branch.
   ```bash
   git push origin HEAD
   ```
5. Test the hotfix in the desired environment (staging or production) via the preview deployment. This can be done by triggering the **Trigger Vercel Deployment** workflow via [GitHub Actions](/actions/workflows/trigger-deployment.yml).
6. Create the deployment:
   - **For Staging:**
      - Trigger the **Create Release** workflow via GitHub Actions.
      - Use `ref` to specify the branch or tag you want to deploy.
      - Use `is_draft = true` for a draft pre-release (optional) or `is_draft = false` for a published pre-release.
      - Use `is_prerelease = true` to create a staging deployment.
      - Use `release_type = Patch` to create a patched version.
   - **For Production:**
      - Trigger the **Create Release** workflow via GitHub Actions.
      - Use `ref` to specify the branch or tag you want to deploy.
      - Use `is_draft = true` for a draft release (optional) or `is_draft = false` for a published release.
      - Use `is_prerelease = false` to create a production deployment.
      - Use `release_type = Patch` to create a patched version.
7. After the hotfix is deployed, you must proceed to create a PR to update the main branch. (DO NOT FORGET TO PULL THE LATEST CHANGES FROM THE MAIN BRANCH FIRST)

---

## Workflow Dispatch Options

When triggering workflows via the **Actions** tab, the following options must be provided:

### Inputs for Both Staging and Production Workflows:
1. **`ref` (dropdown):** The Git reference (branch or tag) to deploy. Defaults to **main**.
2. **`is_prerelease` (checkbox):** Select this for staging deployments, uncheck it for production.
2. **`is_draft` (boolean):** Determines if the pre-release or release will be marked as a draft.
3. **`release_type` (dropdown):** Select `major`, `minor`, or `patch` to determine the semantic version of the release.

---

## Environment Details

| **Environment** | **Trigger**                           | **Target**       | **Type**         | **Purpose**                                 |
|------------------|---------------------------------------|------------------|------------------|---------------------------------------------|
| Development      | PR creation or merge                 | `Vercel Dev`     | Preview/Deploy   | Test new features or fixes.                |
| Staging          | Workflow dispatch (pre-release)      | `Vercel Staging` | Preview/Deploy   | Pre-release testing for staging.           |
| Production       | Workflow dispatch (release)          | `Vercel Prod`    | Preview/Deploy   | Production release for end users.          |

---

## Notes for Developers

- Always test features in the **Development environment** before promoting to staging or production.
- Use **draft pre-releases** or **draft releases** for testing preview deployments without affecting the actual environments.
- Coordinate with the team before triggering **staging** or **production deployments**, especially for hotfixes.


## Troubleshooting

### 1. Deployment Not Triggered

**Symptoms:**
- Workflow does not run after a PR is created, merged, or manually dispatched.

**Causes:**
- The branch filter in the workflow is incorrect.
- Missing permissions for the `GITHUB_TOKEN`.

**Solutions:**
1. Ensure the workflow listens to the right events and branches in your YAML file:

```yaml
on:
  pull_request:
    branches:
      - main
```

2. Check `permissions` for the `GITHUB_TOKEN`:

```yaml
permissions:
  contents: write
```

---

### 2. Vercel Deployment Fails

**Symptoms:**
- Deployment step fails with errors like `VERCEL_PROJECT_ID not set`.

**Causes:**
- Incorrect or missing Vercel secrets (`VERCEL_PROJECT_ID`, `VERCEL_TOKEN`).

**Solutions:**
1. Confirm the secrets are set in the repository settings under **Secrets and variables**.
2. Validate the secrets are correctly passed in the workflow:

```yaml
env:
  VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
  VERCEL_TOKEN: ${{ secrets.VERCEL_TOKEN }}
```

---

### 3. Incorrect Version in Release

**Symptoms:**
- The tag or version number in the release is incorrect.

**Causes:**
- The workflow does not properly calculate the next version.

**Solutions:**
1. Ensure the workflow calculates the next version correctly using semantic versioning:

```bash
# Example: Incrementing a patch version
latest_version="1.0.0"
next_version=$(echo $latest_version | awk -F. '{$3++; print $1"."$2"."$3}')
```

2. Debug the version logic by printing the calculated value in the logs:

```yaml
- name: Debug Version
  run: echo "Next version: ${{ env.next_version }}"
```

---

### 4. Permissions Error for GitHub API Requests

**Symptoms:**
- Error: `Resource not accessible by integration` or `403 Forbidden`.

**Causes:**
- The `GITHUB_TOKEN` is missing `workflow` permissions.

**Solutions:**
- Add `workflow: write` to your workflow permissions:

```yaml
permissions:
  contents: write
  workflows: write
```

### 5. Reverting a Release and Tag

**Symptoms:**
- A release and tag were created incorrectly, and you need to delete them to create a new release with the same tag.

**Causes:**
-	Incorrect release information or a mistake in the associated tag.

**Solutions:**
##### 1. Delete the Release:
-  Go to the Releases section of your GitHub repository.
-  Find the release associated with the incorrect tag.
-  Click Delete this release.

##### 2. Delete the Tag:
###### Via the GitHub UI:
-  Go to the Tags section of your GitHub repository.
-  Find the the incorrect tag.
-  Click Delete this tag.

###### Locally via Git:

```bash
git push --delete origin <tag-name>
git tag -d <tag-name>
```

##### 3.	Create a New Release:
- Go to the Actions section of your repository.
-	Click on `Create Release` workflow, select the ref branch, and provide the correct information.
