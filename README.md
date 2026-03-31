# mapviewer community

Minimal example repository with a simple collaboration workflow based on short-lived branches, pull requests, unit tests and controlled deployments.

## Proposed workflow

This repository intentionally avoids GitFlow.

### Branching model

- `main`: always stable and deployable.
- `feature/<name>`: short-lived branches for each change.
- Optional release tags: `v1.0.0`, `v1.1.0`, etc. for production deployments.

### Collaboration rules
1. Create a branch from `main`.
2. Open a pull request back to `main`.
3. CI runs unit tests and build validation automatically.
4. Merge with **Squash and merge** to keep history clean.
5. Staging deployment is **manual** and protected by GitHub Environment reviewers
6. Production deployment is **manual** and protected by GitHub Environment reviewers.

## CI/CD included

### 1. Continuous Integration

File: `.github/workflows/ci.yml`

Runs on:
- pull requests to `main`
- direct pushes to `main`

Steps:
- install dependencies
- run unit tests with Vitest
- build the application

### 2. Automatic deployment to staging (test)

File: `.github/workflows/deploy-staging.yml`

Runs manually by a user with write access to the rep.

### 3. Controlled deployment to production

File: `.github/workflows/deploy-production.yml`

Runs only through **manual trigger** (`workflow_dispatch`).

Recommended protection:
- create a GitHub Environment called `production`
- configure **Required reviewers** with the users allowed to approve production deployments
- optionally restrict the branch/tag patterns that can deploy

## Repository protection recommended in GitHub

### Branch protection for `main`

Enable:
- require pull request before merging
- require status checks to pass
- require conversation resolution before merging
- restrict direct pushes
- optionally require 1 or 2 approvals

### Environments

Create two environments:
- `staging`: no approval required
- `production`: required reviewers enabled

## Local execution

```bash
npm install
npm test
npm run build
```

Serve the app locally with any static server pointing to `dist/` or `src/`.

## Notes about deployment

The script `scripts/deploy-static.sh` is a placeholder.
Replace it with the real deployment command for your infrastructure.

Examples:
- rsync to a VM
- upload to S3 / Azure Blob
- deploy to Kubernetes ingress-backed static hosting
- deploy to an internal web server

## CODEOWNERS example

If you want mandatory review from maintainers, add a `.github/CODEOWNERS` file like this:

```text
* @org/frontend-maintainers @org/devops-maintainers
```

## Why this model is simpler than GitFlow

- only one permanent branch (`main`)
- no `develop`, `release`, or `hotfix` branches by default
- staging comes from merged code in `main`
- production is controlled with approvals, not with extra branching complexity
