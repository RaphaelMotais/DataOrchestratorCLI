<p align="center">
 <a href="https://circleci.com/gh/scaleway/scaleway-cli/tree/v2"><img src="https://circleci.com/gh/scaleway/scaleway-cli/tree/v2.svg?style=shield" alt="CircleCI" /></a>
 <a href="https://goreportcard.com/report/github.com/scaleway/scaleway-cli"><img src="https://goreportcard.com/badge/scaleway/scaleway-cli" alt="GoReportCard" /></a> <!-- GoReportCard do not support branches. -->
</p>

# Data Orchestrator CLI

The Data Orchestrator CLI is a command-line tool to manage and interact with Scaleway's Data Orchestrator platform directly from your terminal. It enables developers and operators to define, deploy, and monitor data workflows with ease.

> ⚠️ **Private Beta** 
> This project is currently in private beta. Commands and APIs may change before general availability.

---

## 📦 Installation

A command-line interface (CLI) tool for interacting with Scaleway's Data Orchestrator API. This tool allows you to manage workflows, definitions, and executions directly from your terminal.

> ⚠️ **Private Beta** 
> This project is currently in private beta. Commands and APIs may change before general availability.

---

## 🚀 Getting Started

### Prerequisites

- [Go](https://go.dev/dl/) (version 1.21 or higher)
- A Scaleway [API Access Key](https://console.scaleway.com/credentials)
- Your Scaleway **Project ID**
- Your Scaleway **Organisation ID**

### Download your CLI binary

Download the latest release for your platform:

**Latest version (v1.0.4):**

- [Linux AMD64](https://github.com/RaphaelMotais/DataOrchestratorCLI/releases/download/1.0.0.2/scw-do-linux)
- [macOS ARM64 (M1/M2)](https://github.com/RaphaelMotais/DataOrchestratorCLI/releases/download/1.0.0.2/scw-do-macos-arm64)
- [macOS AMD64 (Intel)](https://github.com/RaphaelMotais/DataOrchestratorCLI/releases/download/1.0.0.2/scw-do-macos-amd64)
- [Windows AMD64](https://github.com/RaphaelMotais/DataOrchestratorCLI/releases/download/1.0.0.2/scw-do-windows-amd64.exe)

> 💡 **Note**: These links point to the latest successful build artifacts. For permanent links, go to the [Releases page](https://gitlab.infra.online.net/products/wofl/src/data-orchestrator-cli/-/releases) and download from there.

### Or build from source

```bash
git clone git@gitlab.infra.online.net:products/wofl/src/data-orchestrator-cli.git
cd data-orchestrator-cli

And build directly:
```bash
go build -o scw-do ./cmd/scw
```

### Configuration
Set your Scaleway credentials via environment variables:
```bash
export SCW_ACCESS_KEY="your_access_key"
export SCW_SECRET_KEY="your_secret_key"
export SCW_DEFAULT_ORGANIZATION_ID="you_organization_id"
export SCW_API_URL="https://agw.stg.fr-par.internal.scaleway.com" (for internal staging)
export SCW_API_URL="https://agw.fr-par.internal.scaleway.com" (for internal production)
export SCW_API_URL="https://api.scaleway.com" (for external production)
```

### Worflow Definition example
```bash
document:
  dsl: 1.0.3
  namespace: examples
  name: example-workflow
  version: 1.0.0
do:
  - task0:
        set:
          color1: red
          color2: green
          color3: blue
  - task1:
      call: serverless_job
      with:
        id: 0dbc8c9f-e6f2-4dd5-88e0-a4b7a4743f02
        region: nl-ams
  - taskTry:
      try:
        - try_task__fail:
            call: serverless_job
            with:
              id: daa3ea4e-97f3-4e5c-8fbc-66e438c979b9
              region: nl-ams
      catch:
        errors:
          with:
            status: 500
        as: try_task__fail_error
        do:
          - task3:
              set:
                color1: red
                color2: green
                color3: blue
                try_task__fail_error: "${ .try_task__fail_error }"
```

### Commands
#### Create a Workflow Definition

##### In Yaml
```bash
./scw-do data-orchestrator definition create region=fr-par name="test-raph-001" project-id="264f6ba9-9858-4b7d-a5c8-8c01cc91b105" version-name="v1-0-0" yaml-content=@test.yaml
```

##### With cron schedule
```bash
./scw-do data-orchestrator definition create region=fr-par name="test-raph-001" project-id="264f6ba9-9858-4b7d-a5c8-8c01cc91b105" version-name="v1-0-0" yaml-content=@test.yaml cron-schedule="0 0 * * *" cron-timezone="Europe/Paris"
```

#### Update a Workflow Definition
```bash
./scw-do data-orchestrator definition update workflow-definition-id=8bf15625-dbda-4dd0-a229-360b917e27e7 yaml-content=@test.yaml region=fr-par
```

#### Update a Workflow Definition with cron schedule
```bash
./scw-do data-orchestrator definition update workflow-definition-id=8bf15625-dbda-4dd0-a229-360b917e27e7 name="super-workflow" cron-schedule="1 0 * * *" cron-timezone="Europe/Paris" overlap-policy=buffer_one region=fr-par
```

#### Delete a Workflow Definition
```bash
./scw-do data-orchestrator definition delete region=fr-par workflow-definition-id=90cc484b-5cc9-4cc5-8996-e1f96911b3cb
```

#### List Workflows
```bash
./scw-do data-orchestrator definition list region=fr-par
```

#### Get a Specific Workflow
```bash
./scw-do data-orchestrator definition get workflow-definition-id=6684158a-733b-4a57-9750-41877c68bbe7 region=fr-par
```

#### Get a Specific Workflow DSL
```bash
./scw-do data-orchestrator definition get-dsl workflow-definition-id=6730a041-b867-4089-b3d6-c7e5cff77a81 region=fr-par
```

#### Start a Workflow
```bash
./scw-do data-orchestrator definition start region=fr-par workflow-definition-id=c4964e39-394b-42ba-a095-51a1406f7ffa
```

#### Pause a Workflow
```bash
./scw-do data-orchestrator run pause region=fr-par project-id=264f6ba9-9858-4b7d-a5c8-8c01cc91b105 workflow-run-id=c4964e39-394b-42ba-a095-51a1406f7ffa
```

#### Resume a Workflow
```bash
./scw-do data-orchestrator run resume region=fr-par project-id=264f6ba9-9858-4b7d-a5c8-8c01cc91b105 workflow-run-id=c4964e39-394b-42ba-a095-51a1406f7ffa
```

#### Terminate a Workflow
```bash
./scw-do data-orchestrator run terminate region=fr-par project-id=264f6ba9-9858-4b7d-a5c8-8c01cc91b105 workflow-run-id=c4964e39-394b-42ba-a095-51a1406f7ffa
```

#### Delete a Workflow Run
```bash
./scw-do data-orchestrator run delete region=fr-par project-id=264f6ba9-9858-4b7d-a5c8-8c01cc91b105 workflow-run-id=c4964e39-394b-42ba-a095-51a1406f7ffa
```

#### Cleanup Old Workflow Runs
Delete all workflow runs older than a specified duration:
```bash
./scw-do data-orchestrator run cleanup region=fr-par older-than=30d project-id=264f6ba9-9858-4b7d-a5c8-8c01cc91b105
```

With optional flags:
```bash
# Dry-run mode (preview what would be deleted)
./scw-do data-orchestrator run cleanup region=fr-par older-than=30d project-id=264f6ba9-9858-4b7d-a5c8-8c01cc91b105 dry-run=true
```

Supported duration suffixes: `d` (days), `h` (hours), `m` (minutes)

#### List Workflow runs
```bash
./scw-do data-orchestrator run list region=fr-par project-id=264f6ba9-9858-4b7d-a5c8-8c01cc91b105
```

#### Get a run status
```bash
./scw-do data-orchestrator run get workflow-run-id=bacc819b-5cce-499f-a3df-025a2d7056b9 region=fr-par
```

#### Get a runs status in graph
```bash
./scw-do data-orchestrator run status workflow-run-id=bacc819b-5cce-499f-a3df-025a2d7056b9 region=fr-par
```

#### List Projects with Data Orchestrator Permissions
List all accessible projects and their Data Orchestrator permissions (read/write):
```bash
./scw-do data-orchestrator project list
```

Example output:
```
PROJECT ID                            PROJECT NAME    READ    WRITE    PERMISSIONS
264f6ba9-9858-4b7d-a5c8-8c01cc91b105  my-project      true    true     DataOrchestratorReadOnly, DataOrchestratorWrite
31f89829-6143-46c3-bfc8-6d98907d8e72  test-project    true    false    DataOrchestratorReadOnly
```

#### For a full list of commands, run:
```bash
./scw-do data-orchestrator --help 
```

### License
This project is licensed under the MIT License - see the LICENSE file for details.
