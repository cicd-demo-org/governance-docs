# Post-setup checklist

Record the owner, date, evidence link and disposition for each item. A checked
demo item is not proof of your organization's production compliance.

## Access and repository governance

- [ ] Confirm administrators and least-privilege application, security,
      change and QA owners; remove unnecessary membership and outside access.
- [ ] Confirm the five intended repositories and their chosen visibility.
      Public source and Actions logs must contain no customer data or secrets.
- [ ] Protect default branches and workflow files; verify required reviews,
      stale-review handling, bypass policy and CODEOWNERS as required by your
      organization. The demo baseline has zero required PR reviews; it is not a
      substitute for a production review policy.
- [ ] Confirm the active organization ruleset targets only intended
      repositories, requires both central workflows and the expected simulated
      `delivery / prod` check, and actually blocks a test PR before approval.
- [ ] Review Actions policy, third-party actions, full-SHA pins, default token
      permissions, fork approval rules, allowed reusable workflows and runner
      access. Keep untrusted PR execution on hosted isolated runners.

## Security settings and credentials

- [ ] Verify entitlement and enable CodeQL/code scanning, dependency alerts,
      secret scanning and push protection where available. Check actual
      configuration and alerts rather than assuming a workflow enables them.
- [ ] Confirm alert severity thresholds and branch merge rules are appropriate;
      the demo runs CodeQL but does not add a CodeQL merge-protection rule.
- [ ] Audit repository, organization and environment variables and secrets. Use
      least-privilege short-lived identities, expiration and rotation; restrict
      environment secret access. Never put secrets into files, logs or artifacts.
- [ ] Verify `DEMO_MODE=simulation` and confirm no simulated pass can satisfy a
      real vendor, QA, change or deployment policy.

## Environments and reviewers

- [ ] Run `scripts/validate.py` from `admin` with the intended org, repository
      and approver. Investigate every failure; setup is not transactional.
- [ ] Verify `dev`, `test` and `prod` exist. The demo script configures the
      individual named by `--approver` as the `prod` reviewer and permits
      self-approval. `demo-release-reviewers` is **not** configured as the
      environment reviewer.
- [ ] For production use, independently define protected branches,
      non-self reviewers, administrator bypass, required independent roles,
      change windows, secret access and evidence retention. A GitHub Environment
      requires only one of its listed reviewers to approve.
- [ ] If enabling the real example, separately create and verify `real-prod`
      with no admin bypass, protected branches and a non-self reviewer; the
      baseline setup intentionally does not create it.

## Optional safe-settings

- [ ] Decide whether to use safe-settings at all. The example configuration is
      optional and no App is installed by the setup script.
- [ ] If adopting it, review the pinned version, GitHub App permissions,
      managed fields, precedence, environment deletion semantics and recovery
      procedure. Avoid overlapping ownership with the setup script or rulesets.
- [ ] Exercise reconciliation in a disposable organization before applying it
      to an existing one; monitor and audit configuration drift.

## End-to-end verification

- [ ] In an isolated checkout with its own manifest, run the Node lint, tests
      and build; run central contract tests and admin baseline tests; build the
      documentation strictly.
- [ ] Open a test PR and verify both organization-required workflows, the
      actual CodeQL run and simulated adapter results. Confirm the PR remains
      blocked while `prod` approval is pending, then approve and confirm the
      required checks pass. Record run links and approver identity.
- [ ] Exercise failed npm/policy checks, absent simulation configuration,
      missing artifact and checksum failure in a disposable test; confirm no
      downstream stage advances. Confirm review rules actually block merging.
- [ ] For real integrations, complete the [replacement guide](replace-integrations.md),
      test authentication, negative verdicts, missing/stale/mismatched
      evidence, errors and timeouts, and get independent owner approval.
- [ ] If opting in to the [real container example](real-deployment.md), verify
      the isolated, restricted runner group and environment, image provenance,
      independent approval, digest, health check, failed-candidate restoration
      and manual rollback on a nonproduction host. Keep a tested recovery plan
      for host loss and registry unavailability.
- [ ] Record all limitations, evidence links, ownership, retention and remaining
      manual steps. Do not describe synthetic passes or this checklist as
      production authorization.
