# GitHub Actions Demos

This repository provides a progressive series of **13 runnable GitHub Actions demos** for teaching and learning. Each workflow is self-contained and heavily commented. They do not deploy anything—each one focuses on a specific Actions concept.

## Quick start

1. Create a new GitHub repository.
2. Upload this folder (keep the `.github/` path).
3. Open the **Actions** tab and run any workflow via **Run workflow** (they all support `workflow_dispatch`).

## What’s included

- From a minimal echo job, to environments with approvals, strategy matrices, caching and artifacts, GitHub Script, **reusable workflows**, **composite actions**, **PR-triggered checks**, and **AWS OIDC** role assumption.
- All examples use the default `GITHUB_TOKEN` permissions unless otherwise noted.
- Linux and Windows runners are demonstrated in the matrix example.

## Requirements and setup (per demo)

- **02 – Env and Secrets**: Optional repo secret `DEMO_SECRET` to show masking (`***` in logs).
- **06 – Conditions, Timeouts, Concurrency**: Also triggers on `push` to `main` to show conditional logic and concurrency cancellation.
- **11 – Environment Approvals**: Create an environment named `staging` in **Settings → Environments**, enable **Required reviewers**, and approve runs when prompted.
- **12 – OIDC to AWS**: In AWS IAM, create a role trusted by GitHub’s OIDC provider for this repository. Grant minimal permissions (for the demo, listing S3 is sufficient) and set its ARN in `role-to-assume`.

## Tips

- YAML is whitespace‑sensitive—use spaces, not tabs.
- GitHub expressions require **double curly braces**: `${{ ... }}`.
- To call a reusable workflow in the **same repository**, reference it with `uses: ./.github/workflows/<file>.yml` and ensure the target file exists on the same branch.
- Composite actions live under `.github/actions/<name>/action.yml` and are referenced with `uses: ./.github/actions/<name>`.

---

## Workflow overview table

| # | Workflow file | Name (in workflow) | Triggers | What it demonstrates | Inputs / Outputs | Pre-reqs | How to run & what you’ll see |
|---|---|---|---|---|---|---|---|
| 1 | `.github/workflows/01_hello.yml` | 01 – Hello (Echo Only) | `workflow_dispatch` | Smallest possible workflow: one job, one step, simple `run: echo` | — | — | Actions → select workflow → Run. Logs show “Hello from GitHub Actions!”. |
| 2 | `.github/workflows/02_env_secrets.yml` | 02 – Env and Secrets | `workflow_dispatch` | Job- and step-level `env`, secret masking in logs, creating and using **step outputs** via `$GITHUB_OUTPUT` | Step output `message` | (Optional) repo secret `DEMO_SECRET` to see masking | Run manually. Shows env echoes; secret is masked as `***`; prints step output via `${{ steps.make_output.outputs.message }}`. |
| 3 | `.github/workflows/03_multi_jobs_needs.yml` | 03 – Multiple Jobs with Needs | `workflow_dispatch` | Multiple jobs plus `needs`; **pass data across jobs** via job `outputs` | Job `build` sets output `artifact_name`; consumed by `test` | — | Run manually. Logs show downstream jobs reading `${{ needs.build.outputs.artifact_name }}`; final “summary” step runs after both jobs finish. |
| 4 | `.github/workflows/04_matrix.yml` | 04 – Matrix Strategy | `workflow_dispatch` | `strategy.matrix` across OS and Node; `include`/`exclude`; `fail-fast: false`; `actions/setup-node` | Matrix vars `matrix.os`, `matrix.node` | — | Run manually. Multiple runs (Ubuntu/Windows, Node 18/20/22 per include/exclude); logs show runner OS and `node -v`. |
| 5 | `.github/workflows/05_artifacts_and_cache.yml` | 05 – Artifacts and Cache | `workflow_dispatch` | Create files; **upload/download artifacts**; **actions/cache** with a key | Artifact `demo-artifact`; cache of `.cache-demo/` keyed by OS plus `hashFiles('**/*.yml')` | — | Run manually. Artifact uploaded then downloaded; cached directory persists between runs (cache hit messages in logs). |
| 6 | `.github/workflows/06_conditions_concurrency.yml` | 06 – Conditions, Timeouts, Concurrency | `workflow_dispatch`, `push` to `main` | `concurrency` group with `cancel-in-progress`; `timeout-minutes`; conditional steps using `if:` and event/ref checks | — | — | On manual run or push to `main`, first step echoes matched condition; on non-main pushes that step is skipped. Concurrent runs cancel older ones. |
| 7 | `.github/workflows/07_github_script.yml` | 07 – GitHub Script | `workflow_dispatch` | `actions/github-script@v7`: access `context`, write **job summary** via `core.summary`, and emit notices | — | — | Run manually. See a **Run Summary** with actor/event/ref and a truncated JSON payload; a `core.notice` appears in logs. |
| 8 | `.github/workflows/08_reusable_workflow.yml` | 08 – Reusable Workflow (workflow_call) | `workflow_call` | Defines a **reusable workflow** with typed **inputs** (`greeting`, `run-matrix`) and an **output** (`final-message`) | Inputs: `greeting` (string, required), `run-matrix` (bool, default false). Output: `final-message` | — | Not run directly; called by #9. When invoked, it prints the greeting, optionally “pretend matrix,” and returns “Reusable finished successfully”. |
| 9 | `.github/workflows/09_call_reusable.yml` | 09 – Call Reusable Workflow | `workflow_dispatch` | **Calls** #8 via `uses: ./.github/workflows/08_reusable_workflow.yml` with `with:` inputs | Forwards inputs to #8; can read its outputs if needed | #8 must exist on same branch | Run manually. Confirms invocation of reusable workflow with the provided greeting. |
| 10 | `.github/workflows/10_composite_action.yml` | 10 – Composite Action Demo | `workflow_dispatch` | Uses a **local composite action** from `.github/actions/hello-composite`; demonstrates action **outputs** | Composite action exposes output `salutation` | — | Run manually. It prints a greeting from the composite action. To read its output in a later step, give the `uses` step an `id` (e.g., `id: hello`) and use `${{ steps.hello.outputs.salutation }}`. |
| 11 | `.github/workflows/11_env_approvals.yml` | 11 – Environment Approvals | `workflow_dispatch` (input: `do-dangerous-thing`) | **Protected environments**: job targets `environment: staging` and **pauses for approval** (required reviewers) | Input: `do-dangerous-thing` (`true`/`false`) | Create environment **staging** in repo Settings → Environments; enable **Required reviewers** | Run manually. After approval, job continues. If `do-dangerous-thing: true`, it simulates a deploy; otherwise it logs skipping. |
| 12 | `.github/workflows/12_oidc_aws.yml` | 12 – OIDC to AWS (Assume Role) | `workflow_dispatch` (input: `aws-region`) | **OIDC**: `permissions: id-token: write`; `aws-actions/configure-aws-credentials@v4` to assume role; simple `aws` CLI calls | Input: `aws-region`; no secrets needed for OIDC | In AWS: create IAM role **trusted by GitHub OIDC** for this repo; set `role-to-assume` ARN in the workflow; grant minimal perms (e.g., `s3:ListAllMyBuckets`) | Run manually. Shows STS caller identity and lists S3 buckets (if policy allows). |
| 13 | `.github/workflows/13_pr_checks.yml` | 13 – PR-triggered Checks | `pull_request` to `main` (paths: yml/yaml/js/ts/md) | **Changed-files gating** via `dorny/paths-filter@v3`; conditional jobs: lint only when JS/TS changed, docs check only when MD changed | Job outputs: `js_changed`, `docs_changed` | — | Open a PR changing JS/TS → runs Node + ESLint (non-blocking by default). Changing MD → runs docs check (ensures `README.md` exists). |

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

### Quick start

1. Create a new GitHub repository and upload this folder (keep the `.github/` path).  
2. Create one issue labeled **`release`** titled **“Publish next release changelog”** (used by the release notes workflow).  
3. Optionally add a `bug` label to issues to test the triage workflow.  
4. Go to **Actions** and run any workflow via **Run workflow** (all support `workflow_dispatch`).

### Access & permissions

These workflows rely on **GitHub Models**. They run with `GITHUB_TOKEN` and require the `models: read` permission in each workflow file. You do **not** need to add model API keys for the default GitHub-hosted models; your repo/org must have access to GitHub Models. See the notes in each workflow and the “What to configure” section below.
