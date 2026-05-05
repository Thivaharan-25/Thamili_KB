# CI/CD Checklist

> **Extracted from:** `.github/workflows/deploy.yml`  
> **See also:** [[build-ready/deployment-security|deployment-security]]

The existing project deploys to Vercel on pushes to `main`. The web app CI should add quality gates before deployment.

## Required CI Steps

1. Install dependencies with lockfile.
2. Type check: `tsc --noEmit`.
3. Lint: `eslint`.
4. Format check: `prettier --check`.
5. Unit/component tests: `vitest`.
6. Accessibility tests: `axe-core`/`jest-axe`.
7. Build: `vite build`.
8. Optional E2E smoke: Playwright against built preview.
9. Vercel deploy only after checks pass.

## Branch Policy

- No direct commits to main.
- Mandatory PR reviews.
- CI required before merge.

