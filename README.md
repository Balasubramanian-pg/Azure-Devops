# Azure-Devops
Got it — you want the plan built around **Azure DevOps (the product)** specifically, for a Data Engineer role. That changes the entire structure. I'll assume moderate Git knowledge and focus on Azure DevOps services, data engineering integration, and Azure data platform deployment patterns.

---

## Phase 1: Azure DevOps Foundations (Days 1–8)

### Day 1: Azure DevOps Services Overview & Project Setup

```markdown
# Day 1: Azure DevOps Services Overview & Project Setup

## Why This Matters for Data Engineering
Azure DevOps is the end-to-end platform Microsoft shops use for data pipeline development. You need to understand all five services before you can use them together for data engineering workflows.

## Core Concepts
- The five services: Azure Boards, Azure Repos, Azure Pipelines, Azure Test Plans, Azure Artifacts
- Organization vs Project vs Team structure
- Creating an organization and project for a data engineering team
- User roles and permissions (Basic, Stakeholder, Project Admin)
- Azure DevOps CLI and REST API basics
- Azure DevOps vs GitHub vs GitLab — when to choose what

## Hands-On Lab
1. Create an Azure DevOps organization if you don't have one
2. Create a project named `data-platform` with Git as the version control
3. Set up a team `Data Engineering` and add a sample user
4. Navigate all five services in the UI
5. Install the Azure DevOps CLI and authenticate

## Data Engineering Tie-In
Data teams often organize projects by domain (e.g., "Marketing Analytics", "Supply Chain Data"). Understanding project structure prevents fragmentation across teams.

## Further Reading
- Azure DevOps Services overview (Microsoft Learn)
- "Azure DevOps for Data Teams" (Microsoft Docs)
```

### Day 2: Azure Repos — Git for Data Engineering Teams

```markdown
# Day 2: Azure Repos — Git for Data Engineering Teams

## Why This Matters for Data Engineering
Azure Repos is where your ADF pipelines, Databricks notebooks, SQL scripts, Terraform configs, and Airflow DAGs live. Unlike GitHub, it integrates natively with Azure Pipelines and ADF Git integration.

## Core Concepts
- Azure Repos vs GitHub repos (when to use which)
- Repository creation and structure for data projects
- Branch policies: required reviewers, build validation, linked work items
- Pull request workflow for data pipeline changes
- Branching strategy for data teams (trunk-based, GitFlow)
- ADF Git integration (adf_publish branch)
- Large File Storage (LFS) for large datasets or ML models
- Notifications and code review comments

## Hands-On Lab
1. Create a repo `data-platform` with folders: `/adf`, `/databricks`, `/sql`, `/terraform`, `/dags`
2. Add a branch policy on `main` requiring 1 reviewer and a successful pipeline
3. Create a feature branch, make a change, and open a PR
4. Review the PR and complete it
5. Explore the "Files", "Commits", "Pushes", and "Branches" views

## Data Engineering Tie-In
ADF uses Azure Repos for its Git integration — when you edit a pipeline in ADF, changes are committed to a branch. This is the foundation of ADF CI/CD.

## Further Reading
- Azure Repos documentation (Microsoft Learn)
- "Git Branching Strategies for Data Teams"
```

### Day 3: Azure Boards for Data Engineering Work Tracking

```markdown
# Day 3: Azure Boards for Data Engineering Work Tracking

## Why This Matters for Data Engineering
Data pipelines have multiple stakeholders (analysts, scientists, business users). Boards connects pipeline work to business requirements and tracks progress across data initiatives.

## Core Concepts
- Work item types: Epic, Feature, User Story, Bug, Task
- Agile process: Kanban boards, sprint boards, backlogs
- Query and filter work items
- Linking work items to commits and PRs
- Dashboards and reporting
- Integrating Boards with GitHub/Repos for traceability

## Hands-On Lab
1. Create a Kanban board with columns: Backlog → In Progress → Code Review → Testing → Done
2. Create work items:
   - Epic: "Build Sales Analytics Data Pipeline"
   - Feature: "Ingest from Salesforce API"
   - User Story: "As an analyst, I want daily sales data in the warehouse"
   - Task: "Write ADF pipeline for Salesforce ingestion"
3. Link a Task to a Git commit
4. Create a query for "All active bugs in data pipelines"

## Data Engineering Tie-In
Traceability from business requirement → data pipeline → deployment is critical for audits and compliance in regulated industries.

## Further Reading
- Azure Boards documentation (Microsoft Learn)
- "Agile Data Engineering with Azure Boards"
```

### Day 4: Azure Pipelines — Core Concepts & YAML Fundamentals

```markdown
# Day 4: Azure Pipelines — Core Concepts & YAML Fundamentals

## Why This Matters for Data Engineering
Azure Pipelines is the CI/CD engine of Azure DevOps. It builds, tests, and deploys data pipelines (ADF, Databricks, SQL, Spark) across environments.

## Core Concepts
- Pipelines vs Releases (classic vs YAML)
- YAML pipeline structure: `trigger`, `pool`, `stages`, `jobs`, `steps`
- Agents: Microsoft-hosted vs self-hosted
- Variables and variable groups
- Templates: reusable steps, jobs, stages
- Pipeline artifacts
- Environments and approvals

## Hands-On Lab
1. Create a YAML pipeline `azure-pipelines.yml` that:
   - Triggers on `main` and PRs
   - Uses `ubuntu-latest` agent
   - Runs a Python script and prints output
2. Add a variable `environment: dev`
3. Parameterize the pipeline with `env` (dev/staging/prod)
4. Run the pipeline manually and on commit

```yaml
trigger:
  branches:
    include: [main]

parameters:
  - name: env
    default: dev
    values: [dev, staging, prod]

pool:
  vmImage: ubuntu-latest

variables:
  pythonVersion: '3.11'

stages:
  - stage: Build
    jobs:
      - job: Test
        steps:
          - task: UsePythonVersion@0
            inputs:
              versionSpec: $(pythonVersion)
          - script: python -c "print('Hello from data pipeline')"
            displayName: 'Run test script'
```

## Data Engineering Tie-In
Every ADF pipeline, dbt project, and Databricks job should have a YAML pipeline. This is the "hello world" of DataOps on Azure.

## Further Reading
- Azure Pipelines YAML schema (Microsoft Learn)
- "Azure Pipelines for Data Engineers"
```

### Day 5: Azure Artifacts for Data Engineering Packages

```markdown
# Day 5: Azure Artifacts for Data Engineering Packages

## Why This Matters for Data Engineering
Data teams share Python wheels, dbt packages, and internal libraries. Azure Artifacts is the private package registry that integrates with your pipelines.

## Core Concepts
- Feeds: project-scoped vs organization-scoped
- Package types: PyPI, npm, NuGet, Maven, Universal Packages
- Upstream sources (proxy public packages)
- Connecting to feeds from `pip`, `poetry`, `conda`
- Authenticating with personal access tokens (PATs)
- Pipeline integration: publish and consume artifacts

## Hands-On Lab
1. Create a feed `data-engineering-packages`
2. Build a Python wheel from a custom ETL utility library
3. Publish the wheel to the feed
4. Install it from the feed using `pip`
5. Configure the feed as an upstream for PyPI

```bash
# Add feed to pip config
pip install keyring artifacts-keyring
pip install my-etl-utils --index-url https://pkgs.dev.azure.com/org/_packaging/data-engineering-packages/pypi/simple/
```

## Data Engineering Tie-In
Internal packages (schema validators, API clients, transformation helpers) should be versioned and distributed via Artifacts, not copy-pasted across repos.

## Further Reading
- Azure Artifacts documentation (Microsoft Learn)
- "Publishing Python Packages to Azure Artifacts"
```

### Day 6: Azure Test Plans for Data Pipeline Testing

```markdown
# Day 6: Azure Test Plans for Data Pipeline Testing

## Why This Matters for Data Engineering
Data pipelines need both code tests (pytest) and manual/exploratory tests (e.g., verifying a dashboard after a deployment). Test Plans bridges both.

## Core Concepts
- Test Plans vs automated tests in Pipelines
- Test suites: static, requirement-based, query-based
- Test cases and shared steps
- Manual testing for data validation
- Integration with Pipelines for automated test results
- Parameterized tests for multiple environments

## Hands-On Lab
1. Create a Test Plan "Sales Pipeline UAT"
2. Add a Test Suite for "Daily Sales Ingestion"
3. Create test cases:
   - "Verify sales data lands in raw layer by 6 AM"
   - "Verify row count matches source"
   - "Verify no nulls in primary key"
4. Run the test cases and record results
5. Publish automated pytest results to the pipeline

```yaml
- task: PublishTestResults@2
  inputs:
    testResultsFormat: 'JUnit'
    testResultsFiles: '**/test-results.xml'
```

## Data Engineering Tie-In
UAT for data pipelines is often manual ("does the report look right?"). Test Plans formalizes this without losing the ability to automate.

## Further Reading
- Azure Test Plans documentation (Microsoft Learn)
- "Testing Data Pipelines with Azure DevOps"
```

### Day 7: Agent Pools — Hosted vs Self-Hosted for Data Workloads

```markdown
# Day 7: Agent Pools — Hosted vs Self-Hosted for Data Workloads

## Why This Matters for Data Engineering
Data pipelines often need specific dependencies (Java, Spark, ODBC drivers, Databricks CLI) that Microsoft-hosted agents don't have. Self-hosted agents solve this.

## Core Concepts
- Microsoft-hosted agents: Ubuntu, Windows, macOS
- Self-hosted agents: VM, container, Kubernetes
- Agent pools and capabilities
- Installing software on agents (Java, Spark, ODBC, Databricks CLI)
- Scaling agents with Azure Container Apps or AKS
- Security: agent service accounts, network access to data sources

## Hands-On Lab
1. Create a self-hosted agent on an Azure VM
2. Install Java 11, Spark 3.5, and Databricks CLI
3. Register the agent in a pool `data-agents`
4. Create a pipeline that runs on this pool and executes `spark-submit --version`
5. Verify the agent appears in the pool

```yaml
pool:
  name: data-agents
  demands:
    - java
    - spark
```

## Data Engineering Tie-In
Self-hosted agents are essential when pipelines need to connect to on-premises databases, Hadoop clusters, or VNets that hosted agents can't reach.

## Further Reading
- Azure Pipelines agents documentation
- "Self-Hosted Agents for Data Engineering"
```

### Day 8: Variable Groups, Secrets & Azure Key Vault Integration

```markdown
# Day 8: Variable Groups, Secrets & Azure Key Vault Integration

## Why This Matters for Data Engineering
Data pipelines handle database credentials, storage account keys, and API tokens. Azure DevOps variable groups + Key Vault provide secure, centralized secret management.

## Core Concepts
- Variable groups: shared across pipelines
- Secret variables (masked in logs)
- Azure Key Vault integration with variable groups
- Linking Key Vault to variable groups
- Environment-specific variable groups (dev/staging/prod)
- Using variables in YAML: `$(variableName)`
- Runtime parameters vs variables

## Hands-On Lab
1. Create a Key Vault with secrets: `sql-password`, `storage-key`, `databricks-token`
2. Create variable groups: `data-pipeline-dev`, `data-pipeline-prod`
3. Link each group to Key Vault (dev vault vs prod vault)
4. Use the variable group in a pipeline to connect to a database
5. Verify secrets are masked in pipeline logs

```yaml
variables:
  - group: data-pipeline-$(env)

steps:
  - script: |
      echo "Connecting to $(sqlServer) with user $(sqlUser)"
      # Password is loaded from Key Vault, never printed
    env:
      SQL_PASSWORD: $(sql-password)
```

## Data Engineering Tie-In
Never hardcode credentials in ADF linked services, Databricks notebooks, or pipeline YAML. Key Vault + variable groups is the standard pattern.

## Further Reading
- "Link a Key Vault to a variable group" (Microsoft Learn)
- "Azure Key Vault for Data Engineers"
```

---

## Phase 2: Azure Pipelines Deep Dive (Days 9–18)

### Day 9: Multi-Stage YAML Pipelines for Data Platforms

```markdown
# Day 9: Multi-Stage YAML Pipelines for Data Platforms

## Why This Matters for Data Engineering
A data platform deployment has stages: build → test → deploy to dev → test → deploy to staging → approve → deploy to prod. Multi-stage pipelines model this.

## Core Concepts
- Stages: Build, Test, DeployDev, DeployStaging, DeployProd
- Dependencies between stages (`dependsOn`)
- Conditional stages (`condition`)
- Environment approvals and checks
- Deployment jobs vs regular jobs
- Stage-level variables

## Hands-On Lab
Create a multi-stage pipeline for an ADF deployment:
1. **Build**: Generate ARM templates from ADF
2. **DeployDev**: Deploy to dev ADF
3. **TestDev**: Run validation tests
4. **DeployProd**: Requires manual approval

```yaml
stages:
  - stage: Build
    jobs:
      - job: BuildADF
        steps:
          - task: PowerShell@2
            inputs:
              targetType: 'inline'
              script: 'npm install && npm run build'

  - stage: DeployDev
    dependsOn: Build
    jobs:
      - deployment: DeployADFDev
        environment: 'adf-dev'
        strategy:
          runOnce:
            deploy:
              steps:
                - task: AzureResourceManagerTemplateDeployment@3
                  inputs:
                    deploymentScope: 'Resource Group'
                    azureResourceManagerConnection: 'AzureServiceConnection'
                    subscriptionId: '$(subscriptionId)'
                    resourceGroupName: 'rg-adf-dev'
                    location: 'East US'
                    templateLocation: 'Linked artifact'
                    csmFile: '$(Pipeline.Workspace)/drop/ARMTemplateForFactory.json'
                    csmParametersFile: '$(Pipeline.Workspace)/drop/ARMTemplateParametersForFactory.json'
                    overrideParameters: '-factoryName $(adfNameDev)'

  - stage: DeployProd
    dependsOn: DeployDev
    condition: succeeded()
    jobs:
      - deployment: DeployADFProd
        environment: 'adf-prod'
        strategy:
          runOnce:
            deploy:
              steps:
                - task: AzureResourceManagerTemplateDeployment@3
                  inputs:
                    deploymentScope: 'Resource Group'
                    azureResourceManagerConnection: 'AzureServiceConnection'
                    subscriptionId: '$(subscriptionId)'
                    resourceGroupName: 'rg-adf-prod'
                    location: 'East US'
                    templateLocation: 'Linked artifact'
                    csmFile: '$(Pipeline.Workspace)/drop/ARMTemplateForFactory.json'
                    csmParametersFile: '$(Pipeline.Workspace)/drop/ARMTemplateParametersForFactory.json'
                    overrideParameters: '-factoryName $(adfNameProd)'
```

## Data Engineering Tie-In
Every production data pipeline deployment should go through approval gates. Multi-stage pipelines enforce this.

## Further Reading
- "Specify jobs in a pipeline" (Microsoft Learn)
- "Multi-stage YAML pipelines for data platforms"
```

### Day 10: Azure Data Factory CI/CD with Azure Pipelines

```markdown
# Day 10: Azure Data Factory CI/CD with Azure Pipelines

## Why This Matters for Data Engineering
ADF is the most common data integration tool in Azure. Its CI/CD pattern (Git integration + ARM template deployment) is unique and essential to master.

## Core Concepts
- ADF Git integration: `adf_publish` branch
- ARM templates: `ARMTemplateForFactory.json`, `ARMTemplateParametersForFactory.json`
- Building ADF: `npm install`, `npm run build`
- Deploying ADF: AzureResourceManagerTemplateDeployment task
- Overriding linked service parameters (dev vs prod)
- Trigger management (stop/start triggers during deployment)
- Global parameters for environment-specific values

## Hands-On Lab
1. Create a dev ADF with a pipeline that copies data from Blob to SQL
2. Connect ADF to Azure Repos (Git integration)
3. Create a build pipeline that:
   - Installs npm dependencies
   - Runs `npm run build` to generate ARM templates
   - Publishes ARM templates as pipeline artifacts
4. Create a release pipeline that:
   - Deploys ARM templates to dev
   - Requires approval for prod
   - Deploys to prod
   - Stops and restarts triggers

```yaml
# Build pipeline
steps:
  - task: NodeTool@0
    inputs:
      versionSpec: '16.x'

  - script: |
      npm install
      npm run build
    displayName: 'Build ADF ARM templates'

  - task: PublishPipelineArtifact@1
    inputs:
      targetPath: '$(Build.SourcesDirectory)/adf_publish'
      artifact: 'adf-artifacts'
```

## Data Engineering Tie-In
ADF's Git integration is the foundation of its CI/CD. Without it, deployments are manual and error-prone.

## Further Reading
- "Continuous integration and delivery in Azure Data Factory" (Microsoft Learn)
- "ADF CI/CD Best Practices"
```

### Day 11: Azure Databricks CI/CD with Azure Pipelines

```markdown
# Day 11: Azure Databricks CI/CD with Azure Pipelines

## Why This Matters for Data Engineering
Databricks is the primary Spark platform on Azure. CI/CD for Databricks involves building Python wheels, running unit tests, and deploying notebooks/jobs.

## Core Concepts
- Databricks Repos vs Azure Repos integration
- Building Python wheels for Databricks
- Unit testing with pytest on Databricks code
- Deploying notebooks with Databricks CLI or REST API
- Databricks Asset Bundles (DABs) for deployment
- Job deployment and trigger configuration
- Using Databricks service principal for authentication

## Hands-On Lab
1. Create a Databricks workspace and connect it to Azure Repos
2. Create a Python package `etl_utils` with a transform function
3. Write unit tests with pytest
4. Create a build pipeline that:
   - Builds the wheel
   - Runs unit tests
   - Publishes the wheel to Azure Artifacts
5. Create a deploy pipeline that:
   - Uploads the wheel to DBFS
   - Creates/updates a Databricks job using the wheel

```yaml
# Build pipeline
steps:
  - task: UsePythonVersion@0
    inputs:
      versionSpec: '3.10'

  - script: |
      pip install -r requirements.txt
      pip install build
      python -m build --wheel
      pytest tests/
    displayName: 'Build and test Python wheel'

  - task: PublishPipelineArtifact@1
    inputs:
      targetPath: 'dist/'
      artifact: 'python-wheel'

# Deploy pipeline
steps:
  - task: UsePythonVersion@0
    inputs:
      versionSpec: '3.10'

  - script: pip install databricks-cli
    displayName: 'Install Databricks CLI'

  - script: |
      databricks fs cp dist/*.whl dbfs:/FileStore/wheels/ --overwrite
      databricks jobs create --json-file job-config.json
    displayName: 'Deploy wheel and job'
    env:
      DATABRICKS_HOST: $(databricksHost)
      DATABRICKS_TOKEN: $(databricksToken)
```

## Data Engineering Tie-In
Databricks notebooks are not testable in isolation. Packaging logic as Python wheels and testing them in CI is the production-grade approach.

## Further Reading
- "Continuous integration and delivery on Databricks using Azure DevOps" (Microsoft Learn)
- "Databricks Asset Bundles" documentation
```

### Day 12: Azure Synapse Analytics CI/CD

```markdown
# Day 12: Azure Synapse Analytics CI/CD

## Why This Matters for Data Engineering
Synapse combines SQL pools, Spark pools, and pipelines. Its CI/CD pattern is similar to ADF but with additional SQL database project deployment.

## Core Concepts
- Synapse Git integration (similar to ADF)
- ARM templates for Synapse workspaces
- SQL Server Data Tools (SSDT) database projects for dedicated SQL pools
- Deploying SQL scripts via Azure Pipelines
- Synapse workspace deployment task
- Spark pool and SQL pool configuration as code

## Hands-On Lab
1. Create a Synapse workspace with a dedicated SQL pool
2. Connect Synapse to Azure Repos
3. Create a SQL database project with table definitions
4. Create a pipeline that:
   - Builds the SQL project (DACPAC)
   - Deploys the DACPAC to dev SQL pool
   - Deploys Synapse artifacts (pipelines, notebooks) via ARM templates
   - Promotes to prod

```yaml
# Build SQL project
steps:
  - task: VSBuild@1
    inputs:
      solution: '**/*.sqlproj'
      platform: 'Any CPU'
      configuration: 'Release'

  - task: CopyFiles@2
    inputs:
      contents: '**/*.dacpac'
      targetFolder: '$(Build.ArtifactStagingDirectory)'

  - task: PublishPipelineArtifact@1
    inputs:
      targetPath: '$(Build.ArtifactStagingDirectory)'
      artifact: 'sql-dacpac'

# Deploy SQL project
steps:
  - task: SqlAzureDacpacDeployment@1
    inputs:
      azureSubscription: 'AzureServiceConnection'
      ServerName: '$(synapseServer)'
      DatabaseName: '$(sqlPoolName)'
      SqlUsername: '$(sqlUser)'
      SqlPassword: '$(sqlPassword)'
      deployType: 'DacpacTask'
      DeploymentAction: 'Publish'
      DacpacFile: '$(Pipeline.Workspace)/sql-dacpac/**/*.dacpac'
```

## Data Engineering Tie-In
Synapse SQL pool schema changes must be versioned and deployed through CI/CD, not manually executed in SSMS.

## Further Reading
- "Continuous integration and deployment for dedicated SQL pool" (Microsoft Learn)
- "Synapse CI/CD Best Practices"
```

### Day 13: dbt CI/CD with Azure Pipelines

```markdown
# Day 13: dbt CI/CD with Azure Pipelines

## Why This Matters for Data Engineering
dbt is the standard for SQL transformations on Azure (Synapse, Databricks SQL, Snowflake). CI/CD ensures models are tested and deployed safely.

## Core Concepts
- dbt project structure: models, tests, macros, seeds
- `dbt build` in CI
- Slim CI: run only changed models with `state:modified+`
- State comparison with `--state` and `--defer`
- dbt artifacts: `manifest.json`, `run_results.json`
- Deployment to prod via Airflow, dbt Cloud, or Azure Pipelines
- Testing: schema tests, custom data tests

## Hands-On Lab
1. Create a dbt project targeting Synapse or Databricks SQL
2. Write staging and mart models
3. Add schema tests: `unique`, `not_null`, `relationships`
4. Create a PR pipeline that runs `dbt build --select state:modified+`
5. Create a deploy pipeline that runs full `dbt build` in prod

```yaml
# PR validation
steps:
  - script: |
      pip install dbt-synapse
      dbt deps
      dbt build --select state:modified+ --defer --state ./prod-manifest
    displayName: 'dbt Slim CI'

# Production deployment
steps:
  - script: |
      dbt deps
      dbt build --target prod
    displayName: 'dbt Full Build'
```

## Data Engineering Tie-In
Slim CI with state comparison reduces dbt CI time from hours to minutes for large projects.

## Further Reading
- dbt "Continuous Integration" guide
- "dbt on Azure with Azure DevOps"
```

### Day 14: Airflow DAG Deployment with Azure Pipelines

```markdown
# Day 14: Airflow DAG Deployment with Azure Pipelines

## Why This Matters for Data Engineering
Airflow orchestrates data pipelines. DAGs are code and should be deployed via CI/CD, not manually copied to a shared filesystem.

## Core Concepts
- DAG testing: import errors, DAG integrity, task dependencies
- `airflow dags test` and `airflow tasks test`
- Deployment patterns:
  - Git-sync sidecar (Kubernetes)
  - S3/GCS bucket sync
  - Azure Blob sync
  - Direct file copy to Airflow workers
- Environment separation (dev/staging/prod)
- DAG versioning and rollback

## Hands-On Lab
1. Create an Airflow DAG that runs a Databricks job
2. Write a test that validates all DAGs import without errors
3. Create a build pipeline that:
   - Runs DAG import tests
   - Packages DAGs as a zip artifact
4. Create a deploy pipeline that:
   - Uploads DAGs to Azure Blob Storage (synced to Airflow)
   - Triggers a DAG refresh

```yaml
# Build pipeline
steps:
  - script: |
      pip install apache-airflow pytest
      pytest tests/test_dags.py
    displayName: 'Test DAG integrity'

  - task: ArchiveFiles@2
    inputs:
      rootFolderOrFile: 'dags/'
      archiveType: 'zip'
      archiveFile: '$(Build.ArtifactStagingDirectory)/dags.zip'

  - task: PublishPipelineArtifact@1
    inputs:
      targetPath: '$(Build.ArtifactStagingDirectory)/dags.zip'
      artifact: 'airflow-dags'

# Deploy pipeline
steps:
  - task: AzureCLI@2
    inputs:
      azureSubscription: 'AzureServiceConnection'
      scriptType: 'bash'
      scriptLocation: 'inlineScript'
      inlineScript: |
        az storage blob upload \
          --account-name $(storageAccount) \
          --container-name airflow-dags \
          --file $(Pipeline.Workspace)/airflow-dags/dags.zip \
          --name dags.zip \
          --overwrite
```

## Data Engineering Tie-In
DAG deployment via CI/CD ensures every environment runs the same DAG code and prevents "it works on my machine" failures.

## Further Reading
- "Test, synchronize, and deploy your DAGs from GitHub" (Google Cloud)
- "Airflow CI/CD with Azure DevOps"
```

### Day 15: Terraform with Azure Pipelines

```markdown
# Day 15: Terraform with Azure Pipelines

## Why This Matters for Data Engineering
Terraform provisions Azure data infrastructure: Storage Accounts, Data Factory, Databricks workspaces, Synapse, Key Vaults. CI/CD ensures infrastructure changes are reviewed and applied safely.

## Core Concepts
- Terraform in Azure Pipelines: `init`, `plan`, `apply`
- Remote state in Azure Storage Account
- State locking with blob leases
- Service connections for Azure authentication
- Plan output as PR comment
- Approval gates before `apply`
- Workspaces for environment separation

## Hands-On Lab
1. Create a Terraform config that provisions:
   - Resource Group
   - Storage Account (ADLS Gen2)
   - Data Factory
   - Key Vault
2. Configure remote state in Azure Storage
3. Create a PR pipeline that runs `terraform plan`
4. Create a deploy pipeline that runs `terraform apply` after approval

```yaml
# PR validation (plan)
steps:
  - task: TerraformInstaller@0
    inputs:
      terraformVersion: '1.6.0'

  - script: |
      terraform init \
        -backend-config="resource_group_name=$(tfStateRg)" \
        -backend-config="storage_account_name=$(tfStateSa)" \
        -backend-config="container_name=tfstate" \
        -backend-config="key=data-platform.tfstate"
      terraform plan -out=tfplan
    displayName: 'Terraform Plan'

# Deploy (apply)
steps:
  - script: terraform apply -auto-approve tfplan
    displayName: 'Terraform Apply'
```

## Data Engineering Tie-In
Terraform ensures every new data source gets consistent infrastructure: encryption, versioning, tagging, and access controls.

## Further Reading
- "Terraform with Azure Pipelines" (Microsoft Learn)
- "Infrastructure as Code for Data Platforms on Azure"
```

### Day 16: Azure Pipelines Templates & Reusability

```markdown
# Day 16: Azure Pipelines Templates & Reusability

## Why This Matters for Data Engineering
Data teams have multiple pipelines (ADF, Databricks, dbt, Airflow) that share common patterns. Templates eliminate duplication.

## Core Concepts
- Template types: steps, jobs, stages, variables
- Template parameters
- Template expressions: `${{ }}` vs `$( )` vs `$[ ]`
- Extending templates (`extends`)
- Template repositories (separate repo for templates)
- Conditional logic in templates

## Hands-On Lab
1. Create a template repo `pipeline-templates`
2. Create templates:
   - `steps/build-python.yml`
   - `steps/deploy-adf.yml`
   - `stages/deploy-environment.yml`
3. Use these templates in an ADF pipeline and a Databricks pipeline
4. Parameterize the templates for dev/prod

```yaml
# templates/stages/deploy-adf.yml
parameters:
  - name: environment
    type: string
  - name: serviceConnection
    type: string

stages:
  - stage: Deploy_${{ parameters.environment }}
    jobs:
      - deployment: DeployADF
        environment: 'adf-${{ parameters.environment }}'
        strategy:
          runOnce:
            deploy:
              steps:
                - template: steps/deploy-adf.yml
                  parameters:
                    serviceConnection: ${{ parameters.serviceConnection }}
                    resourceGroup: 'rg-adf-${{ parameters.environment }}'
```

## Data Engineering Tie-In
Templates enforce standards: every data pipeline uses the same testing, security scanning, and deployment patterns.

## Further Reading
- "Template types & usage" (Microsoft Learn)
- "Reusable YAML Templates for Data Pipelines"
```

### Day 17: Pipeline Triggers & Scheduling for Data Workloads

```markdown
# Day 17: Pipeline Triggers & Scheduling for Data Workloads

## Why This Matters for Data Engineering
Data pipelines run on schedules, on data arrival, or on code changes. Azure Pipelines supports all three trigger types.

## Core Concepts
- CI triggers: branch, path, batch
- PR triggers
- Scheduled triggers (cron)
- Pipeline triggers (trigger another pipeline)
- Resource triggers (trigger on artifact publish)
- Path filters: only run when data pipeline code changes
- Batch mode: avoid concurrent runs

## Hands-On Lab
1. Configure a pipeline with:
   - CI trigger on `main` and `feature/*`
   - Path filter: only run when `/adf/` or `/databricks/` changes
   - Scheduled trigger: daily at 2 AM
2. Create a pipeline trigger: when the ADF build pipeline succeeds, trigger the Databricks pipeline
3. Test path filters by changing a file outside the filter path

```yaml
trigger:
  branches:
    include:
      - main
      - feature/*
  paths:
    include:
      - adf/*
      - databricks/*
    exclude:
      - docs/*

schedules:
  - cron: "0 2 * * *"
    displayName: 'Daily 2 AM build'
    branches:
      include: [main]
    always: true

resources:
  pipelines:
    - pipeline: adf-build
      source: 'ADF-Build'
      trigger:
        branches:
          include: [main]
```

## Data Engineering Tie-In
Path filters prevent unnecessary pipeline runs when only documentation changes, saving agent minutes and cost.

## Further Reading
- "Triggers in Azure Pipelines" (Microsoft Learn)
- "Scheduled Triggers for Data Pipelines"
```

### Day 18: Pipeline Caching & Performance Optimization

```markdown
# Day 18: Pipeline Caching & Performance Optimization

## Why This Matters for Data Engineering
Data pipelines have large dependencies (Spark, Python libraries, npm packages). Caching dramatically reduces build times.

## Core Concepts
- Pipeline caching: `Cache@2` task
- Cache keys: dependency file hash, branch, OS
- Restore keys for partial cache hits
- Docker layer caching
- Parallel jobs and matrix strategies
- Artifact compression
- Self-hosted agents with pre-installed dependencies

## Hands-On Lab
1. Add caching for pip dependencies in a Python pipeline
2. Add caching for npm dependencies in an ADF build pipeline
3. Measure build time before and after caching
4. Implement a matrix strategy to test multiple Python versions in parallel

```yaml
variables:
  PIP_CACHE_DIR: $(Pipeline.Workspace)/.pip

steps:
  - task: Cache@2
    inputs:
      key: 'pip | "$(Agent.OS)" | requirements.txt'
      restoreKeys: |
        pip | "$(Agent.OS)"
      path: $(PIP_CACHE_DIR)
    displayName: 'Cache pip dependencies'

  - script: |
      pip install -r requirements.txt
    displayName: 'Install dependencies'
```

## Data Engineering Tie-In
A Databricks pipeline that takes 20 minutes without caching can drop to 5 minutes with cached wheels and dependencies.

## Further Reading
- "Pipeline caching" (Microsoft Learn)
- "Optimizing Azure Pipelines for Data Workloads"
```

---

## Phase 3: Advanced Azure DevOps for Data Engineering (Days 19–28)

### Day 19: Azure DevOps REST API for Data Pipeline Automation

```markdown
# Day 19: Azure DevOps REST API for Data Pipeline Automation

## Why This Matters for Data Engineering
Sometimes you need to trigger pipelines, query runs, or manage artifacts programmatically — from Airflow, a script, or a custom tool.

## Core Concepts
- Authentication: PAT, OAuth, managed identity
- REST API endpoints: pipelines, runs, artifacts, variables
- Triggering a pipeline from another system
- Querying pipeline status and logs
- Managing variable groups via API
- Webhooks for pipeline events
- Azure DevOps CLI as an alternative

## Hands-On Lab
1. Create a PAT with `Pipelines: Read & Execute` scope
2. Use `curl` or Python to:
   - Trigger a pipeline run
   - Query the run status
   - Download artifacts
3. Create a webhook that sends a Slack message when a pipeline fails
4. Trigger a pipeline from an Airflow DAG using the API

```python
import requests

org = "myorg"
project = "data-platform"
pipeline_id = 42
pat = "your-pat"

url = f"https://dev.azure.com/{org}/{project}/_apis/pipelines/{pipeline_id}/runs?api-version=7.0"

response = requests.post(
    url,
    headers={"Authorization": f"Basic {pat}"},
    json={"resources": {"repositories": {"self": {"refName": "refs/heads/main"}}}}
)

run_id = response.json()["id"]
print(f"Pipeline run started: {run_id}")
```

## Data Engineering Tie-In
Airflow can trigger Azure Pipelines for infrastructure changes, or Azure Pipelines can trigger Airflow DAGs via the Airflow REST API.

## Further Reading
- "Azure DevOps REST API" (Microsoft Learn)
- "Automating Azure DevOps with Python"
```

### Day 20: Azure DevOps CLI for Data Engineers

```markdown
# Day 20: Azure DevOps CLI for Data Engineers

## Why This Matters for Data Engineering
The `az devops` CLI lets you manage pipelines, repos, and artifacts from the terminal — essential for scripting and automation.

## Core Concepts
- Installation: `az extension add --name azure-devops`
- Authentication: `az devops login`
- Default organization and project config
- Pipeline commands: `az pipelines run`, `az pipelines runs list`
- Repo commands: `az repos pr create`, `az repos pr list`
- Artifact commands: `az artifacts universal download`
- Variable group commands: `az pipelines variable-group list`

## Hands-On Lab
1. Install the Azure DevOps CLI extension
2. Configure defaults: `az devops configure --defaults organization=... project=...`
3. List pipelines: `az pipelines list`
4. Run a pipeline: `az pipelines run --name "ADF-Build"`
5. Check run status: `az pipelines runs show --id 123`
6. Create a PR: `az repos pr create --source-branch feature --target-branch main`

```bash
# Run pipeline and wait for completion
run_id=$(az pipelines run --name "Data-Pipeline-CI" --query "id" -o tsv)
az pipelines runs show --id $run_id --query "status"
```

## Data Engineering Tie-In
CLI commands can be embedded in Airflow BashOperator tasks, cron jobs, or deployment scripts.

## Further Reading
- "Azure DevOps CLI" (Microsoft Learn)
- "Azure DevOps CLI for Data Engineers"
```

### Day 21: Azure DevOps Security & Permissions for Data Teams

```markdown
# Day 21: Azure DevOps Security & Permissions for Data Teams

## Why This Matters for Data Engineering
Data teams handle sensitive pipelines and credentials. Azure DevOps permissions must follow least privilege.

## Core Concepts
- Organization-level permissions
- Project-level permissions: Build, Release, Repos, Boards
- Pipeline permissions: service connections, agent pools, variable groups
- Service connections: Azure Resource Manager, Key Vault, Docker Registry
- Security groups: Project Administrators, Contributors, Readers
- Pipeline approval permissions
- Audit logging and security policies

## Hands-On Lab
1. Create a security group `Data Engineers` with Contributor access to Repos and Build
2. Restrict `DeployProd` stage to `Release Managers` group only
3. Create a service connection for Azure Resource Manager with a managed identity
4. Configure the service connection to be usable only by specific pipelines
5. Review audit logs for pipeline permission changes

## Data Engineering Tie-In
Not every data engineer should be able to deploy to production. Separation of duties is critical for compliance.

## Further Reading
- "Security in Azure DevOps" (Microsoft Learn)
- "Securing Azure DevOps for Data Teams"
```

### Day 22: Service Connections & Authentication for Data Services

```markdown
# Day 22: Service Connections & Authentication for Data Services

## Why This Matters for Data Engineering
Pipelines need to authenticate to Azure SQL, Data Lake, Databricks, Synapse, and Key Vault. Service connections manage this securely.

## Core Concepts
- Service connection types: ARM, Azure SQL, Docker, Generic, GitHub
- ARM service connection: service principal vs managed identity
- Workload identity federation (no secrets)
- Databricks service connection
- Synapse service connection
- Key Vault service connection
- Pipeline permissions on service connections

## Hands-On Lab
1. Create an ARM service connection using workload identity federation (no client secret)
2. Create a Databricks service connection using a service principal
3. Grant the service connection access to:
   - Azure SQL Database
   - Data Lake Storage
   - Key Vault
4. Use the service connection in a pipeline to deploy ADF

```yaml
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'ARM-DataPlatform-Prod'
    subscriptionId: '$(subscriptionId)'
    resourceGroupName: 'rg-adf-prod'
    location: 'East US'
    templateLocation: 'Linked artifact'
    csmFile: '$(Pipeline.Workspace)/adf-artifacts/ARMTemplateForFactory.json'
```

## Data Engineering Tie-In
Workload identity federation eliminates secret rotation for service connections — a major security improvement.

## Further Reading
- "Service connections in Azure Pipelines" (Microsoft Learn)
- "Workload Identity Federation for Azure DevOps"
```

### Day 23: Pipeline Approvals, Gates & Checks

```markdown
# Day 23: Pipeline Approvals, Gates & Checks

## Why This Matters for Data Engineering
Deploying to production data pipelines requires approval. Gates and checks automate quality and security validation before deployment.

## Core Concepts
- Environment approvals (manual approval)
- Business hours check
- Azure Monitor alerts check
- Invoke REST API check
- Required template check
- Exclusive lock check
- Branch control check
- Required pipeline check

## Hands-On Lab
1. Create an environment `adf-prod` with a manual approval
2. Add a business hours check (deploy only 9 AM – 5 PM)
3. Add an Azure Monitor alert check (no active alerts)
4. Add a REST API check that queries a data quality endpoint
5. Test a deployment with failed checks

```yaml
stages:
  - stage: DeployProd
    jobs:
      - deployment: DeployADF
        environment: 'adf-prod'  # Approvals configured on this environment
        strategy:
          runOnce:
            deploy:
              steps:
                - script: echo "Deploying to production..."
```

## Data Engineering Tie-In
Gates can check data freshness, quality metrics, or upstream pipeline status before deploying downstream changes.

## Further Reading
- "Approvals and gates" (Microsoft Learn)
- "Environment checks for Data Pipelines"
```

### Day 24: Azure Monitor Integration with Azure DevOps

```markdown
# Day 24: Azure Monitor Integration with Azure DevOps

## Why This Matters for Data Engineering
After deployment, you need to monitor pipeline health. Azure Monitor + Azure DevOps creates a feedback loop.

## Core Concepts
- Azure Monitor alerts as pipeline gates
- Application Insights for pipeline telemetry
- Log Analytics for pipeline logs
- Workbooks for data pipeline dashboards
- Azure DevOps notifications via Teams/Slack
- Pipeline failure alerts
- Integration with PagerDuty/Opsgenie

## Hands-On Lab
1. Create an Application Insights resource for a data pipeline
2. Instrument a Python ETL script to send telemetry
3. Create an Azure Monitor alert for "ETL job failed"
4. Add the alert as a gate in a deployment pipeline
5. Create an Azure DevOps notification for pipeline failures to Teams

```python
from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

# Define metric
etl_success = measure_module.MeasureInt("etl_success", "ETL success count", "count")
etl_success_view = view_module.View(
    "etl_success_view", "ETL success count",
    [], etl_success, aggregation_module.CountAggregation()
)
```

## Data Engineering Tie-In
Pipeline failures should trigger alerts in Azure Monitor, which can then gate downstream deployments.

## Further Reading
- "Azure Monitor and Azure DevOps integration" (Microsoft Learn)
- "Monitoring Data Pipelines with Azure Monitor"
```

### Day 25: Azure DevOps + Microsoft Fabric CI/CD

```markdown
# Day 25: Azure DevOps + Microsoft Fabric CI/CD

## Why This Matters for Data Engineering
Microsoft Fabric is the new unified analytics platform. CI/CD for Fabric uses Git integration and deployment pipelines, but can also be driven by Azure DevOps.

## Core Concepts
- Fabric Git integration: workspace ↔ Azure Repos
- Fabric deployment pipelines (built-in CI/CD)
- Azure DevOps for Fabric: REST API and CLI
- Deploying Fabric items: notebooks, data pipelines, semantic models
- Environment-specific configuration
- Fabric capacity and workspace management

## Hands-On Lab
1. Create a Fabric workspace and connect it to Azure Repos
2. Create a notebook and a data pipeline in Fabric
3. Commit changes to Git from Fabric
4. Create an Azure Pipeline that:
   - Validates Fabric item JSON
   - Uses the Fabric REST API to deploy to another workspace
5. Test the deployment

```yaml
steps:
  - task: AzureCLI@2
    inputs:
      azureSubscription: 'AzureServiceConnection'
      scriptType: 'bash'
      scriptLocation: 'inlineScript'
      inlineScript: |
        # Authenticate to Fabric API
        token=$(az account get-access-token --resource https://api.fabric.microsoft.com --query accessToken -o tsv)

        # Deploy notebook
        curl -X POST "https://api.fabric.microsoft.com/v1/workspaces/$(workspaceId)/items" \
          -H "Authorization: Bearer $token" \
          -H "Content-Type: application/json" \
          -d @notebook-definition.json
```

## Data Engineering Tie-In
Fabric is Microsoft's strategic data platform. Data engineers should understand both its native deployment pipelines and how to drive them from Azure DevOps.

## Further Reading
- "Microsoft Fabric CI/CD" (Microsoft Learn)
- "Fabric Git Integration and Azure DevOps"
```

### Day 26: Database DevOps with Azure SQL & Synapse

```markdown
# Day 26: Database DevOps with Azure SQL & Synapse

## Why This Matters for Data Engineering
Data warehouses have schema, stored procedures, and data. Database DevOps applies CI/CD to database changes.

## Core Concepts
- SQL Server Data Tools (SSDT) database projects
- DACPAC and BACPAC
- Schema comparison and deployment
- Pre-deployment and post-deployment scripts
- SQL linting with SQLFluff or SSDT
- Data seeding vs schema deployment
- Rollback strategies for database changes

## Hands-On Lab
1. Create a SQL database project with:
   - Tables for a star schema
   - Stored procedures for transformations
   - Pre-deployment script (drop indexes)
   - Post-deployment script (create indexes, seed reference data)
2. Build the DACPAC
3. Deploy to dev Azure SQL
4. Run schema comparison against target
5. Deploy to prod after approval

```yaml
# Build
steps:
  - task: VSBuild@1
    inputs:
      solution: '**/*.sqlproj'

  - task: CopyFiles@2
    inputs:
      contents: '**/*.dacpac'
      targetFolder: '$(Build.ArtifactStagingDirectory)'

# Deploy
steps:
  - task: SqlAzureDacpacDeployment@1
    inputs:
      azureSubscription: 'AzureServiceConnection'
      ServerName: '$(sqlServer)'
      DatabaseName: '$(databaseName)'
      SqlUsername: '$(sqlUser)'
      SqlPassword: '$(sqlPassword)'
      deployType: 'DacpacTask'
      DeploymentAction: 'Publish'
      DacpacFile: '$(Pipeline.Workspace)/sql-dacpac/**/*.dacpac'
      AdditionalArguments: '/p:BlockOnPossibleDataLoss=false'
```

## Data Engineering Tie-In
Database deployments should be as automated as application deployments. Manual SQL execution is a recipe for drift and errors.

## Further Reading
- "Database DevOps with Azure SQL" (Microsoft Learn)
- "SSDT Database Projects for Data Warehouses"
```

### Day 27: Azure DevOps for Spark on Azure (HDInsight, Synapse Spark)

```markdown
# Day 27: Azure DevOps for Spark on Azure (HDInsight, Synapse Spark)

## Why This Matters for Data Engineering
Spark jobs need CI/CD too — packaging, testing, and deployment to HDInsight or Synapse Spark pools.

## Core Concepts
- Packaging PySpark jobs: wheel, zip, JAR
- Dependency management: `--py-files`, `--jars`
- `spark-submit` from Azure Pipelines
- HDInsight cluster deployment with ARM templates
- Synapse Spark pool job definitions
- Livy API for job submission
- Spark job monitoring via Azure Monitor

## Hands-On Lab
1. Package a PySpark job as a wheel
2. Create a Synapse Spark job definition
3. Create a pipeline that:
   - Builds the wheel
   - Uploads it to ADLS Gen2
   - Submits a Spark job via the Synapse REST API
4. Monitor the job status

```yaml
steps:
  - task: UsePythonVersion@0
    inputs:
      versionSpec: '3.10'

  - script: |
      pip install -r requirements.txt
      python -m build --wheel
    displayName: 'Build PySpark wheel'

  - task: AzureCLI@2
    inputs:
      azureSubscription: 'AzureServiceConnection'
      scriptType: 'bash'
      scriptLocation: 'inlineScript'
      inlineScript: |
        az storage blob upload \
          --account-name $(storageAccount) \
          --container-name spark-artifacts \
          --file dist/*.whl \
          --name spark-etl.whl

        # Submit Spark job to Synapse
        az synapse spark job submit \
          --workspace-name $(synapseWorkspace) \
          --spark-pool-name $(sparkPool) \
          --name "etl-job" \
          --main-definition-file "abfss://spark-artifacts@$(storageAccount).dfs.core.windows.net/spark-etl.whl" \
          --language python \
          --command "spark_etl.main"
```

## Data Engineering Tie-In
Spark jobs on Synapse or HDInsight should be deployed via CI/CD, not manually submitted via the portal.

## Further Reading
- "Submit Spark jobs to Synapse" (Microsoft Learn)
- "CI/CD for PySpark Jobs on Azure"
```

### Day 28: End-to-End Data Platform Deployment Pipeline

```markdown
# Day 28: End-to-End Data Platform Deployment Pipeline

## Why This Matters for Data Engineering
This is the synthesis: a single Azure Pipeline that deploys an entire data platform — infrastructure, ADF, Databricks, SQL, and monitoring.

## Core Concepts
- Pipeline orchestration: multiple pipelines triggered in sequence
- Infrastructure first: Terraform deploys storage, ADF, Databricks, Synapse
- Then applications: ADF pipelines, Databricks wheels, SQL schemas
- Then validation: data quality tests, smoke tests
- Then monitoring: dashboards, alerts
- Rollback strategies at each layer
- Environment promotion: dev → staging → prod

## Hands-On Lab
Design and implement a multi-pipeline deployment:
1. **Infra Pipeline**: Terraform applies resource groups, storage, ADF, Databricks, Synapse
2. **ADF Pipeline**: Builds and deploys ARM templates
3. **Databricks Pipeline**: Builds wheel and deploys jobs
4. **SQL Pipeline**: Deploys DACPAC to Synapse
5. **Validation Pipeline**: Runs data quality checks
6. **Monitoring Pipeline**: Deploys Grafana dashboards and alerts

Trigger all from a single "master" pipeline with stages.

```yaml
# master-pipeline.yml
trigger:
  branches:
    include: [main]

stages:
  - stage: Terraform
    jobs:
      - job: TerraformApply
        steps:
          - template: pipelines/terraform.yml

  - stage: ADF
    dependsOn: Terraform
    jobs:
      - job: DeployADF
        steps:
          - template: pipelines/adf.yml

  - stage: Databricks
    dependsOn: Terraform
    jobs:
      - job: DeployDatabricks
        steps:
          - template: pipelines/databricks.yml

  - stage: SQL
    dependsOn: Terraform
    jobs:
      - job: DeploySQL
        steps:
          - template: pipelines/sql.yml

  - stage: Validate
    dependsOn: [ADF, Databricks, SQL]
    jobs:
      - job: RunSmokeTests
        steps:
          - template: pipelines/smoke-tests.yml
```

## Data Engineering Tie-In
This is what a production data platform deployment looks like on Azure — fully automated, staged, and validated.

## Further Reading
- "Deploying a Data Platform with Azure DevOps"
- "End-to-End Data Engineering on Azure"
```

---

## Phase 4: DataOps & Production Practices (Days 29–40)

### Day 29: Git Branching Strategy for Data Teams

```markdown
# Day 29: Git Branching Strategy for Data Teams

## Why This Matters for Data Engineering
Data pipelines have multiple developers, environments, and release cycles. The wrong branching strategy causes merge conflicts, deployment failures, and data corruption.

## Core Concepts
- Trunk-based development: short-lived branches, frequent merges
- GitFlow: feature, develop, release, hotfix branches
- GitHub Flow: feature → main
- Environment branches: `dev`, `staging`, `prod` (and why they're dangerous)
- Release branches for data pipelines
- Feature flags for data pipeline changes
- ADF Git integration branching model (adf_publish)

## Hands-On Lab
1. Choose a branching strategy for your data team
2. Document it in a `CONTRIBUTING.md` file
3. Create branch policies in Azure Repos:
   - `main`: require PR, 1 reviewer, build validation
   - `release/*`: require PR, 2 reviewers
4. Practice a feature branch → PR → merge workflow
5. Simulate a hotfix branch for a production pipeline bug

## Data Engineering Tie-In
ADF's `adf_publish` branch is generated automatically — understanding how it fits into your branching model prevents deployment confusion.

## Further Reading
- "A successful Git branching model" (nvie.com)
- "Branching Strategies for Data Teams"
```

### Day 30: Data Pipeline Testing Strategy in Azure DevOps

```markdown
# Day 30: Data Pipeline Testing Strategy in Azure DevOps

## Why This Matters for Data Engineering
Data pipelines fail silently. Testing must cover code, data, and integration.

## Core Concepts
- Test pyramid for data: unit → integration → data quality → end-to-end
- Unit tests: transform functions, schema validators
- Integration tests: pipeline run with sample data
- Data quality tests: Great Expectations, dbt tests
- Contract tests: schema compatibility
- Smoke tests: post-deployment validation
- Test data management: fixtures, seeds, anonymized data

## Hands-On Lab
Create a comprehensive test suite for a data pipeline:
1. **Unit tests**: pytest for Python transform functions
2. **Integration tests**: run a mini ADF pipeline against sample data
3. **Data quality tests**: Great Expectations for output validation
4. **Smoke tests**: query the warehouse after deployment to verify data freshness

```yaml
# Run all tests in pipeline
steps:
  - script: pytest tests/unit/ --junitxml=test-results/unit.xml
    displayName: 'Unit tests'

  - script: pytest tests/integration/ --junitxml=test-results/integration.xml
    displayName: 'Integration tests'

  - script: great_expectations checkpoint run prod_checkpoint
    displayName: 'Data quality checks'

  - task: PublishTestResults@2
    inputs:
      testResultsFormat: 'JUnit'
      testResultsFiles: 'test-results/*.xml'
```

## Data Engineering Tie-In
Testing should run in CI on every PR and in CD after every deployment.

## Further Reading
- "Testing Data Pipelines" (Data Engineering Blog)
- "Great Expectations with Azure DevOps"
```

### Day 31: Data Quality Gates in Azure Pipelines

```markdown
# Day 31: Data Quality Gates in Azure Pipelines

## Why This Matters for Data Engineering
Deploying a pipeline that produces bad data is worse than not deploying. Quality gates prevent this.

## Core Concepts
- Quality gates as pipeline stages
- Great Expectations checkpoints as gates
- dbt tests as gates
- Custom data quality scripts
- Threshold-based gates (fail if > 1% nulls)
- Alerting vs blocking gates
- Quality gate dashboards

## Hands-On Lab
1. Create a data quality gate that:
   - Runs Great Expectations on the output of a pipeline
   - Fails if any expectation fails
   - Publishes a quality report as an artifact
2. Add the gate to a deployment pipeline
3. Test: introduce a data quality issue and verify the gate blocks deployment

```yaml
stages:
  - stage: DataQuality
    jobs:
      - job: RunQualityChecks
        steps:
          - script: |
              great_expectations checkpoint run sales_pipeline_checkpoint
            displayName: 'Run data quality checks'

          - task: PublishPipelineArtifact@1
            inputs:
              targetPath: 'great_expectations/uncommitted/data_docs/'
              artifact: 'data-quality-report'
            condition: always()
```

## Data Engineering Tie-In
Quality gates should run after every pipeline execution, not just during deployment.

## Further Reading
- "Data Quality Gates in CI/CD"
- "Great Expectations Checkpoints"
```

### Day 32: Rollback Strategies for Data Pipeline Deployments

```markdown
# Day 32: Rollback Strategies for Data Pipeline Deployments

## Why This Matters for Data Engineering
Deployments fail. Data pipelines have the added complexity of state (data already processed). Rollback strategies must handle both code and data.

## Core Concepts
- Code rollback: revert Git commit, redeploy previous artifact
- Data rollback: reprocess from checkpoint, restore snapshot
- ADF rollback: redeploy previous ARM template
- Databricks rollback: revert job definition, re-run previous wheel
- SQL rollback: DACPAC rollback limitations, pre/post scripts
- Blue-green deployment for data pipelines
- Canary deployment for data transformations

## Hands-On Lab
1. Simulate a failed ADF deployment
2. Roll back by redeploying the previous ARM template
3. Simulate a bad Databricks transformation
4. Roll back by:
   - Reverting to previous wheel
   - Reprocessing the affected partition
5. Document the rollback procedure in a runbook

```yaml
# Rollback pipeline
parameters:
  - name: rollbackToCommit
    type: string

steps:
  - checkout: self
    persistCredentials: true

  - script: |
      git checkout $(rollbackToCommit)
      # Rebuild and redeploy from this commit
    displayName: 'Checkout rollback commit'

  - template: pipelines/deploy-adf.yml
```

## Data Engineering Tie-In
Idempotent pipelines make rollbacks safe. If re-running a pipeline duplicates data, rollback is impossible.

## Further Reading
- "Rollback Strategies for Data Pipelines"
- "Blue-Green Deployment for Data"
```

### Day 33: Azure DevOps + Airflow for Data Orchestration

```markdown
# Day 33: Azure DevOps + Airflow for Data Orchestration

## Why This Matters for Data Engineering
Airflow orchestrates data pipelines. Azure DevOps deploys Airflow DAGs, infrastructure, and configurations.

## Core Concepts
- Airflow deployment patterns on Azure:
  - Azure Container Instances
  - AKS with Helm
  - Azure VM
  - Managed Airflow (MWAA on AWS — not Azure, but relevant)
- DAG deployment via Azure Pipelines
- Airflow connections and variables as code
- Airflow REST API for triggering DAGs from Azure Pipelines
- Azure Pipelines triggered by Airflow via webhooks
- Monitoring Airflow with Azure Monitor

## Hands-On Lab
1. Deploy Airflow on AKS using Helm
2. Create a DAG that runs an ADF pipeline
3. Create an Azure Pipeline that:
   - Tests DAGs
   - Deploys DAGs to Azure Blob (synced to Airflow)
   - Triggers a DAG run via Airflow REST API
4. Configure Airflow to trigger Azure Pipelines when a DAG fails

```yaml
# Azure Pipeline triggers Airflow DAG
steps:
  - script: |
      curl -X POST "https://airflow.example.com/api/v1/dags/etl_pipeline/dagRuns" \
        -H "Content-Type: application/json" \
        -u "$(airflowUser):$(airflowPassword)" \
        -d '{"conf": {"env": "prod"}}'
    displayName: 'Trigger Airflow DAG'
```

```python
# Airflow DAG triggers Azure Pipeline
from airflow.providers.microsoft.azure.hooks.azure_devops import AzureDevOpsHook

def trigger_azure_pipeline(**context):
    hook = AzureDevOpsHook(azure_devops_conn_id='azure_devops')
    hook.run_pipeline(project='data-platform', pipeline_id=42)
```

## Data Engineering Tie-In
The combination of Airflow (orchestration) + Azure DevOps (deployment) is common in enterprises migrating from on-prem to Azure.

## Further Reading
- "Airflow on Azure with Azure DevOps"
- "Triggering Azure Pipelines from Airflow"
```

### Day 34: Azure DevOps + dbt for SQL Transformations

```markdown
# Day 34: Azure DevOps + dbt for SQL Transformations

## Why This Matters for Data Engineering
dbt is the standard for SQL transformations. Azure DevOps provides CI/CD for dbt projects.

## Core Concepts
- dbt project structure for Azure (Synapse, Databricks SQL, Snowflake)
- dbt profiles for Azure authentication
- CI/CD for dbt: `dbt build`, slim CI, state comparison
- dbt Cloud vs dbt Core with Azure DevOps
- dbt artifacts: `manifest.json`, `run_results.json`, `catalog.json`
- dbt docs generation and hosting
- Testing: schema tests, custom tests, source freshness

## Hands-On Lab
1. Create a dbt project targeting Synapse
2. Configure `profiles.yml` for dev and prod
3. Create a PR pipeline:
   - Install dbt
   - Run `dbt build --select state:modified+ --defer --state ./prod-manifest`
   - Publish `manifest.json` as artifact
4. Create a deploy pipeline:
   - Run full `dbt build` in prod
   - Generate and publish dbt docs

```yaml
# PR validation
steps:
  - task: UsePythonVersion@0
    inputs:
      versionSpec: '3.10'

  - script: |
      pip install dbt-synapse
      dbt deps
      dbt build --select state:modified+ --defer --state ./prod-manifest --target dev
    displayName: 'dbt Slim CI'

  - task: PublishPipelineArtifact@1
    inputs:
      targetPath: 'target/manifest.json'
      artifact: 'dbt-manifest'

# Production deployment
steps:
  - script: |
      dbt deps
      dbt build --target prod
      dbt docs generate
    displayName: 'dbt Production Build'

  - task: PublishPipelineArtifact@1
    inputs:
      targetPath: 'target/'
      artifact: 'dbt-docs'
```

## Data Engineering Tie-In
dbt + Azure DevOps is the standard transformation layer for modern Azure data platforms.

## Further Reading
- "dbt with Azure DevOps" (dbt Docs)
- "dbt on Synapse Best Practices"
```

### Day 35: Azure DevOps + Databricks Asset Bundles (DABs)

```markdown
# Day 35: Azure DevOps + Databricks Asset Bundles (DABs)

## Why This Matters for Data Engineering
Databricks Asset Bundles (DABs) are the new standard for deploying Databricks resources (jobs, pipelines, notebooks) as code.

## Core Concepts
- DAB project structure: `databricks.yml`, resources, variables
- Bundles vs Databricks CLI (legacy)
- Deployment targets: dev, staging, prod
- CI/CD with DABs: `databricks bundle validate`, `databricks bundle deploy`
- Environment-specific variables
- Bundle artifacts: wheels, notebooks
- Integration with Azure DevOps pipelines

## Hands-On Lab
1. Create a DAB project:
   ```
   my-data-pipeline/
   ├── databricks.yml
   ├── src/
   │   └── etl.py
   └── resources/
       └── etl_job.yml
   ```
2. Define a job resource
3. Create an Azure Pipeline that:
   - Validates the bundle
   - Deploys to dev
   - Requires approval for prod
   - Deploys to prod

```yaml
# databricks.yml
bundle:
  name: my-data-pipeline

workspace:
  host: $(databricksHost)

resources:
  jobs:
    etl_job:
      name: "ETL Job"
      tasks:
        - task_key: "transform"
          python_wheel_task:
            package_name: "etl"
            entry_point: "main"
          libraries:
            - whl: ./dist/*.whl

targets:
  dev:
    workspace:
      host: $(databricksHostDev)
  prod:
    workspace:
      host: $(databricksHostProd)
```

```yaml
# Azure Pipeline
steps:
  - script: |
      databricks bundle validate --target $(env)
      databricks bundle deploy --target $(env)
    displayName: 'Deploy Databricks Bundle'
    env:
      DATABRICKS_HOST: $(databricksHost)
      DATABRICKS_TOKEN: $(databricksToken)
```

## Data Engineering Tie-In
DABs replace manual notebook deployment and job creation with declarative, versioned configuration.

## Further Reading
- "Databricks Asset Bundles" (Databricks Docs)
- "CI/CD with DABs and Azure DevOps"
```

### Day 36: Azure DevOps + Azure Functions for Event-Driven Data Pipelines

```markdown
# Day 36: Azure DevOps + Azure Functions for Event-Driven Data Pipelines

## Why This Matters for Data Engineering
Event-driven data pipelines (file arrival, message queue) use Azure Functions. CI/CD ensures functions are tested and deployed reliably.

## Core Concepts
- Azure Functions: triggers, bindings, runtime
- Function deployment: zip deploy, container, slot
- CI/CD for Functions: build, test, deploy
- Function slots (staging → swap to prod)
- Application Insights integration
- Configuration management (app settings, Key Vault references)

## Hands-On Lab
1. Create a Python Azure Function triggered by Blob Storage
2. Write unit tests for the function logic
3. Create a pipeline that:
   - Builds the function
   - Runs tests
   - Deploys to a staging slot
   - Runs smoke tests
   - Swaps staging → prod
4. Monitor function invocations in Application Insights

```yaml
steps:
  - task: UsePythonVersion@0
    inputs:
      versionSpec: '3.10'

  - script: |
      pip install -r requirements.txt
      pytest tests/
    displayName: 'Test function'

  - task: ArchiveFiles@2
    inputs:
      rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
      archiveFile: '$(Build.ArtifactStagingDirectory)/function.zip'

  - task: AzureFunctionApp@1
    inputs:
      azureSubscription: 'AzureServiceConnection'
      appType: 'functionAppLinux'
      appName: '$(functionAppName)'
      package: '$(Build.ArtifactStagingDirectory)/function.zip'
      deploymentMethod: 'zipDeploy'
      slotName: 'staging'
```

## Data Engineering Tie-In
Event-driven pipelines (file arrival → process → notify) are common for real-time data ingestion.

## Further Reading
- "Azure Functions CI/CD" (Microsoft Learn)
- "Event-Driven Data Pipelines on Azure"
```

### Day 37: Azure DevOps + Azure Event Hubs / Kafka CI/CD

```markdown
# Day 37: Azure DevOps + Azure Event Hubs / Kafka CI/CD

## Why This Matters for Data Engineering
Streaming data pipelines use Event Hubs or Kafka. CI/CD ensures producer and consumer code is deployed consistently.

## Core Concepts
- Event Hubs vs Kafka (protocol compatibility)
- Producer/consumer code packaging
- Schema Registry integration
- CI/CD for streaming apps (containers, Functions, Databricks)
- Consumer group management
- Monitoring consumer lag
- Disaster recovery and geo-replication

## Hands-On Lab
1. Create an Event Hub namespace and hub
2. Write a Python producer and consumer
3. Containerize both applications
4. Create a pipeline that:
   - Builds and tests both containers
   - Pushes to Azure Container Registry
   - Deploys to AKS or Container Apps
5. Monitor consumer lag in Azure Monitor

```yaml
steps:
  - task: Docker@2
    inputs:
      containerRegistry: 'ACR-ServiceConnection'
      repository: 'eventhub-producer'
      command: 'buildAndPush'
      Dockerfile: 'producer/Dockerfile'
      tags: |
        $(Build.BuildId)
        latest

  - task: KubernetesManifest@0
    inputs:
      action: 'deploy'
      kubernetesServiceConnection: 'AKS-ServiceConnection'
      manifests: 'k8s/producer-deployment.yaml'
      containers: '$(containerRegistry)/eventhub-producer:$(Build.BuildId)'
```

## Data Engineering Tie-In
Streaming pipelines require the same CI/CD rigor as batch pipelines, with additional considerations for schema evolution and consumer lag.

## Further Reading
- "Event Hubs with Azure DevOps"
- "Streaming Data Pipelines on Azure"
```

### Day 38: Azure DevOps + Azure Container Registry (ACR) Integration

```markdown
# Day 38: Azure DevOps + Azure Container Registry (ACR) Integration

## Why This Matters for Data Engineering
Data engineering workloads are increasingly containerized (Spark, Airflow, custom ETL). ACR stores these images, and Azure Pipelines builds and pushes them.

## Core Concepts
- ACR vs Docker Hub vs GitHub Container Registry
- ACR tasks: automated builds on commit
- Image tagging strategies: `latest`, semantic versioning, Git SHA
- Image scanning with Defender for Containers
- ACR geo-replication
- Pipeline integration: Docker@2 task, ACR service connection
- Pull secrets in Kubernetes

## Hands-On Lab
1. Create an ACR instance
2. Write a Dockerfile for a PySpark job
3. Create a pipeline that:
   - Builds the image
   - Scans for vulnerabilities
   - Pushes to ACR with tags: `latest`, `v1.0.0`, `$(Build.BuildId)`
4. Configure AKS to pull from ACR using a managed identity

```yaml
steps:
  - task: Docker@2
    inputs:
      containerRegistry: 'ACR-ServiceConnection'
      repository: 'spark-etl'
      command: 'buildAndPush'
      Dockerfile: 'Dockerfile'
      tags: |
        latest
        $(Build.BuildId)
        $(Build.SourceVersion)

  - task: AzureCLI@2
    inputs:
      azureSubscription: 'AzureServiceConnection'
      scriptType: 'bash'
      scriptLocation: 'inlineScript'
      inlineScript: |
        az acr scan --registry $(acrName) --image spark-etl:$(Build.BuildId)
```

## Data Engineering Tie-In
Containerized Spark jobs are portable across AKS, Azure Container Apps, and Databricks.

## Further Reading
- "Azure Container Registry with Azure Pipelines"
- "Containerizing Data Engineering Workloads"
```

### Day 39: Azure DevOps + Azure Kubernetes Service (AKS) for Data Platforms

```markdown
# Day 39: Azure DevOps + Azure Kubernetes Service (AKS) for Data Platforms

## Why This Matters for Data Engineering
AKS hosts Airflow, Spark, Kafka, and custom data services. CI/CD deploys to AKS via Helm or kubectl.

## Core Concepts
- AKS cluster provisioning with Terraform or Bicep
- Helm charts for data platforms (Airflow, Spark Operator, Kafka)
- CI/CD: build image → push to ACR → deploy to AKS
- GitOps with ArgoCD or Flux on AKS
- AKS monitoring with Azure Monitor Container Insights
- Autoscaling: HPA, KEDA, cluster autoscaler
- Workload identity for pod-level Azure access

## Hands-On Lab
1. Create an AKS cluster with Terraform
2. Deploy Airflow using Helm
3. Create a pipeline that:
   - Builds a custom Airflow image
   - Pushes to ACR
   - Updates the Helm release with the new image tag
4. Configure KEDA to scale Airflow workers based on queue depth

```yaml
steps:
  - task: HelmDeploy@0
    inputs:
      connectionType: 'Kubernetes Service Connection'
      kubernetesServiceConnection: 'AKS-ServiceConnection'
      namespace: 'airflow'
      command: 'upgrade'
      chartType: 'FilePath'
      chartPath: 'charts/airflow'
      releaseName: 'airflow'
      valueFile: 'charts/airflow/values-prod.yaml'
      overrideValues: |
        images.airflow.repository=$(acrName).azurecr.io/airflow
        images.airflow.tag=$(Build.BuildId)
```

## Data Engineering Tie-In
AKS is the most flexible platform for data workloads on Azure — it can host Airflow, Spark, Kafka, and custom services.

## Further Reading
- "AKS with Azure DevOps" (Microsoft Learn)
- "Data Platforms on AKS"
```

### Day 40: Cost Management & Optimization for Data Pipelines

```markdown
# Day 40: Cost Management & Optimization for Data Pipelines

## Why This Matters for Data Engineering
Data pipelines consume significant compute and storage. DevOps practices like autoscaling, spot instances, and lifecycle policies reduce cost.

## Core Concepts
- Azure cost drivers for data: compute (Databricks, Synapse), storage (ADLS), data transfer
- Azure Cost Management and budgets
- Tagging strategy for cost allocation
- Reserved instances and savings plans
- Spot instances for non-critical jobs
- Autoscaling: Databricks autoscaling, AKS cluster autoscaler, KEDA
- Storage lifecycle policies (hot → cool → archive)
- Pipeline cost optimization: caching, parallel jobs, self-hosted agents

## Hands-On Lab
1. Analyze the cost of a Databricks job: cluster size, runtime, DBUs
2. Switch to spot instances for a non-critical job
3. Add a lifecycle policy to move old data to cool storage
4. Set up a cost budget alert in Azure Cost Management
5. Optimize a pipeline by adding caching (see Day 18)

## Data Engineering Tie-In
A 50% cost reduction often comes from better partitioning and autoscaling, not from cutting corners on reliability.

## Further Reading
- "Azure Cost Management for Data Engineering"
- "Optimizing Databricks Costs"
```

---

## Phase 5: Capstone & Advanced Topics (Days 41–50)

### Day 41: Azure DevOps + Microsoft Purview for Data Governance

```markdown
# Day 41: Azure DevOps + Microsoft Purview for Data Governance

## Why This Matters for Data Engineering
Data governance (lineage, classification, access control) is increasingly required. Purview integrates with ADF, Synapse, and Databricks.

## Core Concepts
- Purview: data map, data catalog, data estate
- Scanning Azure data sources (ADLS, Synapse, SQL)
- Lineage from ADF and Synapse
- Data classification (PII, financial)
- Access policies
- Integration with Azure DevOps: deployment of Purview configurations
- Compliance: GDPR, HIPAA

## Hands-On Lab
1. Create a Purview account
2. Register and scan an ADLS Gen2 storage account
3. View lineage from ADF pipelines
4. Classify PII columns
5. Create a pipeline that deploys Purview configurations (collections, scans) via REST API

```yaml
steps:
  - task: AzureCLI@2
    inputs:
      azureSubscription: 'AzureServiceConnection'
      scriptType: 'bash'
      scriptLocation: 'inlineScript'
      inlineScript: |
        # Create Purview scan
        az purview scan create \
          --account-name $(purviewAccount) \
          --data-source-name $(dataSourceName) \
          --scan-name "daily-scan" \
          --kind AzureStorage
```

## Data Engineering Tie-In
Governance should be automated, not manual. Deploying Purview configurations via CI/CD ensures consistency.

## Further Reading
- "Microsoft Purview" (Microsoft Learn)
- "Data Governance with Azure DevOps"
```

### Day 42: Azure DevOps + Azure Policy for Data Platform Compliance

```markdown
# Day 42: Azure DevOps + Azure Policy for Data Platform Compliance

## Why This Matters for Data Engineering
Azure Policy enforces compliance (encryption, tagging, allowed regions). CI/CD deploys policies as code.

## Core Concepts
- Policy definitions and assignments
- Policy initiatives (groups of policies)
- Enforcement modes: audit, deny, deployIfNotExists
- Policy as code: Bicep, Terraform, ARM
- Remediation tasks
- Compliance reporting
- CI/CD for policy deployment

## Hands-On Lab
1. Create a policy that requires encryption on storage accounts
2. Create a policy that requires a `cost-center` tag
3. Assign policies at the subscription level
4. Create a pipeline that deploys policies using Bicep
5. Test: create a non-compliant storage account and verify denial

```bicep
// policy.bicep
resource storageEncryption 'Microsoft.Authorization/policyAssignments@2022-06-01' = {
  name: 'require-storage-encryption'
  properties: {
    policyDefinitionId: '/providers/Microsoft.Authorization/policyDefinitions/require-storage-encryption'
    scope: subscription().id
    enforcementMode: 'Deny'
  }
}
```

```yaml
steps:
  - task: AzureCLI@2
    inputs:
      azureSubscription: 'AzureServiceConnection'
      scriptType: 'bash'
      scriptLocation: 'inlineScript'
      inlineScript: |
        az deployment sub create \
          --location eastus \
          --template-file policy.bicep
```

## Data Engineering Tie-In
Policies ensure every data lake, warehouse, and pipeline meets security and compliance requirements.

## Further Reading
- "Azure Policy as Code" (Microsoft Learn)
- "Compliance for Data Platforms"
```

### Day 43: Azure DevOps + Azure Monitor & Log Analytics for Data Pipelines

```markdown
# Day 43: Azure DevOps + Azure Monitor & Log Analytics for Data Pipelines

## Why This Matters for Data Engineering
Monitoring data pipelines requires more than pipeline status. You need data freshness, volume, latency, and quality metrics.

## Core Concepts
- Azure Monitor: metrics, alerts, action groups
- Log Analytics: KQL queries, workbooks
- Application Insights for custom telemetry
- Data pipeline SLIs: freshness, latency, completeness, error rate
- Alerts for ADF, Databricks, Synapse
- Dashboards with Azure Workbooks
- Integration with Azure DevOps: alerts as pipeline gates

## Hands-On Lab
1. Create a Log Analytics workspace
2. Instrument an ADF pipeline to send custom metrics
3. Write KQL queries for:
   - Pipeline run success rate
   - Average duration
   - Data freshness
4. Create an Azure Monitor alert for "pipeline failure"
5. Create an Azure Workbook dashboard

```kusto
// KQL: Pipeline success rate
ADFPipelineRun
| where TimeGenerated > ago(24h)
| summarize
    TotalRuns = count(),
    SuccessfulRuns = countif(Status == "Succeeded")
| extend SuccessRate = SuccessfulRuns * 100.0 / TotalRuns
```

## Data Engineering Tie-In
Monitoring should be deployed via CI/CD, just like infrastructure and pipelines.

## Further Reading
- "Monitoring Data Pipelines with Azure Monitor"
- "KQL for Data Engineers"
```

### Day 44: Azure DevOps + Azure Logic Apps for Pipeline Orchestration

```markdown
# Day 44: Azure DevOps + Azure Logic Apps for Pipeline Orchestration

## Why This Matters for Data Engineering
Logic Apps are used for lightweight orchestration: triggering pipelines, sending notifications, and integrating SaaS tools.

## Core Concepts
- Logic Apps: triggers, actions, connectors
- Consumption vs Standard
- Integration with ADF, Functions, Event Grid
- CI/CD for Logic Apps: ARM templates, Bicep
- Monitoring Logic Apps runs
- Error handling and retry policies

## Hands-On Lab
1. Create a Logic App that:
   - Triggers on a new file in Blob Storage
   - Calls an ADF pipeline
   - Sends a Teams notification on completion
2. Export the Logic App as an ARM template
3. Create a pipeline that deploys the Logic App to dev and prod

```yaml
steps:
  - task: AzureResourceManagerTemplateDeployment@3
    inputs:
      deploymentScope: 'Resource Group'
      azureResourceManagerConnection: 'AzureServiceConnection'
      subscriptionId: '$(subscriptionId)'
      resourceGroupName: 'rg-logicapps-$(env)'
      location: 'East US'
      templateLocation: 'Linked artifact'
      csmFile: '$(Pipeline.Workspace)/logicapp/LogicApp.json'
      csmParametersFile: '$(Pipeline.Workspace)/logicapp/LogicApp.parameters.json'
      overrideParameters: '-workflowName "data-pipeline-$(env)"'
```

## Data Engineering Tie-In
Logic Apps are the "glue" for event-driven data pipelines — connecting SaaS sources, storage, and orchestration.

## Further Reading
- "Logic Apps CI/CD" (Microsoft Learn)
- "Event-Driven Data Pipelines with Logic Apps"
```

### Day 45: Azure DevOps + Azure API Management for Data APIs

```markdown
# Day 45: Azure DevOps + Azure API Management for Data APIs

## Why This Matters for Data Engineering
Data APIs expose curated data to consumers. APIM provides management, security, and monitoring.

## Core Concepts
- APIM: APIs, products, subscriptions
- Policies: rate limiting, authentication, transformation
- Backends: Functions, Logic Apps, ADF
- CI/CD for APIM: ARM templates, Bicep, APIM DevOps Resource Kit
- Monitoring: Application Insights, Azure Monitor
- Versioning and revisions

## Hands-On Lab
1. Create an APIM instance
2. Import a data API (e.g., Function App)
3. Add a rate-limiting policy
4. Create a pipeline that deploys API definitions and policies via Bicep
5. Monitor API usage in Azure Monitor

```yaml
steps:
  - task: AzureCLI@2
    inputs:
      azureSubscription: 'AzureServiceConnection'
      scriptType: 'bash'
      scriptLocation: 'inlineScript'
      inlineScript: |
        az deployment group create \
          --resource-group $(resourceGroup) \
          --template-file apim.bicep \
          --parameters apimInstanceName=$(apimName)
```

## Data Engineering Tie-In
Data APIs are the "serving layer" of a data platform. CI/CD ensures they are versioned and deployed safely.

## Further Reading
- "APIM DevOps" (Microsoft Learn)
- "Data APIs on Azure"
```

### Day 46: Azure DevOps + Azure Data Share & External Data Collaboration

```markdown
# Day 46: Azure DevOps + Azure Data Share & External Data Collaboration

## Why This Matters for Data Engineering
Data engineers share data with partners and internal teams. Azure Data Share and external tables enable governed data collaboration.

## Core Concepts
- Azure Data Share: share data from ADLS, SQL, Synapse
- External tables in Synapse/ Databricks
- Data sharing governance
- CI/CD for Data Share: ARM templates, REST API
- Access control and monitoring
- Delta Sharing (Databricks)

## Hands-On Lab
1. Create a Data Share and share a dataset with another account
2. Create a pipeline that deploys Data Share configurations via ARM templates
3. Monitor share status and access
4. Create external tables in Synapse pointing to shared data

## Data Engineering Tie-In
Data sharing is increasingly required for B2B data collaboration. Automating it via CI/CD ensures consistency.

## Further Reading
- "Azure Data Share" (Microsoft Learn)
- "Delta Sharing on Azure"
```

### Day 47: Azure DevOps + Azure Arc for Hybrid Data Pipelines

```markdown
# Day 47: Azure DevOps + Azure Arc for Hybrid Data Pipelines

## Why This Matters for Data Engineering
Many enterprises have on-prem data sources (SQL Server, Hadoop, file shares). Azure Arc enables hybrid data pipelines.

## Core Concepts
- Azure Arc: Arc-enabled servers, Kubernetes, data services
- Arc-enabled SQL Server
- Arc-enabled data services (PostgreSQL, SQL Managed Instance)
- CI/CD for Arc: deploying configurations via GitOps
- Monitoring Arc resources
- Hybrid data pipelines: on-prem → Azure

## Hands-On Lab
1. Connect an on-prem VM to Azure Arc
2. Deploy an Arc-enabled PostgreSQL instance
3. Create a pipeline that deploys Arc configurations
4. Monitor Arc resources in Azure Monitor

## Data Engineering Tie-In
Arc enables "lift and shift" of data services to Azure management while keeping data on-prem.

## Further Reading
- "Azure Arc for Data Services" (Microsoft Learn)
- "Hybrid Data Pipelines with Arc"
```

### Day 48: Azure DevOps + Azure Synapse Link for Near-Real-Time Analytics

```markdown
# Day 48: Azure DevOps + Azure Synapse Link for Near-Real-Time Analytics

## Why This Matters for Data Engineering
Synapse Link enables near-real-time analytics on operational data (Cosmos DB, SQL, Dataverse) without ETL.

## Core Concepts
- Synapse Link for Cosmos DB
- Synapse Link for SQL Server 2022
- Synapse Link for Dataverse
- Analytical store vs row store
- CI/CD for Synapse Link: ARM templates, REST API
- Monitoring Synapse Link replication
- Querying linked data with Synapse SQL and Spark

## Hands-On Lab
1. Enable Synapse Link on a Cosmos DB container
2. Query the analytical store with Synapse SQL
3. Create a pipeline that deploys Synapse Link configurations
4. Monitor replication latency

## Data Engineering Tie-In
Synapse Link eliminates ETL for operational analytics, but requires CI/CD for configuration management.

## Further Reading
- "Azure Synapse Link" (Microsoft Learn)
- "Near-Real-Time Analytics on Azure"
```

### Day 49: Capstone — Design an End-to-End Data Platform with Azure DevOps

```markdown
# Day 49: Capstone — Design an End-to-End Data Platform with Azure DevOps

## Why This Matters for Data Engineering
This is the synthesis: design a complete Azure data platform with Azure DevOps as the CI/CD backbone.

## Core Concepts
- Architecture: ingestion → storage → processing → serving → monitoring
- Technology choices: ADF, Databricks, Synapse, dbt, Airflow, AKS
- CI/CD: Azure Pipelines, Terraform, Helm, DABs, DACPAC
- Security: Key Vault, managed identities, Azure Policy
- Governance: Purview, lineage, data quality
- Monitoring: Azure Monitor, Log Analytics, Grafana

## Hands-On Lab
Design a data platform for a fictional e-commerce company:
1. **Ingestion**: ADF for batch, Event Hubs for streaming, Logic Apps for SaaS
2. **Storage**: ADLS Gen2 with medallion architecture (bronze/silver/gold)
3. **Processing**: Databricks for Spark, dbt for SQL transformations
4. **Serving**: Synapse SQL pool, Cosmos DB, API Management
5. **Orchestration**: Airflow on AKS
6. **CI/CD**: Azure Pipelines for all components
7. **IaC**: Terraform for infrastructure
8. **Monitoring**: Azure Monitor, Log Analytics, Grafana
9. **Governance**: Purview for lineage and classification
10. **Security**: Key Vault, managed identities, Azure Policy

Document:
- Architecture diagram (Mermaid or draw.io)
- Repository structure and branching strategy
- Pipeline design for each component
- Environment promotion strategy
- Rollback procedures
- Cost estimate and optimization plan

## Data Engineering Tie-In
This architecture is the synthesis of every topic in this 50-day plan.

## Further Reading
- "Azure Data Platform Architecture" (Microsoft Learn)
- "Designing Data-Intensive Applications" (Kleppmann)
```

### Day 50: Capstone — Build & Deploy the Data Platform

```markdown
# Day 50: Capstone — Build & Deploy the Data Platform

## Why This Matters for Data Engineering
This is your final project. You will build, test, deploy, and monitor a complete data platform using Azure DevOps.

## Project Requirements
Build a data platform that:
1. **Ingests** data from an API (e.g., weather, crypto) via ADF or Logic Apps
2. **Stores** raw data in ADLS Gen2 (bronze layer)
3. **Transforms** with Databricks (silver layer) and dbt on Synapse (gold layer)
4. **Orchestrates** with Airflow on AKS
5. **Serves** data via Synapse SQL pool and API Management
6. **CI/CD** via Azure Pipelines:
   - Terraform for infrastructure
   - ADF ARM templates
   - Databricks wheels and DABs
   - dbt models
   - Airflow DAGs
7. **Tests**:
   - Unit tests (pytest)
   - Data quality tests (Great Expectations)
   - Integration tests (ADF pipeline runs)
8. **Monitors**:
   - Azure Monitor for pipeline health
   - Log Analytics for logs
   - Grafana for dashboards
9. **Governs**:
   - Purview for lineage
   - Azure Policy for compliance
10. **Security**:
    - Key Vault for secrets
    - Managed identities for service connections
    - Private endpoints for data services

## Step-by-Step Plan
1. **Week 1**: Terraform infrastructure + Azure Repos setup
2. **Week 2**: ADF ingestion + Databricks transformation + dbt models
3. **Week 3**: Airflow on AKS + Synapse serving layer
4. **Week 4**: CI/CD pipelines for all components + testing
5. **Week 5**: Monitoring, governance, security hardening
6. **Final**: Write a detailed README with architecture, setup instructions, and lessons learned

## Deliverables
- Git repository with all code and infrastructure
- Architecture diagram
- Azure Pipelines for each component
- Grafana/Log Analytics dashboard showing pipeline health
- README with setup instructions and a post-mortem of issues encountered

## Data Engineering Tie-In
This project demonstrates that you can deploy, automate, monitor, and troubleshoot a complete Azure data platform using Azure DevOps — the mark of a senior data engineer.

## Further Reading
- "Azure Data Engineering Projects" (GitHub)
- "How to Build a Data Platform on Azure"
```

---

## How to Use This Plan

1. **One topic per day**: Create a markdown file for each day (e.g., `day-01-azure-devops-overview.md`).
2. **Add your own notes**: After completing the hands-on lab, append your learnings, commands, and "gotchas" to the file.
3. **Link related topics**: Use `[[wikilinks]]` or relative links to connect Day 10 (ADF CI/CD) to Day 28 (End-to-End Deployment), for example.
4. **Review weekly**: At the end of each phase, review the markdown files and consolidate key learnings into a "cheat sheet."
5. **Build the capstone**: Days 49–50 are the synthesis. Don't skip them — they're where everything comes together.
6. **Adapt to your environment**: If your company uses Azure DevOps Server (on-prem) instead of Services, note the differences (e.g., no hosted agents, different service connection types).

This plan is specifically built around **Azure DevOps (the product)** and its five services — Boards, Repos, Pipelines, Test Plans, and Artifacts — with every topic tied to a data engineering workflow on Azure.
