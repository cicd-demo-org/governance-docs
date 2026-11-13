# Operations and troubleshooting

- **Missing required workflows:** check that `required-workflows` exists, the
  referenced paths are on `main`, the ruleset targets the app, and the plan
  supports required workflows. Rulesets require workflows independently of
  event path filters.
- **Simulation stops:** inspect the first failed job, verify the `DEMO_MODE`
  organization variable equals `simulation`, and verify artifact availability
  and SHA-256 checksums. Do not retry by weakening a gate.
- **Production waits:** an authorized environment reviewer must approve the
  deployment from the Actions run. In the baseline this is the individual
  supplied as `--approver`, not the demo team. Review the run and its synthetic
  scope.
- **CodeQL fails:** confirm the public repository's code scanning entitlement,
  permissions, and Actions policy. CodeQL is a normal workflow, not a
  ruleset-required workflow. A real scan is separate from simulated vendor
  results.
- **Required check never appears:** check the precise `delivery / prod` check
  name and ensure the pull request workflow is enabled and allowed to run.
- **Setup errors:** check administrator scopes, repository names, plan features,
  and GitHub's API error. The script stops on an API error; rerun after fixing
  the cause. It is not transactional, so validate the result afterward.

Review Actions artifacts, run summaries, and environment deployment history as
demo diagnostics. They are not a production audit or retention solution. See
the [real deployment guide](real-deployment.md) for host and rollback recovery.
