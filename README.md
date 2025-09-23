# Demo list

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
