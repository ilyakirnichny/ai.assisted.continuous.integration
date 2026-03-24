# AI-Assisted Continuous Integration — Practice Results

---

## Practice 1 — Generate a Complete Playwright CI/CD Workflow

In this exercise, GitHub Copilot Chat was used to generate a CI/CD workflow for Playwright tests that includes parallel execution, caching, and comprehensive test reporting.

### What was done

- Created `.github/workflows/playwright-tests.yml` triggered on pull requests targeting `main`.
- Configured the job to run on `ubuntu-latest` with a 30-minute timeout and `CI=true` environment variable.
- Set up Node.js LTS with `npm` dependency caching via `actions/setup-node@v4`.
- Added `npm ci` for reproducible dependency installation.
- Installed Playwright browsers with system dependencies using `npx playwright install --with-deps`.
- Ran all Playwright tests in parallel with `npx playwright test`.
- Uploaded two artifacts on every run (including failures):
  - `playwright-html-report` — the HTML test report.
  - `playwright-traces` — Playwright trace files for debugging.
- Added `concurrency` to cancel in-progress runs for the same PR.
- Applied minimal read-only `permissions`.

### Key learnings

- Prompting Copilot with explicit requirements (runner, toolchain, artifact names, conditions) produces a ready-to-use YAML with minimal correction needed.
- Initial generation used `yarn`; correcting the prompt/output to match the actual `package-lock.json` (npm) resolved the runner error immediately.

---

## Practice 2 — Add Notifications

With a working workflow in place, it was enhanced with Microsoft Teams failure notifications.

### What was done

- Added a final step `Notify Teams on failure` to the existing job.
- The step uses `if: failure()` so it only runs when a previous step fails.
- Sends a Teams MessageCard via `curl` to a webhook URL stored in GitHub Secrets as `TEAMS_WEBHOOK_URL`.
- The notification message includes:
  - **PR Title** — `github.event.pull_request.title`
  - **Author** — `github.event.pull_request.user.login`
  - **Branch** — `github.head_ref`
  - **Link to the failed run** — constructed from `github.server_url`, `github.repository`, and `github.run_id`

### Key learnings

- Copilot correctly scoped the notification to `if: failure()` and placed it as the last step so it can catch failures from any earlier step.
- Using a GitHub Secret for the webhook URL keeps sensitive data out of the repository.
- The MessageCard format is a straightforward way to deliver structured notifications to Teams without requiring a third-party action.
