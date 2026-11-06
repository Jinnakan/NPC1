# Product Definition of Done (Sprint 1)

Version 1.0 · 2026-11-06

A Sprint 1 feature is done only when all of these hold, with evidence recorded in Part F:

1. Required behavior in its contract (F-01 to F-05) is implemented across UI, API and database.
2. Every planned test in `sts.md` for that feature has been executed; results (command, tested commit, pass/fail/skip counts) are retained.
3. Meaningful tests pass. No required test is disabled or weakened to pass; skipped or failing tests are reported.
4. Rule work followed TDD: the failing-test commit precedes the passing commit.
5. The feature branch was reviewed by the teammate (not the author) with a specific recorded check, question or finding, and merged into sprint staging.
6. Staging passed integration and acceptance checks, and the release PR into `main` is merged and tagged.
7. README, `.env.example`, seed and these documents match the released behavior; any contract change is recorded in the version history below.
8. Incomplete work, known limitations and failed or skipped tests are disclosed.
9. No real secrets or passwords exist in Git or the PDF.

## Contract version history

| Date | Version | Change | Reason |
|---|---|---|---|
| 2026-11-06 | 1.0 | Initial contracts for F-01 to F-05 and STS | Pre-implementation baseline |
