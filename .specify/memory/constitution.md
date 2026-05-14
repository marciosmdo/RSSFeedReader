# RSSFeedReader Constitution

## Core Principles (actionable)

### I. Security — treat all external feeds as untrusted input
- Threat modelling: document data flows and threat surface for any new integration in specs/research.md.
- Input validation: validate feed URLs (scheme, host allowlist/denylist, length) before fetching.
- Parsing safety: use a secure XML/HTML parser with entity expansion disabled; reject feeds that trigger XML bombs.
- Network hygiene: enforce timeouts (e.g. connect 5s, read 10s), connection limits, and per-host rate limiting in ingestion code.
- Secrets: never commit credentials; use environment variables or secret manager. Fail CI on detected secrets.
- Dependency security: enable automated dependency updates (Dependabot or equivalent) and require a security scan (SCA) on merge.
- Runtime hardening: run container/image scans (Trivy), and static security checks (Bandit for Python) in CI.

### II. Maintainability — small, testable, well-documented modules
- Modular design: keep feed ingestion, parsing, storage, and presentation as separate modules with clear interfaces.
- Tests-first: every new feature must include unit tests for logic and an integration test for end-to-end feed ingestion (tests fail before implementation).
- Test coverage: critical modules (ingestion/parsing/storage) must have coverage targets; track regressions in CI.
- Readable code: require linters and formatters (e.g., ruff/black, mypy for type checking) as blocking CI checks.
- Documentation: update specs/research.md, plan.md, and quickstart.md for every feature that changes architecture or deployment.

### III. Code Quality & Review
- PR standard: PRs must be small, linked to a spec in specs/[###-feature]/spec.md, include tests, and pass CI (lint, types, tests, security scan).
- Code reviews: at least one approving review from a maintainer; security-sensitive changes require a second reviewer.
- Static analysis: enforce type checking (mypy), complexity checks, and known-bad-pattern detection in CI.

### IV. Observability & Reliability
- Logging: structured logs for ingestion and parsing errors; do not log raw feed content wholesale (redact PII).
- Metrics: emit metrics for feed success/failure rates, parse times, queue/backlog length, and cache hit rates.
- Alerts: define SLOs for ingestion (e.g., p95 parse latency) and set alert thresholds for sustained failures or backlogs.

### V. Data Handling & Privacy
- Minimal retention: persist only what is required; document retention policy in plan.md.
- Sanitization: sanitize HTML before presenting to users; strip scripts and unsafe attributes.
- Exports & backups: document backup strategy and ensure exports do not include secrets.

### VI. Release, Versioning & Rollout
- Semantic versioning for releases; include migration notes for any breaking changes in specs/plan.md.
- Rollout strategy: staged rollout for changes affecting ingestion (canary → gradual → full) with monitoring.

### VII. Governance & Amendments
- Amendments: constitution changes require a PR referencing the relevant spec and approval by two maintainers.
- Exceptions: any exception to a principle must be documented in the feature plan with justification and mitigation.
- Enforcement: CI gate enforces linting, types, tests, and security scans; failures block merges.

## Quick checklist (must be satisfied for feature acceptance)
- [ ] Spec linked in specs/[###-feature]/spec.md ([spec template](.specify/templates/spec-template.md))
- [ ] Threat model recorded in research.md
- [ ] Unit + integration tests added and failing before implementation
- [ ] Lint, types, and security scans pass in CI
- [ ] Document retention and privacy decisions in plan.md

**Rationale:** These rules map to the project's goals (see [StakeholderDocuments/ProjectGoals.md](StakeholderDocuments/ProjectGoals.md)), feature constraints (see [StakeholderDocuments/AppFeatures.md](StakeholderDocuments/AppFeatures.md)), and chosen stack (see [StakeholderDocuments/TechStack.md](StakeholderDocuments/TechStack.md)). They prioritize secure ingestion of untrusted RSS/ATOM feeds, maintainable modular code, and CI-enforced quality gates.
