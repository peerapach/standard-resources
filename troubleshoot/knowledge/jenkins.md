# Jenkins Knowledge Base

## Common Exit Codes
- **Exit 1**: General build failure — check console log for the failing step
- **Exit 127**: Command not found — tool not installed or PATH misconfigured
- **Exit 128/137**: Process killed — usually OOM or timeout; increase executor memory
- **Exit 143**: SIGTERM — pipeline was aborted or timed out

## Pipeline Failure Patterns

### Stage fails with "sh: command not found"
The tool (e.g., maven, gradle, docker) is not on the agent PATH. Fix: use `tool` directive or install the tool on the agent.

### "No space left on device"
Docker image cache or workspace is full. Run `docker system prune` on the agent or increase disk. Workspace cleanup plugin helps for long-running agents.

### "Checkout SCM failed"
- Credentials not attached to pipeline
- SSH key not whitelisted on the Git server
- Repository URL changed — update in Jenkinsfile or job configuration

### Build hangs indefinitely
- Missing `timeout` wrapper in Jenkinsfile
- Interactive prompt waiting for input (use `-y` flags)
- Docker pull blocking on rate limit

### "Queue waiting for executor"
All agents are busy. Either add more agents or check for stuck builds holding executors.

## Shared Library Issues
- `groovy.lang.MissingMethodException`: method not available in that library version — pin the library version in `@Library`
- Library not found: check Global Pipeline Libraries configuration in Jenkins system settings

## Credentials & Security
- Credentials must be bound using `withCredentials` or `credentials()` — never hardcoded
- Masked secrets appearing in logs: ensure the credential is type Secret Text, not plain String parameter

## Agent Configuration
- **Label mismatch**: `agent { label 'xxx' }` must match an online agent with that label
- **Docker agent**: requires Docker installed on the agent node and Jenkins has permission to run containers

## Artifact & Archiving
- `archiveArtifacts` path must be relative to workspace
- Artifacts not found: check glob pattern; paths are case-sensitive on Linux agents
