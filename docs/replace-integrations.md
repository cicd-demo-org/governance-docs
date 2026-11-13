# Replace simulated integrations

The demo's `simulated-integrations` matrix deliberately prints synthetic
passes. It is not an adapter framework and its results **must never authorize a
real deployment**. The steps below describe work your organization must
implement and verify; no vendor connection, credentials, or real qualification
are supplied here. Keep the existing simulation unchanged and no-account.

## Common replacement procedure

1. Agree on the policy owner, the exact application/component, source SHA,
   immutable artifact digest, acceptable evidence age, and authoritative
   service/project identity. Define the required test or policy thresholds
   before writing an adapter. Version and protect this policy.
2. Obtain the service's **official** supported API/CLI documentation for your
   installed version and product edition. Define the precise submission,
   polling, final-state, pagination, and report APIs. Do not copy guessed API
   paths or assume different services share a `PASSED` vocabulary.
3. Give the service identity only the required project permissions. Prefer
   short-lived OIDC or a narrowly scoped GitHub App where supported. Otherwise
   store each credential separately as a GitHub **environment secret** in a
   protected integration environment. Restrict its reviewers and branch access,
   expiry, rotation, and network egress. Never commit secrets, pass them through
   command-line arguments, put them in artifacts, or print responses containing
   secrets. No vendor secret is needed by the simulation.
4. Implement one independently testable adapter per gate, on a trusted
   default-branch workflow. Pass configuration via validated environment
   variables, not interpolated shell. Send secrets only to the adapter step,
   never to a step that executes untrusted pull-request code. Use verified TLS,
   explicit timeouts, bounded retries for transient errors, and a total deadline.
5. Normalize the authenticated final result into a versioned evidence record
   containing at least gate, service/project, source SHA, artifact digest,
   authoritative verdict, completion time, policy/version, and report reference.
   Verify all identity fields and freshness against the candidate. Keep
   evidence in an access-controlled retention system appropriate to your
   organization; an Actions artifact alone is not an authoritative ledger.
6. Make separate required jobs for the actual gates in a **new real-mode
   workflow**. Replace the synthetic matrix only in that real workflow; make
   the real deployment job depend on every real gate. Missing configuration,
   credentials or evidence, unknown/pending/error states, mismatched digest or
   SHA, timeouts, HTTP/authentication errors, malformed responses, and policy
   failures must exit nonzero and block downstream deployment. Never use
   `continue-on-error` or a default success when service calls fail.
7. Test a valid pass and each failure above with deterministic fixtures, then
   exercise the service's test project using a dedicated test identity. Verify
   the displayed result against the service itself, inspect redaction, and
   have the security and service owners approve the adapter. Add real evidence
   to an independently protected deployment policy before using production.

The separate real container example has **no integration hooks wired into
authorization**. Treat it as a sandbox demonstration until your organization
adds and independently enforces the real gates described here.

## Veracode SAST

1. Identify the correct application profile, scan type, policy, and packaging
   format. Determine how the service binds an uploaded binary to the candidate
   source and artifact digest.
2. Create a least-privilege Veracode identity. If the supported integration
   requires API ID and key, store them as separate environment secrets and
   use the vendor's supported signing/credential mechanism; do not log them.
3. Submit the exact artifact, retain the returned scan identifier, and poll to
   an authoritative completed state within a bounded deadline. Query the
   application policy result for that scan and verify its identity.
4. Fail on incomplete scans, nonpassing policy, wrong application or artifact,
   ambiguous findings, authentication failure, and expired evidence. Exercise
   an intentionally failing policy before enabling the gate.

See [Veracode API authentication](https://docs.veracode.com/r/API_authentication)
and your version's official scan and policy documentation.

## Veracode SCA

1. Select the supported dependency/SCA scan and organization policy separately
   from SAST; a SAST upload is not an SCA result.
2. Provision the SCA-specific least-privilege identity or agent token using the
   vendor's current guidance and store it as a protected secret if required.
3. Scan the exact lockfile, component and revision; retain the scan identity
   and confirm the final vulnerability **and license** policy result.
4. Fail on absent or incomplete inventories, nonpassing/unknown policy,
   identity mismatch, expired results, and errors. Test a prohibited dependency
   or license against a test project.

See [Veracode SCA documentation](https://docs.veracode.com/r/Software_Composition_Analysis).

## SonarQube

1. Configure the correct project, branch/PR mapping, analysis version and
   **new-code baseline**. Define the quality gate, including at least 80% new
   code coverage if that is your organization's requirement.
2. Issue a project-scoped analysis token and, if necessary, a separate
   read-only gate-query identity; store secrets in a protected environment.
3. Produce and retain the correct coverage report, submit analysis for the
   candidate source SHA, wait for the background task, and query the
   authoritative quality gate for that analysis.
4. Fail if the task is pending/failed, analysis or project is wrong, coverage
   is missing, the new-code baseline differs, the gate is not passing, or
   service responses are malformed or stale. Test coverage just below and at
   the agreed threshold.

See [SonarQube quality gates](https://docs.sonarsource.com/sonarqube-server/quality-standards-administration/managing-quality-gates/introduction-to-quality-gates/).

## JFrog artifact and dependency controls

1. Choose the approved repository and immutable artifact coordinates, retention,
   promotion policy, and whether dependency/license policy is enforced by
   JFrog Xray. Dependency policy and artifact publication are separate results.
2. Configure a narrowly scoped, short-lived OIDC exchange if supported by your
   JFrog instance; otherwise use a protected, expiring access token. Limit
   repository read/write and Xray permissions independently.
3. Upload the exact approved candidate, record immutable coordinates and digest,
   and verify the returned digest. Obtain the completed dependency and license
   policy result for the correct build and inventory.
4. Reject a missing inventory, unknown or failed policy, mutable/incorrect
   coordinates, digest mismatch, upload error, timeout, or expired token.
   Confirm that retries do not overwrite a different artifact.

See [JFrog GitHub Actions OIDC integration](https://jfrog.com/help/r/jfrog-platform-administration-documentation/openid-connect-integration).

## Test-case management and QA

1. Choose the authoritative test-management system, suite/version, test
   environment, required test cases and independent QA owner. Define whether
   automated and manual evidence are required.
2. Provision an integration identity limited to the project and required
   read/write operations; store any API credential in a protected secret. Do
   not give the app build or pull-request code access to it.
3. Register or locate a test execution for the exact artifact digest, source
   SHA, environment and suite. Record execution ID and authenticated report
   location; wait for the final qualification and required approvals.
4. Fail if the suite is incomplete, a required case is missing/failed/skipped,
   a result is stale, the artifact differs, the service is unreachable, or QA
   qualification is revoked. Test all of these with a nonproduction project.

## Change management

1. Select your organization's change system and define the authorized
   application, exact artifact digest, deployment target, approvers, current
   change window and revocation behavior. A user-supplied timestamp or ticket
   number is not approval.
2. Use a least-privilege service identity and supported short-lived
   authentication where possible. Store any fallback credential in a protected
   environment secret and limit its network access.
3. Create or retrieve the change record through the documented API. Immediately
   before deployment, re-query its authenticated status, approved identities,
   target, digest and current window; retain the immutable record reference.
4. Block on a missing, pending, denied, expired, revoked or ambiguous change;
   wrong target/digest; authentication errors; and timeout. Recheck after a
   long approval wait. Use a test change to demonstrate both approval and
   revocation; never infer approval from a successful ticket-creation request.

## Cutover and recovery

Require security, service, QA, change and application owners to review the
real workflow and its negative tests. Enable each integration only in a test
environment first, measure service failures and rollback procedures, and keep
the simulation separate. A real release must require **all** real gates, not
both synthetic and real results with an OR. If an adapter is unavailable,
stop promotion; do not switch to simulated evidence as a fallback.
