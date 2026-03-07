# Power Platform Solution Template

This template scaffolds a **Power Platform solution** project with Infrastructure-as-Code support, CI/CD pipelines, and source-controlled solution artifacts.

## Supported Power Platform Applications

Select one or more during project creation:

| Application | Description |
|-------------|-------------|
| **Power Apps - Canvas App** | Low-code canvas apps with custom UIs |
| **Power Apps - Model-Driven App** | Data-driven apps built on Dataverse tables |
| **Power Automate** | Cloud flows for process automation |
| **Power BI** | Reports and dashboards |
| **Power Pages** | External-facing portal/website |
| **Copilot Studio** | AI-powered chatbots (formerly Power Virtual Agents) |
| **Custom Connector** | Bridges Power Platform to external REST APIs |

## Project Structure

```
Deployment/
├── Bicep/                          # Azure support infrastructure (Key Vault, etc.)
│   ├── Main.bicep
│   ├── Setup.bicep
│   └── bicepconfig.json
├── Pipeline/                       # CI/CD pipeline definitions
│   ├── Pipeline.yml
│   ├── Build.yml
│   └── Release.yml
└── PowerPlatform/                  # Power Platform-specific deployment scripts
    ├── environment-setup.ps1
    ├── solution-import.ps1
    └── solution-settings/
        ├── dev.json
        ├── test.json
        └── prod.json
solutions/                          # Unpacked solutions (one subfolder per solution)
├── Core/                           # Foundation: Dataverse tables, security roles, env vars
│   └── src/
│       └── Other/Solution.xml
├── Apps/                           # Canvas apps, model-driven apps
│   └── src/
└── Automation/                     # Power Automate cloud flows
    └── src/
```

## Prerequisites

- [Power Platform CLI (pac)](https://learn.microsoft.com/en-us/power-platform/developer/cli/introduction)
- Azure subscription (for support resources)
- Power Platform environment with appropriate licenses
- Azure DevOps or GitHub for CI/CD

## Getting Started

1. Install Power Platform CLI: `dotnet tool install --global Microsoft.PowerApps.CLI.Tool`
2. Authenticate: `pac auth create --url https://your-org.crm.dynamics.com`
3. Clone your solution: `pac solution clone --name YourSolutionName --outputDirectory solutions/Core/src`
4. Make changes in Power Platform maker portal
5. Export and unpack: `pac solution export` then `pac solution unpack`
6. Commit and push — CI/CD handles the rest

## CI/CD Pipeline

The pipeline performs:
1. **Build**: Discovers all solution folders under `solutions/` and packs each into a managed `.zip`
2. **Release**: Imports all solutions in alphabetical order to target environments

Environment-specific settings are applied from:
- Per-solution: `solution-settings/{SolutionName}/{env}.json` (takes priority)
- Shared fallback: `solution-settings/{env}.json`

## Useful Commands

```powershell
# Create a new Power Platform environment
pac admin create --name "MyApp-Dev" --type Sandbox --region europe

# Export solution from dev
pac solution export --name MySolution --path ./solution.zip --managed

# Unpack for source control
pac solution unpack --zipfile ./solution.zip --folder ./solutions/Core/src --processCanvasApps

# Pack for deployment
pac solution pack --folder ./solutions/Core/src --zipfile ./Core.zip --packagetype Managed
```