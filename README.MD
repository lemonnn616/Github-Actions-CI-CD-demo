# GitHub Actions CI/CD Demo (DevOps Portfolio)

A hands-on repository that demonstrates **GitHub Actions** capabilities across the CI/CD lifecycle: triggers, contexts, variables, conditions, caching, artifacts, matrix strategy, reusable workflows, concurrency/timeout controls, and containerized jobs with service dependencies.

This project is designed as a **portfolio-grade demo** for DevOps / CI/CD roles: it is intentionally focused on workflow mechanics and patterns rather than application complexity.

---

## What’s inside

### Key CI/CD topics covered
- **Workflow triggers**: `push`, `pull_request`, `issues`, `schedule`, `workflow_dispatch`, `workflow_run`, `repository_dispatch`
- **Branch / tag / path filters** (including negation patterns)
- **Contexts & expressions**: `github`, `runner`, `job`, `steps`, `toJson`, conditional functions
- **Environment variables** at workflow / job / step levels + `$GITHUB_ENV`
- **Conditional execution**: `always()`, `success()`, `failure()`, `cancelled()`, step conclusions
- **Caching**: `actions/cache` (and a custom composite action)
- **Artifacts**: upload/download to pass data between jobs
- **Matrix builds**: `include`, `exclude`, `fail-fast`, `max-parallel`
- **Reusable workflows**: `workflow_call` and `uses: ./.github/workflows/...`
- **Concurrency control**: cancel in-progress runs
- **Timeouts**: job-level timeout guards
- **Docker / containers**: running jobs in a container, using service containers (MySQL)

---

## Repository structure

```text
.
├── .github
│   ├── actions
│   │   └── cache-node-modules
│   │       └── action.yaml          # custom composite action for npm cache + install
│   └── workflows
│       ├── first_workflow.yaml
│       ├── second_workflow.yaml
│       ├── third_workflow.yaml
│       ├── fourth_workflow.yaml
│       ├── 5_workflow.yaml
│       ├── 6_workflow.yaml
│       ├── 7_workflow.yaml
│       ├── 8_workflow.yaml
│       ├── 9_workflow.yaml
│       ├── 10_workflow.yaml
│       ├── 11_workflow.yaml
│       ├── 12_workflow.yaml
│       ├── 13_workflow.yaml
│       ├── 14_workflow.yaml
│       ├── 15_workflow.yaml
│       ├── 16_workflow.yaml
│       ├── 17_workflow.yaml
│       ├── workflow.yaml            # reusable workflow (called by 16_workflow.yaml)
│       ├── concurrency.yaml
│       ├── timeout.yaml
│       └── docker.yaml
├── index.html                        # tiny demo app (Hello World)
├── package.json
└── package-lock.json
```

---

## Quick start

### Prerequisites
- A GitHub repository (this repo) with Actions enabled
- Optional: Node.js + npm (only if you want to run locally)

### Local run (optional)
This repo is CI/CD-focused, but you can still open the demo page:

```bash
npm install
# then open index.html in a browser
```

### Running workflows
Most workflows are triggered manually:

1. Go to **GitHub → Actions**
2. Pick a workflow (by name)
3. Click **Run workflow**

Some workflows require a specific event (push, PR, issue update, repository dispatch, etc.). See the catalogue below.

---

## Workflow catalogue

> All workflow files live in `.github/workflows/`.

| Workflow name (in UI) | File | Demonstrates | Trigger |
|---|---|---|---|
| First workflow | `first_workflow.yaml` | Basic steps, multiline commands | `workflow_dispatch` |
| Second workflow | `second_workflow.yaml` | Parallel jobs + `needs` dependency | `workflow_dispatch` |
| Third workflow | `third_workflow.yaml` | Checkout-like logic via git commands | `workflow_dispatch` |
| Fourth workflow | `fourth_workflow.yaml` | Reacting to another workflow result | `workflow_run` |
| 5 workflow | `5_workflow.yaml` | `issues` + `pull_request` event types, env vars | issues/PR events |
| 6 workflow | `6_workflow.yaml` | Branch/tag/path filters (incl. negation patterns) | `push` |
| 7 workflow | `7_workflow.yaml` | Cron schedule + `workflow_run` chaining | `schedule`, `workflow_run` |
| 8 workflow | `8_workflow.yaml` | Manual inputs (string/choice/boolean) | `workflow_dispatch` |
| 9 workflow | `9_workflow.yaml` | External event payloads | `repository_dispatch` |
| 10 workflow | `10_workflow.yaml` | Contexts (`github`, `runner`, `job`, `steps`) | `workflow_dispatch` |
| 11 workflow | `11_workflow.yaml` | Env scoping + `$GITHUB_ENV` | `workflow_dispatch` |
| 12 workflow | `12_workflow.yaml` | Conditions: `always/success/failure/cancelled` + `needs` | `workflow_dispatch` |
| 13 workflow | `13_workflow.yaml` | Caching npm deps using `actions/cache` | `workflow_dispatch` |
| 14 workflow | `14_workflow.yaml` | Artifacts upload/download between jobs | `workflow_dispatch` |
| 15 workflow | `15_workflow.yaml` | Matrix builds, include/exclude, fail-fast, max-parallel | `workflow_dispatch` |
| 16 workflow | `16_workflow.yaml` | Calling a reusable workflow | `workflow_dispatch` |
| 17 workflow | `17_workflow.yaml` | Using a **custom composite action** | `workflow_dispatch` |
| Reusable Workflow | `workflow.yaml` | `workflow_call` reusable workflow with inputs | `workflow_call` |
| Test concurrency | `concurrency.yaml` | Concurrency group + cancel-in-progress | `workflow_dispatch` |
| Test timeout | `timeout.yaml` | Job timeout guard | `workflow_dispatch` |
| Docker Test | `docker.yaml` | Job container + service container (MySQL) + docker action | `workflow_dispatch` |

---

## Highlights (what employers usually care about)

### 1) Reusable workflows (DRY pipelines)
`workflow.yaml` is a reusable workflow invoked by `16_workflow.yaml`:

- Shows `on: workflow_call` + typed inputs
- Helps standardize CI logic across repos/environments

### 2) Custom composite action (internal “CI building block”)
This repo includes a custom composite action:

**Path:** `.github/actions/cache-node-modules/action.yaml`

It demonstrates:
- Packaging multi-step logic as a reusable unit
- Optional caching via an input flag
- Printing cache outputs for debugging

Example usage (see `17_workflow.yaml`):

```yaml
- name: Use custom action
  uses: ./.github/actions/cache-node-modules
  with:
    caching: 'false'
```

### 3) Matrix strategy for multi-platform testing
`15_workflow.yaml` demonstrates:
- Multiple OS + Node versions
- `include` / `exclude`
- `fail-fast: false`
- `max-parallel` to control load/cost

This is a common production pattern for test pipelines.

### 4) Artifacts for passing data between jobs
`14_workflow.yaml` shows:
- Generating a file in one job
- Uploading it as an artifact
- Downloading and using it in subsequent jobs

This pattern is frequently used for build outputs, SBOMs, reports, and deployment bundles.

### 5) Concurrency & timeouts (protect runners and reduce waste)
- `concurrency.yaml` cancels in-progress runs for the same concurrency group
- `timeout.yaml` ensures stuck jobs don’t burn minutes indefinitely

---

## How to trigger event-based workflows

### Push / filters (`6_workflow.yaml`)
Triggered on `push` with branch/tag/path filters.  
To test quickly:

```bash
# change index.html (paths filter requires **.html)
git add index.html
git commit -m "Test: HTML change"
git push origin main
```

### Issues / PR events (`5_workflow.yaml`)
Triggered by:
- Issues: edited, deleted
- PR: assigned, closed

Use GitHub UI to edit an issue or close a PR to see it fire.

### Scheduled (`7_workflow.yaml`)
Uses cron:
```yaml
schedule:
  - cron: "*/5 * * * *"
```
> Note: scheduled workflows only run on the default branch and may require the repository to have some activity.

### repository_dispatch (`9_workflow.yaml`)
This trigger is great for integrations (ChatOps, external CI, custom systems). Example:

```bash
curl -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR_GITHUB_TOKEN>" \
  https://api.github.com/repos/<OWNER>/<REPO>/dispatches \
  -d '{"event_type":"test","client_payload":{"titleTest":"Hello from repository_dispatch"}}'
```

Security note:
- Use a **fine-grained PAT** or GitHub App token with the minimum required repo permissions.
- Avoid exposing tokens in logs.

---

## Docker workflow notes (`docker.yaml`)
This workflow demonstrates:
- Running a job inside a container image (`node:21-alpine3.17`)
- Adding a MySQL service container
- Using a Docker-based action step: `uses: docker://node:18-buster-slim`

This is a practical example of how CI can:
- Mirror production runtime
- Depend on services (DB, cache, message broker) during tests

---

## Intentional simplicity
The “application” in this repo is a minimal static `index.html`. The goal is to keep the focus on:
- Pipeline architecture
- Workflow features
- CI/CD building blocks and best practices

---

## Tech stack
- GitHub Actions (Workflows, composite actions, reusable workflows)
- Node.js / npm (for caching and toolchain examples)
- Docker (containerized job execution and service containers)

---

## License
This repository is intended for educational / portfolio demonstration purposes. Add a license if you plan to distribute it broadly.
