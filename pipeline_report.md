# Altrodav Technologies - Task 11 CI/CD Pipeline Report
*Name:* Karthik
*Role:* DevOps Engineer Intern
*Date:* June 19, 2026

---

## 1. Pipeline Architecture

This project implements a CI/CD pipeline using GitHub Actions for a Python application. The pipeline consists of three independent workflows:

- *build.yml* - Installs dependencies, verifies the application runs, and generates a build artifact
- *test.yml* - Runs linting (flake8) and unit tests (pytest) on every push and pull request
- *deploy.yml* - Simulates deployment to a production environment, triggered only on push to the main branch

All workflows run on ubuntu-latest GitHub-hosted runners and use official GitHub Actions (actions/checkout, actions/setup-python, actions/upload-artifact) for consistency and reliability.

---

## 2. Workflow Details

| Workflow   | Trigger                          | Purpose                                  |
|------------|-----------------------------------|-------------------------------------------|
| build.yml  | push/PR to main, develop          | Install deps, verify build, upload artifact |
| test.yml   | push/PR to main, develop          | Run flake8 linting and pytest unit tests |
| deploy.yml | push to main only                 | Simulate deployment with env variables   |

Branching strategy used: main (stable/production) and develop (active development). Workflows trigger on both branches for build/test, but deployment is restricted to main only — preventing unverified code from reaching production.

---

## 3. Build Analysis

- Dependencies (pytest, flake8) installed cleanly via pip install -r requirements.txt
- Application (app.py) executed successfully as part of build verification
- Build artifact (build_status.txt) generated and uploaded using actions/upload-artifact
- Build pipeline completed in approximately 12 seconds

---

## 4. Test & Code Quality Analysis

- 4 unit tests written using pytest, covering add, subtract, multiply, and divide functions (including a divide-by-zero edge case)
- All 4 tests passed both locally and inside the GitHub Actions runner
- flake8 linting initially flagged PEP8 spacing issues (E302, E305 - missing blank lines between functions)
- Issues were fixed locally, re-verified, and confirmed clean before pushing
- Test pipeline completed in approximately 10 seconds

---

## 5. Deployment Summary

- Deployment is simulated (no real server/cloud target, per assessment scope)
- Environment variables (DEPLOY_ENV, APP_VERSION) set dynamically using $GITHUB_ENV
- Deployment restricted to main branch only, ensuring only reviewed/merged code is "deployed"
- Deployment logs confirmed: dependency installation, environment variable setup, simulated deployment execution, and final verification step all completed successfully
- Deploy pipeline completed in approximately 12 seconds

---

## 6. Monitoring Results

| Metric            | Value |
|--------------------|-------|
| Total Runs         | 3     |
| Successful Runs    | 3     |
| Failed Runs        | 0     |
| Success Rate       | 100%  |
| Build Duration     | 12s   |
| Test Duration      | 10s   |
| Deploy Duration    | 12s   |

All three pipelines passed on first execution after local pre-testing (running pytest and flake8 locally before pushing caught issues early, avoiding failed CI runs). Full evidence available in workflow_monitoring.txt and GitHub Actions run logs.

---

## 7. Best Practices Research

### Continuous Integration (CI)
- Practice of automatically building and testing code every time it is pushed or merged
- Catches integration issues early, before they reach production
- In this project: build.yml and test.yml implement CI by running on every push/PR to main and develop

### Continuous Deployment (CD)
- Extends CI by automatically deploying code that passes all tests, without manual intervention
- Reduces release cycle time and human error in deployment steps
- In this project: deploy.yml implements CD, but restricted to main branch only as a safety gate

### Branching Strategies
- main branch represents stable, production-ready code
- develop branch is used for ongoing feature work and integration before merging to main
- Feature branches (not used in this small project, but standard in larger teams) branch off develop and merge back via pull requests
- This separation prevents unfinished or broken code from reaching production directly

### Rollback Mechanisms
- Git allows reverting to any previous commit using git revert or git reset
- In CI/CD, a failed deployment can trigger automatic rollback to the last known good build/artifact
- Keeping deployment artifacts versioned (e.g. via upload-artifact) makes rollback faster since a previous build can be redeployed directly

### Pipeline Security
- Workflow files should avoid hardcoding credentials directly in YAML
- Limit workflow permissions to only what each job needs (principle of least privilege)
- Pin action versions (e.g. actions/checkout@v4) rather than using @latest, to avoid unexpected behavior from upstream changes
- Review third-party actions before using them, since workflows execute with repository-level permissions

### Secrets Management
- Sensitive values (API keys, tokens, deployment credentials) should be stored in GitHub Secrets, not in code or workflow files
- Secrets are referenced in workflows using ${{ secrets.SECRET_NAME }} and are masked in logs automatically
- This project used non-sensitive environment variables for simulation; a real deployment would store credentials like cloud provider keys in GitHub Secrets

---

## 8. Lessons Learned

- Running tests and linting locally before pushing significantly reduces failed CI runs and saves pipeline minutes
- Separating build, test, and deploy into independent workflow files keeps each pipeline focused and easier to debug
- Restricting deployment triggers to the main branch is a simple but effective safety gate
- Capturing monitoring evidence (run counts, durations, success rate) immediately after each pipeline run, rather than after the fact, produces more accurate documentation
