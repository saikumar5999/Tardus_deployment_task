# GEICO CI/CD example

This project is a Java 17 Maven application. Below is a practical branch strategy and CI/CD path using a GEICO-style example.

## Branches

- `main`: production branch
- `develop`: integration and QA branch
- `feature/geico-claim-discount-api`: a sample feature branch

## Real step-by-step flow

### 1. Create the main branch

If this is a new repository, your first stable branch is `main`.

```powershell
git checkout -b main
git push -u origin main
```

### 2. Create the develop branch from main

Developers merge completed features into `develop` first.

```powershell
git checkout main
git checkout -b develop
git push -u origin develop
```

### 3. Create a feature branch from develop

Example business feature for GEICO: add a claim discount eligibility API.

```powershell
git checkout develop
git checkout -b feature/geico-claim-discount-api
```

### 4. Make code changes on the feature branch

Developer work happens only in the feature branch.

```powershell
mvn clean verify
git add .
git commit -m "Add GEICO claim discount eligibility flow"
git push -u origin feature/geico-claim-discount-api
```

### 5. CI runs automatically on the feature branch

Workflow: `.github/workflows/ci.yml`

When you push the feature branch:

1. GitHub Actions checks out the code
2. Installs Java 17
3. Runs `mvn clean verify`

If CI fails, fix the branch before requesting review.

### 6. Open a pull request to develop

Merge path:

- `feature/geico-claim-discount-api` -> `develop`

At this point:

1. CI runs again for the pull request
2. Reviewers approve the code
3. Branch protection ensures checks pass before merge

### 7. Merge into develop

After merge, the staging deployment workflow starts.

Workflow: `.github/workflows/deploy-staging.yml`

Purpose:

- validate the integrated build
- deploy to QA or staging
- allow testers or business users to verify the GEICO feature

### 8. Test in staging

Typical validation in staging:

- API functional test
- regression test
- business approval
- security or compliance checks if required

### 9. Promote develop to main

When staging is approved, open a pull request:

- `develop` -> `main`

Again:

1. CI runs on the pull request
2. reviewers approve
3. merge happens only after all checks pass

### 10. Production deployment starts from main

Workflow: `.github/workflows/deploy-production.yml`

After merge into `main`:

1. production build is created
2. GitHub environment protection can request manual approval
3. deployment runs using production secrets
4. app is released to production

## What CI means here

CI is Continuous Integration.

In this example it means:

- every branch push is validated automatically
- bad code is blocked before merge
- the team integrates changes continuously

## What CD means here

CD is Continuous Delivery or Continuous Deployment.

In this example it means:

- `develop` automatically deploys to staging
- `main` is the only branch allowed to deploy to production
- approvals and secrets are controlled in GitHub Actions environments

## Recommended controls for an enterprise client like GEICO

- protect `main` and `develop`
- require pull request reviews
- require CI checks before merge
- require production approval on the `production` environment
- keep credentials only in repository or environment secrets
- do not allow direct pushes to `main`

## Simple picture of the flow

`main` -> production  
`develop` -> staging  
`feature/geico-claim-discount-api` -> developer branch

Flow:

`feature/geico-claim-discount-api` -> `develop` -> `main` -> production
