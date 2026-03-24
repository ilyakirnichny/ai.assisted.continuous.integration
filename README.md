# Playwright Test Automation Framework

TypeScript + Playwright e2e framework using Page Object Model (POM).  
Target: **https://the-internet.herokuapp.com**

## Stack

- [Playwright Test](https://playwright.dev) — test runner
- TypeScript — strict mode
- Node.js 18+

## Structure

```
src/
  components/
    BaseComponent.ts     # Abstract base for UI components
  fixtures/
    BaseTest.ts          # Custom test with logger, loginPage, homePage fixtures
  pages/
    BasePage.ts          # Abstract base with common page methods
    HomePage.ts          # Home page (/)
    LoginPage.ts         # Login page (/login)
  utils/
    envHelper.ts         # Environment variable helpers
    logger.ts            # Structured logger (info / warn / error / step / debug)
tests/e2e/
  home.spec.ts           # Home page tests (6)
  login.spec.ts          # Login / logout tests (5)
  checkboxes.spec.ts     # Checkboxes tests (5)
.github/workflows/
  playwright-tests.yml   # CI/CD pipeline
playwright.config.ts
```

## Setup

```bash
npm install
npx playwright install
cp .env.example .env
```

## Run

```bash
# All tests (3 workers)
npm test

# Headed
npx playwright test --headed

# UI mode
npx playwright test --ui

# HTML report
npx playwright show-report
```

## CI/CD

The workflow at `.github/workflows/playwright-tests.yml` triggers on pull requests targeting `main`.

**`playwright-tests` job**
- Installs dependencies with `npm ci` and Playwright browsers with `--with-deps`
- Runs all tests with 3 parallel workers (`CI=true`)
- Uploads `playwright-html-report` and `playwright-traces` artifacts on every run
- Sends a Microsoft Teams notification on failure (via `TEAMS_WEBHOOK_URL` secret)
- Publishes the HTML report to **GitHub Pages** on merges to `main`, including a cumulative `history.json`

**`security-scan` job** (runs in parallel)
- Runs `npm audit --audit-level=high` to detect high/critical vulnerabilities
- Writes a summary to the GitHub Actions step summary
- Blocks the PR if vulnerabilities are found (`continue-on-error: false`)

## Conventions

- Selectors: `getByRole`, `getByLabel`, `getByTestId` only — no CSS/XPath
- PascalCase classes, camelCase methods
- Every class and public method has JSDoc
- Pages extend `BasePage`, components extend `BaseComponent`
- Fixtures in `BaseTest.ts` — import `{ test, expect }` from there in every spec

