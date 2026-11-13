# Architecture and gates

The app's pull request runs an organization-required npm build/test workflow and
an organization-policy check. A separate CodeQL caller runs real GitHub code
scanning when enabled and entitled. The delivery caller builds the app once,
then executes six explicitly synthetic adapters, followed by `dev`, `test`, and
`prod` GitHub Environment jobs. Every environment job verifies the same
downloaded artifact checksum. Production waits for an environment reviewer.
The ruleset requires the production job as a check before merging.

The simulated adapters are Veracode SAST, Veracode SCA, SonarQube, JFrog,
test-case management, and change management. Their passes are hard-coded demo
results, **not scans, actual quality measurements, registry publishing, QA, or
change approval**. No external API or credentials are used. GitHub artifacts and
their accompanying checksums illustrate transfer integrity, not trusted build
provenance or a production artifact registry.

## Fail-closed boundaries

Native build/test failures, a missing or invalid `DEMO_MODE`, missing artifacts,
checksum failures, failed prerequisite jobs, and a pending production reviewer
block the simulated progression. The organization ruleset blocks merge until
its required workflows and production status check pass. A missing required
check is not a pass. These protections do not make synthetic evidence
authoritative, and a GitHub Environment's reviewer list does not enforce two
independent approvals.

Real implementation requires replacing each adapter with an independently
reviewed integration that validates authenticated, fresh, exact-revision and
exact-artifact evidence, nonpassing/unknown states, and service errors. Add
real quality thresholds (including new-code coverage), protected credentials,
artifact provenance, policy enforcement, change windows, independent role
approvals, rollback, and deployment authorization before any real deployment.
Never route this simulation into real infrastructure.
