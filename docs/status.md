# Implementation status

| Capability | Status |
| --- | --- |
| Node native lint, tests, build | Implemented |
| Organization npm and secret-file checks | Implemented; require ruleset setup |
| GitHub CodeQL | Real analysis; requires configuration and entitlement |
| Vendor security, quality, registry, QA, change management | Synthetic pass only; no integration |
| Dev/test/prod deployment | Simulated; no infrastructure changed |
| Production approval | One GitHub Environment reviewer, after setup |
| Artifact transfer | GitHub Actions artifact and SHA-256 check; not provenance |
| Safe-settings | Optional example only |
| Role-separated multiple approvals, rollback, production evidence, real deployment | Not implemented |

This reference is a runnable teaching example, not production readiness
certification. Review your organization's actual requirements and verify every
control independently.
