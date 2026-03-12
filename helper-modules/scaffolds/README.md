# Scaffolds

Canonical scaffold files for generating new Azure integration projects.
These files replace the need to clone template repos — the PlatyPal generator
uses them along with dynamically generated Bicep to create complete project
repositories from scratch.

## Structure

```
scaffolds/
├── pipelines/              # Azure DevOps pipeline YAML files per template type
│   ├── shared/             (Pipeline.yml, Build.yml, Release.yml)
│   ├── integration/
│   ├── api/
│   ├── network/
│   └── powerplatform/
├── github-actions/         # GitHub Actions workflow templates
│   ├── integration.yml
│   ├── api.yml
│   └── powerplatform.yml
├── project-files/          # Shared project files (.gitignore, bicepconfig, etc.)
│   ├── bicepconfig.json    (with ACR module alias placeholder)
│   ├── Setup.bicep         (shared template setup)
│   ├── integration.sln     (.NET solution file)
│   ├── gitignore-dotnet    (382-line VS .gitignore for integration)
│   ├── gitignore-shared    (5-line for shared)
│   ├── gitignore-minimal   (1-line for api/network)
│   └── gitignore-powerplatform
├── code-files/             # Template-specific code and project files
│   ├── integration/
│   │   ├── Function/       (Function.cs, .csproj, Program.cs, NodiniteLoggerUtility.cs, host.json, appsettings.json)
│   │   └── LogicApp/       (host.json, connections.json, parameters.json, .funcignore, Artifacts/, wf-example/)
│   ├── api/
│   │   └── Policies/       (myOperation.policy.xml)
│   └── powerplatform/
│       ├── PowerPlatform/  (environment-setup.ps1, solution-import.ps1, solution-settings/)
│       └── solutions/      (README.md)
├── readme/                 # README templates per project type
│   ├── shared-README.md
│   ├── integration-README.md
│   ├── api-README.md
│   ├── network-README.md
│   └── powerplatform-README.md
└── README.md
```

## Pipeline Tokens

The pipeline files use placeholder tokens that the generator replaces:

| Token | Description |
|-------|-------------|
| `test2` | Integration ID (set by generator or user) |
| `test2` | API ID (API template type only) |
| `_-_acrLoginServer_-_` | Azure Container Registry login server (in bicepconfig.json) |

## Usage

The PlatyPal `ProjectScaffoldService` generator:
1. Creates the project directory structure
2. Copies `pipelines/{type}/` files to `Deployment/Pipeline/`
3. Copies relevant `project-files/` to the repo root (.gitignore, bicepconfig.json, .sln)
4. Copies `code-files/{type}/` content (LogicApp workspace, policies, PP scripts)
5. Copies `readme/{type}-README.md` as the repo's `README.md`
6. Generates `Deployment/Bicep/Main.bicep` dynamically via `BicepGenerationService`
7. Performs token replacement on all files
8. Pushes the assembled repo

No template repos need to be cloned.
