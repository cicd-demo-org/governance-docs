# Reproduce in your organization

1. Create an organization with organization-required workflow support. Verify
   the organization's Actions policy allows the pinned public Actions.
2. Create public repositories `.github`, `required-workflows`,
   `nodejs-demo-app`, `admin`, and `governance-docs`. Copy the corresponding
   content, without credentials or customer-specific identifiers.
3. Replace `cicd-demo-org` in caller workflows and links with your organization.
   Publish `required-workflows` first; pin each reusable workflow caller to its
   reviewed full commit SHA. Keep the required workflow files on `main` for the
   ruleset. Push the application and remaining repositories.
4. Authenticate `gh` as an org administrator with `admin:org`, `repo`, and
   `workflow` scopes. In the admin repository run:

   ```sh
   python3 scripts/setup.py --org YOUR_ORG --repo nodejs-demo-app --approver YOUR_LOGIN
   python3 scripts/validate.py --org YOUR_ORG --repo nodejs-demo-app --approver YOUR_LOGIN
   ```

   The setup script owns only its named team, variable, app environments, and
   ruleset, but updates them if they already exist. Review it before running.
   For a different organization verify repository and Actions settings and
   account permissions first. Safe-settings is optional and is not installed.
5. Open an application pull request. Confirm both organization-required
   workflows run and the production job waits for an approved environment
   reviewer. Approve only the simulation, then confirm the required checks pass.
   A reviewer may need to be someone other than the initiator depending on your
   organization and environment settings.
6. Confirm a pull request cannot merge before its required checks succeed. A
   successful simulation does not establish a production-ready control.

See [operations](operations.md) for troubleshooting and [status](status.md)
before adapting this example.
