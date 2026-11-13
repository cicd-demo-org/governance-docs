# Implementation status

| Capability | Status |
| --- | --- |
| Node native lint, tests, build | Implemented |
| Organization npm and secret-file checks | Implemented; require ruleset setup |
| GitHub CodeQL | Real analysis; requires configuration and entitlement |
| Vendor security, quality, registry, QA, change management | Synthetic pass only; no integration |
| Dev/test/prod deployment | Simulated; no infrastructure changed |
| Simulated production approval | The `--approver` user; self-approval permitted by the demo baseline |
| Artifact transfer | GitHub Actions artifact and SHA-256 check; not provenance |
| Safe-settings | Optional example only |
| GHCR build, provenance and self-hosted Docker deployment | Opt-in example; requires review and manual setup; not yet live-verified |
| Rollback | Example restores a prior local container after failed smoke; manual rollback reuses an attested digest; not yet live-verified |
| Real vendor, QA and change integrations | Replacement guide only; not implemented |
| Role-separated multiple approvals and production-grade authorization | Not implemented |

This reference is a runnable teaching example, not production readiness
certification. Review your organization's actual requirements and verify every
control independently.
