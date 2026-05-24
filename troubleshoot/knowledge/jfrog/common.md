# JFrog Artifactory Knowledge Base

## Common Upload/Download Failures

### "401 Unauthorized"
- Token expired or revoked — generate a new Access Token or API Key
- Using wrong credentials for the repo type (e.g., user/password for a token-only repo)
- Anonymous access disabled — must authenticate every request

### "403 Forbidden"
- User/token lacks Deploy permission on the target repository
- Repository is read-only or archived
- IP allowlist blocking the CI agent IP

### "404 Not Found" on download
- Artifact path is wrong — Artifactory paths are case-sensitive
- Virtual repo does not include the local/remote repo where the artifact lives
- Artifact was deleted or never uploaded (check Artifactory logs)

### "409 Conflict" on upload
- Re-uploading to an immutable snapshot repository where re-deploy is disabled
- Enable "Allow Content Override" in repo settings or use a different path/version

### Slow uploads/downloads
- Use the Artifactory Load Balancer URL, not a single node URL
- Multipart upload: set `X-Explode-Archive` header or use JFrog CLI `--threads` flag
- Check network bandwidth and proxy configuration

## Repository Types
- **Local**: where artifacts are actually stored; enforce deployment policies here
- **Remote**: proxy to external registries (Docker Hub, npm, Maven Central)
- **Virtual**: aggregates local + remote; use this URL in CI pipelines
- **Federated**: multi-site replication (Artifactory Enterprise)

## Docker Registry Issues
- "unauthorized: authentication required": `docker login <artifactory-url>` first
- "denied: requested access to the resource is denied": push permission missing on the Docker repo
- Manifest not found: the image tag was not pushed yet or was garbage-collected

## Cleanup & Retention
- Artifact Cleanup plugin: configure retention policies per repo
- `jf rt del` command: bulk delete by pattern; always dry-run first with `--dry-run`
- AQL queries: use for advanced search before mass-deleting

## JFrog CLI
- Install: `curl -fL https://getcli.jfrog.io | sh`
- Configure server: `jf config add <server-id> --url=<url> --access-token=<token>`
- Upload: `jf rt u "target/*.jar" "repo-name/path/" --build-name=... --build-number=...`
- Publish build info: `jf rt bp <build-name> <build-number>`
