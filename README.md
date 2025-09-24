# GitHub Actions Demos

## Fundamentals

1. **01_hello.yml** – The smallest possible workflow: single job, single step, echo.
2. **02_env_secrets.yml** – Environment variables, masking secrets, and step outputs.
3. **03_multi_jobs_needs.yml** – Multiple jobs, dependencies with `needs`, pass data between jobs.
4. **04_matrix.yml** – Strategy matrix (OS / Node versions), `fail-fast`, `include`, `exclude`.
5. **05_artifacts_and_cache.yml** – Upload/download artifacts; caching a directory.
6. **06_conditions_concurrency.yml** – `if:` conditions, timeouts, `concurrency` and `cancel-in-progress`.
7. **07_github_script.yml** – Using `actions/github-script` to read context and write a step summary.
8. **08_reusable_workflow.yml** – Define a **reusable workflow** with `workflow_call` and typed inputs/outputs.
9. **09_call_reusable.yml** – Call the reusable workflow defined in #8 with `uses: ./.github/workflows/...`.
10. **10_composite_action.yml** – Build and use a **composite action** from `.github/actions/hello-composite`.
11. **11_env_approvals.yml** – Uses an **environment** with required reviewers. Demonstrates manual approvals before running a protected job.
12. **12_oidc_aws.yml** – **OIDC to AWS** with `id-token: write`, assuming an AWS role via `aws-actions/configure-aws-credentials` and listing S3 (placeholder role ARN required).
13. **13_pr_checks.yml** – **PR-triggered checks**: simple lint, changed-files gating, and status reporting on pull requests.

> Notes
> - None of these deploy anything; they just demonstrate core features of Actions.
> - `GITHUB_TOKEN` default permissions are enough. If your org enforces restricted permissions,
>   you may need to add `permissions:` blocks as appropriate.
> - Secrets demo simply shows how they are masked in logs; set a repo secret named `DEMO_SECRET`
>   to see masking in action.

## AI

This repo contains three **runnable** GitHub Actions workflows that mirror the GitHub Blog article *Automate your project with GitHub Models in Actions*. Each demo uses **GitHub Models** directly from Actions.

### Workflows

1. `.github/workflows/bug-reproduction-instructions.yml`  
   - On **new issues** labeled `bug`, the workflow asks a model to check if reproduction steps are sufficient.  
   - If insufficient, it **comments** on the issue with a friendly request for details.

2. `.github/workflows/release-notes.yml`  
   - On **merged pull requests**, uses `gh models run` (via the `gh-models` extension) to generate a **one-line changelog** entry and appends it to a pre-created **release** issue.

3. `.github/workflows/weekly-issue-summary.yml`  
   - On a **weekly schedule** (and manual trigger), collects the last week’s open issues, runs a **prompt file** (`prompts/issue-summary.prompt.yml`) through `gh models run`, and opens an issue with a **summary** and **prioritization**.

## Quick start

1. Create a new GitHub repository and upload this folder (keep the `.github/` path).  
2. Create one issue labeled **`release`** titled **“Publish next release changelog”** (used by the release notes workflow).  
3. Optionally add a `bug` label to issues to test the triage workflow.  
4. Go to **Actions** and run any workflow via **Run workflow** (all support `workflow_dispatch`).

## Access & permissions

These workflows rely on **GitHub Models**. They run with `GITHUB_TOKEN` and require the `models: read` permission in each workflow file. You do **not** need to add model API keys for the default GitHub-hosted models; your repo/org must have access to GitHub Models. See the notes in each workflow and the “What to configure” section below.
