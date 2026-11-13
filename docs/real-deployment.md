# Opt-in GHCR and Docker deployment example

This example publishes a real image and can run a real container. It is
**separate from the default simulation**. The example is not a production
authorization system: it does not implement the real vendor, QA, change-window
or multi-role approvals in [the replacement guide](replace-integrations.md).
Do not use it to release a real application until your organization independently
implements and enforces those controls.

The example code must first be reviewed and integrated into the default branches
of `required-workflows` and `nodejs-demo-app`. The application caller pins a
reviewed commit of the reusable workflow. On GitHub, a manually dispatched
workflow file must exist on the default branch. The current demo does **not**
provision a real runner, `real-prod` environment, or opt-in variable, and no
live container deployment has been verified.

## Trust and access prerequisites

1. Protect the application and central workflow default branches with your
   organization's reviewed change controls. Only trusted maintainers may
   change workflow files or the Dockerfile. Ensure your GitHub plan supports
   artifact attestations, protected environments and restricted runner groups.
2. Review [GitHub's warning](https://docs.github.com/en/actions/how-tos/manage-runners/self-hosted-runners/manage-access)
   against using self-hosted runners with public repositories. A compromised
   runner and Docker socket can control its host. The public demo does not make
   this risk safe. Prefer a private deployment repository for real systems.
3. If your organization explicitly accepts that risk, create a dedicated
   `demo-real-deploy` runner group. Allow only the application repository and
   restrict **workflow access** to the exact reviewed reusable deployment
   workflow and revision used by its caller. Do not use the Default group,
   grant all-workflow access, or attach a runner if the required restrictions
   cannot be verified. Enabling public repository access is an explicit risk
   acceptance, not a recommended default. Update the group policy whenever the
   reviewed workflow pin changes.
4. Register a dedicated Linux x64 runner in that group with label
   `demo-real-deploy`; install Docker, curl, Bash, Python 3 and a current GitHub
   CLI supporting `gh attestation verify` and its source/signer flags. The
   runner must support `job.workflow_sha` and `job.workflow_repository`
   (runner version 2.336.0 or later). Isolate the host, filesystem, network and
   Docker daemon from other workloads, and restrict administrator access.
   The deployed container persists on this host; an ephemeral runner
   registration alone does not erase the host or isolate a shared Docker socket.
5. Manually create an application environment named `real-prod`. Configure a
   reviewer other than the workflow initiator, enable **prevent self-review**,
   disable administrator bypass, and allow only protected branches. Verify the
   default branch really has an enforced protection rule. The workflow checks
   these environment settings before scheduling and again on the runner.
   One GitHub Environment approval is not two distinct role approvals.
6. Confirm GHCR package creation is allowed and the resulting package is linked
   to the application repository. The publish job uses the job-scoped
   `GITHUB_TOKEN` with `packages: write`, `attestations: write` and
   `id-token: write`; the deployment job gets read-only package and attestation
   access. Do not create a personal access token or store a secret for this
   example. Grant the application access to an existing private package if
   needed; do not make a sensitive image public just to make a run pass.
7. Only after the preceding controls are in place, set repository Actions
   variable `REAL_DEPLOY_ENABLED` to `true`. Its absence or any other value
   fails closed. Leave `DEMO_MODE=simulation` unchanged. Restrict who can edit
   variables, environments, branch rules and runner group policy.

Environment preflight is a point-in-time check, not an atomic guarantee against
an administrator changing settings afterward. Audit configuration changes,
restrict bypass authority, and recheck controls at each approval.

## Publish and verify the candidate

1. On the application default branch, run **Publish attested demo image**
   manually. The workflow rejects non-default refs or missing opt-in, checks out
   that revision without persisted credentials, runs npm lint/tests/build, then
   builds and publishes the image to GHCR. A pinned GitHub Action produces a
   signed build-provenance attestation for the image digest.
2. Record the full `sha256:...` image digest and full 40-character source SHA
   from the run summary. Do not use the mutable `sha-...` tag as the identity.
   If build, publication, attestation, permissions, or credentials fail, stop.
   An uploaded image without a valid attestation is not a deployable candidate.
3. Independently inspect the source, container, scan/QA/change evidence and
   target. Publishing and the simulation's synthetic results are **not**
   approval. The included sample does not enforce these external controls.

## Deploy and confirm

1. Run **Opt-in real container deployment** on the default branch, providing
   the recorded image digest and source SHA. The hosted preflight rejects
   invalid values, disabled opt-in, non-default refs and insufficient
   `real-prod` protection. A missing runner group may leave the job queued;
   cancel it rather than broadening access.
2. A different authorized `real-prod` reviewer must inspect the run, candidate
   and external evidence before approving. The job checks environment
   protection again after approval. Keep the default simulation's `prod`
   environment separate.
3. On the dedicated host the job authenticates to GHCR with its short-lived
   read token, verifies the image attestation with `gh attestation verify`
   against the application repository, the exact publisher workflow, the
   default-branch ref, the supplied source SHA and GitHub-hosted builder, then
   pulls by digest. A missing token, unverifiable attestation, mismatch, or
   registry error stops the operation before replacing the container.
4. The example runs the container as `nodejs-demo-app`, bound to
   `127.0.0.1:3000`, and checks its HTTP response. Confirm the deployment
   record, digest and health on the isolated host. Loopback-only access is
   intentional: this is not a public load balancer, TLS, scaling, or HA design.
   The deployment script refuses to replace an unrelated or stopped container
   with the same name.

## Rollback and recovery

Before deploying, record the currently running image digest and source SHA in
your change record and ensure its registry artifact and attestation remain
available. If the new container fails its smoke test, the script removes the
candidate and attempts to restart the previous managed container; the workflow
still fails. First deployment has no previous container to restore. Failures
while restarting or restoring are reported and require immediate operator
intervention. A runner crash or forced termination can interrupt cleanup; this
single-host example cannot promise automatic recovery from host failure.

For a later manual rollback, run the **same deployment workflow** with the
previous known-good digest and its original source SHA. It verifies that
image's attestation and requires a fresh `real-prod` approval; it does not use a
mutable tag. If provenance, package access, or approval is unavailable, stop
and follow your organization's incident process rather than bypassing checks.
Do not assume rollback completes data migrations or reverses external effects.

If a failed run leaves `nodejs-demo-app-previous`, inspect container labels,
state and host logs before any manual action; the script refuses to overwrite
it. Record any manual recovery and reconcile the service and change record.

## Verification and limits

Test missing opt-in, malformed SHA/digest, absent reviewer, self-review,
administrator bypass, wrong signer/source, missing attestation, denied package,
failed health check and unavailable runner. A failing case must not promote.
Use a nonproduction host and image, verify rollback and access logs, and obtain
an independent review before any real adoption. The supplied checks are an
example and do not establish trusted production readiness or vendor policy.
