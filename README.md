# teradata-agentstack

A Python SDK package that dynamically generates client classes from OpenAPI specifications for Teradata AI/ML platform services.

**Version:** 20.00.00.00 | **Python:** ≥ 3.9 (64-bit) | **Platform:** Windows, macOS, Linux

---

## Overview

`teradata-agentstack` provides a unified framework for interacting with Teradata platform REST APIs. Instead of hand-writing API wrappers, each SDK module reads the bundled OpenAPI JSON specification at import time and automatically creates Python classes and methods:

- **OpenAPI Tags** are converted to CamelCase Python classes.
- **Operation IDs** are converted to snake_case instance methods.
- A `blueprint()` helper lists all generated classes for a module.

### Included SDKs

| Module | Client Class | Generated Classes | Service |
|---|---|---|---|
| `teradata_agentstack.sqlgen` | `SqlGenClient` | `SqlGenApi`, ... | Teradata SQL Generation |
| `teradata_agentstack.agentops` | `AgentOpsClient` | `Definitions`, `Deployments`, `Secrets`, `Health` | Teradata AgentOps |
| `teradata_agentstack.rayops` | `RayClusterManagementClient` | `RayClusterManagement`, ... | Ray Cluster Management |
| `teradata_agentstack.agento11y` | `AgentO11yClient` | `Traces`, `Projects`, `Evaluations`, ... | Agent Observability |
| `teradata_agentstack.access_manager` | `AccessManagerClient` | `Keys`, `Admin`, `Proxy` | API Access Manager |
| `teradata_agentstack.inference_engine` | `InferenceEngineClient` | `Secrets`, `Pools`, `Endpoints`, ... | Inference Engine |
| `teradata_agentstack.mcp_management` | `MCPManagementClient` | `Servers`, ... | MCP Management Service |
| `teradata_agentstack.mcp_endpoint_management` | `MCPEndpointManagementClient` | `Endpoints`, ... | MCP Endpoint Management |

---

## Installation

```bash
pip install teradata-agentstack
```

> **Note:** A 64-bit Python environment is required. Installation will fail on 32-bit platforms.

### Dependencies

| Package | Version |
|---|---|
| `teradataml` | >=20.00.00.10 |
| `requests` | ≥ 2.33.0 |
| `oauthlib` | ≥ 3.2.2 |
| `requests-oauthlib` | ≥ 2.0.0 |
| `pydantic` | ≥ 2.10.6 |
| `PyYAML` | ≥ 6.0.2 |
| `pandas` | ≥ 0.22 |

---

## Authentication

All SDK clients accept the same authentication objects, imported from the top-level package:

```python
from teradata_agentstack import ClientCredentialsAuth, DeviceCodeAuth, BearerAuth
from teradata_agentstack._auth_modes import BasicAuth
```

### ClientCredentialsAuth — OAuth2 Client Credentials

```python
auth = ClientCredentialsAuth(
    auth_token_url="https://your-sso/token",
    auth_client_id="your_client_id",
    auth_client_secret="your_client_secret"
)
```

### DeviceCodeAuth — OAuth2 Device Code Flow

```python
auth = DeviceCodeAuth(
    auth_token_url="https://your-sso/token",
    auth_device_auth_url="https://your-sso/device_authorization",
    auth_client_id="your_client_id"
)
```

> Tokens are cached at `~/.teradataml/sdk/.token` and reused on subsequent calls.

### BearerAuth — Static Bearer Token

```python
auth = BearerAuth(auth_bearer="your_bearer_token")
```

### BasicAuth — Username / Password

```python
auth = BasicAuth(username="user", password="pass")
```

### Authentication Precedence Order

Each configuration value (`base_url`, `auth`, `ssl_verify`) is resolved in the following order. The first source that provides a value wins:

1. **Constructor arguments** — values passed directly to the client (e.g., `base_url=...`, `auth=...`).
2. **Environment variables** — values read from the process environment (e.g., `BASE_URL`, `BASE_API_AUTH_MODE`).
3. **YAML config file** — values loaded from the config file (explicit `config_file` path, or the default `~/.teradataml/sdk/config.yaml`).

> If none of the three sources supplies a required value, a `TeradataMlException` is raised.

### Authentication via Environment Variables

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

### Authentication via YAML Config File

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

---

## Quick Start

### SqlGen SDK

```python
from teradata_agentstack import BearerAuth
from teradata_agentstack.sqlgen import SqlGenClient

auth = BearerAuth(auth_bearer="your_bearer_token")

client = SqlGenClient(
    base_url="http://your-server:port",
    auth=auth,
    hostname="your_db_host",
    database="your_database",
    vectorstore="your_vectorstore",
    ssl_verify=False
)
```

### AgentOps SDK

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
```

### Ray Cluster Management SDK

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

### Agent Observability SDK

```python
from teradata_agentstack._auth_modes import BearerAuth
from teradata_agentstack.agento11y import AgentO11yClient

auth = BearerAuth(auth_bearer="your_bearer_token")

client = AgentO11yClient(
    base_url="https://your-agento11y-server",
    auth=auth,
    project_id="your-project-id"
)
```

### Access Manager SDK

```python
from teradata_agentstack._auth_modes import BearerAuth
from teradata_agentstack.access_manager import AccessManagerClient

auth = BearerAuth(auth_bearer="your_bearer_token")

client = AccessManagerClient(
    base_url="https://your-server:9876",
    auth=auth
)
```

### Inference Engine SDK

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

### MCP Management SDK

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

### MCP Endpoint Management SDK

```python
from teradata_agentstack._auth_modes import BasicAuth
from teradata_agentstack.mcp_endpoint_management import MCPEndpointManagementClient

auth = BasicAuth(username="user", password="pass")

client = MCPEndpointManagementClient(
    base_url="https://your-mcp-server:8001",
    auth=auth
)
```

---

## Pydantic Model Support

All SDK modules support passing Pydantic models directly to request body parameters. The SDK automatically serializes models before sending:

```python
from teradata_agentstack.agentops.models import AgentDefinitionCreateRequest

# Pass Pydantic model directly — no need to call .model_dump()
result = agent_definitions.create(
    body=AgentDefinitionCreateRequest(name="my-agent", framework="LangGraph", ...),
    file="/path/to/agent.zip",
)

# Equivalent to manually serializing (still supported)
result = agent_definitions.create(
    body=AgentDefinitionCreateRequest(...).model_dump_json(by_alias=True, exclude_unset=True),
    file="/path/to/agent.zip",
)
```

---

## Polling Async Resources

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

---

## The `blueprint()` Function

Every SDK module exposes a `blueprint()` function that prints the dynamically generated classes available for that module:

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

---

## How Dynamic Generation Works

When you import an SDK module (e.g., `from teradata_agentstack.agentops import AgentOpsClient`), the module:

1. Reads the bundled OpenAPI JSON specification file.
2. Converts each **tag** into a CamelCase Python class (unrecognized tags fall into `DefaultApi`).
3. Converts each **operationId** into a snake_case method on the corresponding class.
4. Validates parameters at call time — checking for required args, correct types, and permitted enum values.

All method arguments must be passed as **keyword arguments** (positional arguments are not supported).

---

## Notes and Limitations

- Only **keyword arguments** are accepted in generated API methods; positional arguments are not supported.
- Validations are performed for `string`, `integer`, `number`, `boolean`, `array`, and `object` parameter types.
- **Pydantic models** can be passed directly to `body` parameters — the SDK auto-serializes them.
- TLS certificate verification is enabled by default. Disable only in trusted development environments (`ssl_verify=False`).

---

## License

Copyright 2026 Teradata. All rights reserved. Teradata Confidential and Trade Secret.
