## Teradata AgentStack SDKs

`teradata_agentstack` is a suite of Python SDKs developed by Teradata for managing
AgentOps, Access Manager, Inference Engine, MCP Management, RayOps, and related
AgentStack services. It offers a comprehensive set of APIs for deploying and
operating models, agents, evaluations, MCP servers, and Ray jobs on the
Teradata platform.

A `blueprint()` helper lists all available classes and operations for a module.

For community support, please visit the [Teradata Community](https://support.teradata.com/community?id=community_forum&sys_id=14fe131e1bf7f304682ca8233a4bcb1d).

For Teradata customer support, please visit [Teradata Support](https://support.teradata.com/csm).

Copyright 2026, Teradata. All Rights Reserved.

### Table of Contents
* [Version Compatibility](#version-compatibility)
* [Release Notes](#release-notes)
* [Included SDKs](#included-sdks)
* [Installation](#installation)
* [Authentication](#authentication)
* [Quick Start](#quick-start)
* [Notebook Workflows](#notebook-workflows)
* [Pydantic Model Support](#pydantic-model-support)
* [Polling Async Resources](#polling-async-resources)
* [The blueprint() Function](#the-blueprint-function)
* [Notes and Limitations](#notes-and-limitations)
* [License](#license)

## Version Compatibility

| AgentStack Version | AI Studio Version |
|---|---|
| v20.00.00.00 | AI Studio 1.3 |
| v20.00.00.01 (Hotfix) | AI Studio 1.3 AWS (with Unstructured) |
| v20.00.00.02 | AI Studio 1.3 Update 1 |
| v20.00.00.03 | AI Studio 1.4 |

## Release Notes

### Version 20.00.00.03
Platform Target: AI Studio 1.4
* ##### New Features/Functionality
  * ##### Access Manager
    * Added a Playground execute endpoint to try out an API key directly against a model.
  * ##### AgentOps
    * Added an Evaluations resource to create, list, view, cancel, and view logs for evaluation runs.
  * ##### Inference Engine
    * Added a HuggingFace connectivity check endpoint.
    * Added an endpoint to list available GPU types.
    * Added an endpoint to view deployment events.
    * Added an endpoint to list locally stored models.
  * ##### MCP Management
    * Added a server config resource to get, create, update, and delete configuration for an MCP server.
  * ##### RayOps
    * Job details, status, logs, and delete are now available by `submission_id`.

* ##### Parameter Changes
  * ##### RayOps
    * `job_name` → `submission_id` — used when looking up, checking the status of, viewing logs for, or deleting a job.
    * `code_file` → `source_file` — used when submitting a job.

* ##### Breaking Changes
  * ##### Access Manager
    * Health and usage response fields have changed; review before upgrading.
  * ##### AgentOps
    * `repository_url` was removed from the Git sync request.
  * ##### Inference Engine
    * Model and deployment creation requests have updated field requirements.
  * ##### MCP Management
    * The server response shape has changed.
  * ##### RayOps
    * Update any code referencing the old `job_name` / `code_file` parameter names above.

  No changes were made this release to `agento11y`, `mcp_endpoint_management`, `modelops`, or `sqlgen`.

### Version 20.00.00.02 
Platform Target: AI Studio 1.3 Update 1

* ##### Key Changes
  * Documentation Refinements: General documentation updates and improvements across the package README.
  * Public Notebook Workflows: Added direct reference links to public sample notebook workflows.

### Version 20.00.00.01 
Platform Target: AI Studio 1.3 AWS (with Unstructured)

* ##### Key Fixes & Deliverables
  * RayOps Startup & Import Fix: Resolved a startup/import issue that could stop the RayOps module / `teradata_agentstack` libraries from loading properly during OPF Ray operations.
  * Access Manager Fallback Models: Fixed Access Manager fallback models so API responses are recognized correctly and operate reliably.
  * Contract-Based Test Generation: Updated the test case generator to support contract-based testing and ran dynamic test suites to validate all test runs.
  * Documentation & Notebook Deliverables: Published SDK documentation/HTML reference files and created updated sample notebooks following JupyterHub (JH) guidelines.

### Version 20.00.00.00 
Platform Target: AI Studio 1.3 
The initial release of the `teradata_agentstack` Python SDK, providing a unified interface across 7 core Teradata AI Studio platform services.

* ##### Supported SDK Modules
  * `agentops` (`teradata_agentstack.agentops`): Package, deploy, version, start/stop, and manage always-on AI agents and deployment secrets.
  * `rayops` (`teradata_agentstack.rayops`): Provision node groups and manage the lifecycle of Ray clusters for distributed workloads.
  * `agento11y` (`teradata_agentstack.agento11y`): Trace, inspect spans, execute online/offline evaluations, manage datasets, record feedback scores, and aggregate metrics.
  * `access_manager` (`teradata_agentstack.access_manager`): Manage user and admin API keys, proxy approved LiteLLM management operations, and inspect tenant usage metrics.
  * `inference_engine` (`teradata_agentstack.inference_engine`): Manage model catalogs, versions, workspace secrets, inference deployments (vLLM/KServe/NIM), and async download jobs.
  * `mcp_management` (`teradata_agentstack.mcp_management`): Deploy, scale, restart, and manage Model Context Protocol (MCP) server instances on Kubernetes.
  * `mcp_endpoint_management` (`teradata_agentstack.mcp_endpoint_management`): Configure custom endpoint path visibility mappings for MCP tools, prompts, and resources.

* ##### Core Capabilities Introduced
  * Interactive Discovery (`blueprint()`): Introspect available SDK classes and methods dynamically at runtime.
  * Unified Authentication: Standardized auth mechanisms across all services with 4 supported modes (`BearerAuth`, `BasicAuth`, `ClientCredentialsAuth`, `DeviceCodeAuth`) supporting direct passing, environment variables, or YAML config files.
  * Pydantic Model Support: Strongly typed request models with automatic serialization.
  * Async Polling: Built-in `.poll(id=...)` utility for long-running operations with automatic terminal state detection.
  * Centralized Error Handling: Standardized retry/backoff logic and HTTP status mapping across all modules.

### Included SDKs

| Module | Client Class | Generated Classes | Service |
|---|---|---|---|
| `teradata_agentstack.agentops` | `AgentOpsClient` | `Definitions`, `Deployments`, `Secrets`, `Health` | Teradata AgentOps |
| `teradata_agentstack.rayops` | `RayClusterManagementClient` | `RayClusterManagement`, ... | Ray Cluster Management |
| `teradata_agentstack.agento11y` | `AgentO11yClient` | `Traces`, `Projects`, `Evaluations`, ... | Agent Observability |
| `teradata_agentstack.access_manager` | `AccessManagerClient` | `Keys`, `Admin`, `Proxy` | API Access Manager |
| `teradata_agentstack.inference_engine` | `InferenceEngineClient` | `Secrets`, `Pools`, `Endpoints`, ... | Inference Engine |
| `teradata_agentstack.mcp_management` | `MCPManagementClient` | `Servers`, ... | MCP Management Service |
| `teradata_agentstack.mcp_endpoint_management` | `MCPEndpointManagementClient` | `Endpoints`, ... | MCP Endpoint Management |

### Installation

**Version:** 20.00.00.03 | **Python:** ≥ 3.9 (64-bit) | **Platform:** Windows, macOS, Linux

```bash
pip install teradata-agentstack
```

> **Note:** A 64-bit Python environment is required. Installation will fail on 32-bit platforms.

#### Dependencies

| Package | Version |
|---|---|
| `teradataml` | >=20.00.00.10 |
| `requests` | ≥ 2.33.0 |
| `oauthlib` | ≥ 3.2.2 |
| `requests-oauthlib` | ≥ 2.0.0 |
| `pydantic` | ≥ 2.10.6 |
| `PyYAML` | ≥ 6.0.2 |
| `pandas` | ≥ 0.22 |

### Authentication

All SDK clients accept the same authentication objects, imported from the top-level package:

```python
from teradata_agentstack import ClientCredentialsAuth, DeviceCodeAuth, BearerAuth, BasicAuth
```

#### ClientCredentialsAuth — OAuth2 Client Credentials

```python
auth = ClientCredentialsAuth(
    auth_token_url="https://your-sso/token",
    auth_client_id="your_client_id",
    auth_client_secret="your_client_secret"
)
```

#### DeviceCodeAuth — OAuth2 Device Code Flow

```python
auth = DeviceCodeAuth(
    auth_token_url="https://your-sso/token",
    auth_device_auth_url="https://your-sso/device_authorization",
    auth_client_id="your_client_id"
)
```

#### BearerAuth — Static Token

```python
auth = BearerAuth(auth_bearer="your_bearer_token")
```

#### BasicAuth — Username / Password

```python
auth = BasicAuth(username="user", password="password")
```

#### Authentication Precedence Order

Each configuration value (`base_url`, `auth`, `ssl_verify`) is resolved in the following order. The first source that provides a value wins:

1. **Constructor arguments** — values passed directly to the client (e.g., `base_url=...`, `auth=...`).
2. **Environment variables** — values read from the process environment (e.g., `BASE_URL`, `BASE_API_AUTH_MODE`).
3. **YAML config file** — values loaded from the config file.

> If none of the three sources supplies a required value, a `TeradataMlException` is raised.

#### Authentication via Environment Variables

You can configure authentication without passing an `auth` object by setting environment variables:

| Variable | Description |
|---|---|
| `BASE_URL` | Base URL of the API endpoint |
| `BASE_API_AUTH_MODE` | `client_credentials`, `device_code`, `bearer`, or `basic` |
| `BASE_SSL_VERIFY` | `true` or `false` |
| `BASE_API_AUTH_CLIENT_ID` | OAuth2 client ID |
| `BASE_API_AUTH_CLIENT_SECRET` | OAuth2 client secret |
| `BASE_API_AUTH_TOKEN_URL` | OAuth2 token endpoint URL |
| `BASE_API_AUTH_DEVICE_AUTH_URL` | OAuth2 device authorization URL |
| `BASE_API_AUTH_BEARER_TOKEN` | Static bearer token |

#### Authentication via YAML Config File

```yaml
# config.yaml
base_url: https://your-server
ssl_verify: true
auth_mode: client_credentials
auth_client_id: your_client_id
auth_client_secret: your_client_secret
auth_token_url: https://your-sso/token
```

Pass the path using the `config_file` argument on any client.

### Quick Start

#### AgentOps SDK

```python
from teradata_agentstack import DeviceCodeAuth
from teradata_agentstack.agentops import AgentOpsClient
from teradata_agentstack.agentops import Definitions, Deployments, Secrets, Health
from teradata_agentstack.agentops.models import (
    AgentDefinitionCreateRequest,
    DeploymentCreateRequest,
)

auth = DeviceCodeAuth(
    auth_token_url="https://your-sso/token",
    auth_device_auth_url="https://your-sso/device_authorization",
    auth_client_id="your_client_id"
)

client = AgentOpsClient(
    base_url="https://your-agentops-server",
    auth=auth,
    workspace_id="your-workspace-id",
    namespace="your-k8s-namespace"
)

# Create API instances
agent_definitions = Definitions(client=client)
deployments = Deployments(client=client)
secrets = Secrets(client=client)

# Create an agent definition — pass Pydantic model directly to body=
created = agent_definitions.create(
    body=AgentDefinitionCreateRequest(
        name="my-agent",
        framework="LangGraph",
        artifacts={"storage_type": "zip"},
    ),
    file="/path/to/agent.zip",
)

# Poll until status reaches terminal state (published/failed)
agent_definitions.poll(id=created.id)

# Create a deployment
deployment = deployments.create(body=DeploymentCreateRequest(...))

# Poll until deployment is active/failed/stopped
deployments.poll(id=deployment.agent_id)
```

> **Note:** The old class names (`AgentDefinitions`, `AgentDeployments`, `AgentSecrets`) still work as backward-compatible aliases.

#### Ray Cluster Management SDK

```python
from teradata_agentstack import ClientCredentialsAuth
from teradata_agentstack.rayops import RayClusterManagementClient

auth = ClientCredentialsAuth(
    auth_token_url="https://your-sso/token",
    auth_client_id="your_client_id",
    auth_client_secret="your_client_secret"
)

client = RayClusterManagementClient(
    base_url="https://your-ray-server",
    auth_data=auth
)
```

#### Agent Observability SDK

```python
from teradata_agentstack import BearerAuth
from teradata_agentstack.agento11y import AgentO11yClient

auth = BearerAuth(auth_bearer="your_bearer_token")

client = AgentO11yClient(
    base_url="https://your-agento11y-server",
    auth=auth,
    project_id="your-project-id"
)
```

#### Access Manager SDK

```python
from teradata_agentstack import BearerAuth
from teradata_agentstack.access_manager import AccessManagerClient

auth = BearerAuth(auth_bearer="your_bearer_token")

client = AccessManagerClient(
    base_url="https://your-server:9876",
    auth=auth
)
```

#### Inference Engine SDK

```python
from teradata_agentstack import ClientCredentialsAuth
from teradata_agentstack.inference_engine import InferenceEngineClient

auth = ClientCredentialsAuth(
    auth_token_url="https://your-sso/token",
    auth_client_id="your_client_id",
    auth_client_secret="your_client_secret"
)

client = InferenceEngineClient(
    base_url="https://your-inference-server",
    auth=auth,
    workspace="your-workspace"
)
```

#### MCP Management SDK

```python
from teradata_agentstack import ClientCredentialsAuth
from teradata_agentstack.mcp_management import MCPManagementClient

auth = ClientCredentialsAuth(
    auth_token_url="https://your-sso/token",
    auth_client_id="your_client_id",
    auth_client_secret="your_client_secret"
)

client = MCPManagementClient(
    base_url="https://your-mcp-server",
    auth=auth
)
```

#### MCP Endpoint Management SDK

```python
from teradata_agentstack import BasicAuth
from teradata_agentstack.mcp_endpoint_management import MCPEndpointManagementClient

auth = BasicAuth(username="user", password="password")

client = MCPEndpointManagementClient(
    base_url="https://your-mcp-server:8001",
    auth=auth
)
```

### Notebook Workflows

End-to-end Jupyter notebook examples demonstrating common workflows are available in the public repository:

[teradata-agentstack/Workflows](https://github.com/Teradata/teradata-agentstack/tree/main/Workflows)

### Pydantic Model Support

All SDK modules support passing Pydantic models directly to request body parameters. The SDK automatically serializes models before sending:

```python
from teradata_agentstack.agentops.models import AgentDefinitionCreateRequest

# Pass Pydantic model directly
result = agent_definitions.create(
    body=AgentDefinitionCreateRequest(name="my-agent", framework="LangGraph", ...),
    file="/path/to/agent.zip",
)
```

### Polling Async Resources

Some SDK classes support a `.poll()` method for resources that are processed asynchronously:

| Module | Class | Terminal States | Success States |
|--------|-------|-----------------|----------------|
| `agentops` | `Definitions` | `published`, `failed` | `published` |
| `agentops` | `Deployments` | `active`, `failed`, `stopped` | `active` |

```python
# Poll until agent definition is published (or failed)
result = agent_definitions.poll(id=agent_def_id, interval=5, max_attempts=20)

# Poll until deployment is active (or failed/stopped)
result = deployments.poll(id=deployment_id, interval=10, max_attempts=30)
```

Parameters:
- `id` (required): Resource ID to poll
- `interval` (optional): Seconds between polls (default varies by resource)
- `max_attempts` (optional): Maximum attempts before timeout

### The `blueprint()` Function

Every SDK module exposes a `blueprint()` function that lists all available classes and operations for that module:

```python
from teradata_agentstack.agentops import blueprint
blueprint()
# ----------------------------------------------------------------
# Available classes for AgentOps SDK:
#     * teradata_agentstack.agentops.Definitions
#     * teradata_agentstack.agentops.Deployments
#     ...
# ----------------------------------------------------------------
```

### Notes and Limitations

- Only **keyword arguments** are accepted in API methods; positional arguments are not supported.
- **Pydantic models** can be passed directly to `body` parameters — the SDK handles serialization automatically.
- TLS certificate verification is enabled by default. Disable only in trusted development environments (`ssl_verify=False`).

## License

Copyright 2026, Teradata. All Rights Reserved. Teradata Confidential and Trade Secret.
