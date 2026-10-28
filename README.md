# CloudOps on Azure

A hands-on project for a **Junior Azure Cloud / DevOps Engineer**: deploy, maintain, monitor and troubleshoot a small Azure environment using Azure CLI, Bash, PowerShell and infrastructure as code.

![CI](https://github.com/Skosanapre08/cloudops-on-azure/actions/workflows/ci.yml/badge.svg)

## Why I built this
(Write 1 or 2 sentences in your own words about what you wanted to learn.)

## Job requirements this project covers

| Duty | Where it is covered |
|---|---|
| Deploy and support virtual machines, storage, networking, Azure SQL, App Services | `infra/main.bicep` (Linux VM, Storage, VNet and subnets, Azure SQL, App Service) |
| Monitor platform health with Azure Monitor | Log Analytics, Application Insights, diagnostic settings, HTTP 5xx and VM CPU alerts |
| Investigate application and system logs | `monitoring/kql-queries.md`, `scripts/bash/tail-logs.sh`, `linux-health-check.sh` |
| Troubleshoot Linux and Windows systems | `linux-health-check.sh`, `Get-WindowsServerHealth.ps1`, runbook sections 7 and 8 |
| Troubleshoot DNS, VPN, virtual networks and subnets | Private DNS zone, `net-troubleshoot.sh`, `Test-NetworkHealth.ps1`, optional VPN template, runbook sections 2 to 5 and 9 |
| Scripting with Azure CLI, Bash and PowerShell | everything in `scripts/` |
| Security fundamentals | HTTPS only, TLS 1.2, SSH keys only (no VM passwords), SSH limited to your IP, NSGs, blob public access disabled, secrets via GitHub secrets |

## What gets deployed

| Azure resource | Purpose |
|---|---|
| Virtual network with `web`, `data` and `vm` subnets, plus NSGs | Networking and traffic rules |
| Ubuntu Linux VM (nginx installed automatically) | Virtual machine workload |
| Private DNS zone `azops.internal` linked to the VNet | Internal name resolution, VM registers itself |
| App Service (Linux, Python) | Hosts the web app |
| Azure SQL server and database | Relational database |
| Storage account | Blob storage, public access disabled |
| Log Analytics and Application Insights | Central logs and app monitoring |
| Metric alerts | More than 5 HTTP 5xx errors in 5 minutes; VM CPU above 80% |

## Project layout

| Path | Contents |
|---|---|
| `infra/main.bicep` | Main infrastructure as code |
| `infra/optional-vpn.bicep` | Optional site-to-site VPN (not deployed by the pipeline) |
| `app/` | Small Flask app with `/health` and `/info` |
| `scripts/azure-cli/` | `deploy.sh`, `cleanup.sh`, `vm-ops.sh` |
| `scripts/bash/` | Status report, log tailing, network and Linux health checks |
| `scripts/powershell/` | Resource report, network health, Windows server health |
| `monitoring/kql-queries.md` | Log Analytics (KQL) queries |
| `docs/troubleshooting-runbook.md` | Step-by-step troubleshooting guide |
| `docs/lab-guide.md` | Hands-on exercises that practise each duty, with evidence to capture |
| `docs/interview-prep.md` | Questions and answers for each area of the job |
| `.github/workflows/ci.yml` | Validates templates and scripts; optional deploy |

## Deploy it yourself

You need an Azure account (a free account works) and the [Azure CLI](https://learn.microsoft.com/cli/azure/install-azure-cli).

```bash
az login
export SQL_ADMIN_PASSWORD='A-strong-Passw0rd!'
./scripts/azure-cli/deploy.sh
```

The script creates an SSH key, restricts SSH to your current IP, deploys everything, and prints the web app URL and VM details.

Day-to-day operations:
```bash
./scripts/bash/azure-status.sh               # what is running
./scripts/azure-cli/vm-ops.sh stop           # stop the VM to save money
./scripts/bash/net-troubleshoot.sh <host>    # DNS, port and HTTP checks
```

**Cost warning:** the VM, App Service (B1) and Azure SQL (Basic) are billed while they exist. Run `./scripts/azure-cli/cleanup.sh` when you are done. The VPN template is the most expensive part, so only deploy it briefly.

## CI/CD

Every pull request and push validates both Bicep templates and lints the shell scripts. A manual run (**Actions → Run workflow → deploy = true**) deploys to Azure using OIDC login, so no long-lived passwords are stored. To enable it, add the repository secrets `AZURE_CLIENT_ID`, `AZURE_TENANT_ID`, `AZURE_SUBSCRIPTION_ID` and `SQL_ADMIN_PASSWORD`, and create a federated credential for your repo on an Azure app registration.

## Skills demonstrated

Azure (VMs, VNets and subnets, NSGs, Private DNS, App Service, SQL, Storage, VPN), Bicep, Azure CLI, Bash, PowerShell, Azure Monitor, Log Analytics and KQL, GitHub Actions, Linux and Windows troubleshooting, and security fundamentals.

## License

MIT
