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
- [Macos ARM](https://github.com/RaphaelMotais/DataOrchestratorCLI/releases/download/1.0.0.0/scw-do-macos-arm)
- [Macos AMD](https://github.com/RaphaelMotais/DataOrchestratorCLI/releases/download/1.0.0.0/scw-do-macos-amd64)
- [Windows](https://github.com/RaphaelMotais/DataOrchestratorCLI/releases/download/1.0.0.0/scw-do.exe)
- [Linux](https://github.com/RaphaelMotais/DataOrchestratorCLI/releases/download/1.0.0.0/scw-do-linux-amd64)


### Configuration
Set your Scaleway credentials via environment variables:
```bash
export SCW_ACCESS_KEY="your_access_key"
export SCW_SECRET_KEY="your_secret_key"
export SCW_DEFAULT_ORGANIZATION_ID="you_organization_id"
export SCW_API_URL="https://api.scaleway.com" (for external production)
```

### Worflow Definition example
```bash
document:
  dsl: 1.0.0
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
        region: fr-par
  - taskTry:
      try:
        - try_task__fail:
            call: serverless_job
            with:
              id: daa3ea4e-97f3-4e5c-8fbc-66e438c979b9
              region: fr-par
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
./scw-do data-orchestrator definition create region=fr-par name="test-raph-001" project-id="264f6ba9-9858-4b7d-a5c8-8c01cc91b105" version-name="v1-0-0" yaml-content=@test.yaml cron-schedule="0 0 * * *"   cron-timezone="Europe/Paris"
```

#### Update a Workflow Definition
```bash
./scw-do data-orchestrator definition update workflow-definition-id=8bf15625-dbda-4dd0-a229-360b917e27e7 yaml-content=@test.yaml region=fr-par
```

#### Update a Workflow Definition with cron schedule
```bash
./scw-do data-orchestrator definition update workflow-definition-id=8bf15625-dbda-4dd0-a229-360b917e27e7 name="super-workflow" cron-schedule="1 0 * * *" cron-timezone="Europe/Paris" region=fr-par
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

#### List Workflow runs
```bash
./scw-do data-orchestrator run list region=fr-par
```

#### Get a run status
```bash
./scw-do data-orchestrator run get workflow-run-id=bacc819b-5cce-499f-a3df-025a2d7056b9 region=fr-par
```

#### Get a runs status in graph
```bash
./scw-do data-orchestrator run status workflow-run-id=bacc819b-5cce-499f-a3df-025a2d7056b9 region=fr-par
```

#### For a full list of commands, run:
```bash
./scw-do data-orchestrator --help 
```

### License
This project is licensed under the MIT License - see the LICENSE file for details.
