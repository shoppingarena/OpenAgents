# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]
## [0.1.0] - 2025-12-08

### Changes
- feat: ExecutionBalanceEvaluator and docs (#27)

* feat(evals): add ExecutionBalanceEvaluator, docs, tests, version bumps

* chore: remove obsolete package-lock in evals/framework pre-rebase

* chore: translate ExecutionBalanceEvaluator and test files from Spanish to English

- Translate all code comments and documentation in execution-balance-evaluator.ts
- Translate test descriptions and prompts in execution-balance-positive.yaml
- Translate test descriptions and prompts in execution-balance-negative.yaml

This ensures consistency with the rest of the English codebase as requested in PR review.

* fix(docs): translate Spanish content to English in documentation

---------

Co-authored-by: Alexander Daza <dev.alexander@example.com>
Co-authored-by: Darren Hinde <107584450+darrenhinde@users.noreply.github.com>


_No unreleased changes yet._

## [0.0.2] - 2025-11-29

### Added
- New `ExecutionBalanceEvaluator` in `evals/framework` to assess read vs execution ordering and ratio.
- Contributor guide: `docs/contributing/ADDING_EVALUATOR.md` (English) describing evaluator design principles.
- Test cases under `evals/agents/openagent/tests/10-execution-balance/` (positive & negative scenarios).

### Changed
- Framework README updated with section documenting `ExecutionBalanceEvaluator` and violation codes.

---

## Version Format

```
v0.0.X
│ │ │
│ │ └─ Patch version (increments with each release)
│ └─── Minor version (feature additions)
└───── Major version (breaking changes)
```
