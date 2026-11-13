# CI/CD delivery demo

This example shows a small Node.js application, organization-managed checks,
and a simulated progression through three GitHub Environments. It uses no
external vendor accounts or deployment secrets.

| Repository | Purpose |
| --- | --- |
| [.github](https://github.com/cicd-demo-org/.github) | Profile, templates, and security policy |
| [required-workflows](https://github.com/cicd-demo-org/required-workflows) | Shared checks and simulated delivery |
| [nodejs-demo-app](https://github.com/cicd-demo-org/nodejs-demo-app) | Sample application and callers |
| [admin](https://github.com/cicd-demo-org/admin) | Organization setup and validation |
| [governance-docs](https://github.com/cicd-demo-org/governance-docs) | This documentation |

**Simulation only:** passing synthetic results do not prove security, quality,
change approval, or a real deployment. See [architecture](architecture.md),
[reproduction](reproduce.md), [integration replacement](replace-integrations.md),
[the optional real deployment example](real-deployment.md),
[the post-setup checklist](post-setup-checklist.md), and
[implementation status](status.md).
