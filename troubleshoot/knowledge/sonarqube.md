# SonarQube Knowledge Base

## Quality Gate Statuses
- **PASSED**: All conditions met — safe to proceed
- **FAILED**: One or more conditions violated — do not promote artifact
- **NONE**: No Quality Gate configured for the project
- **ERROR**: Analysis error — check scanner logs

## Common Analysis Failures

### "Failed to connect to SonarQube server"
- Check `SONAR_HOST_URL` and network connectivity from the CI agent
- Verify the token has `Execute Analysis` permission
- TLS/SSL: ensure the CA cert is trusted on the agent

### "You're not authorized to run analysis"
The token used does not have permission. Assign `Execute Analysis` permission in SonarQube → Project → Permissions.

### Coverage is 0% but tests pass
- Coverage report not generated before scanner runs — run `mvn test` or `pytest --cov` first
- `sonar.coverage.jacoco.xmlReportPaths` / `sonar.python.coverage.reportPaths` not set
- Report file path does not match the configured path

### New Code Quality Gate fails on old code
The baseline branch is not set correctly. Set `sonar.newCode.referenceBranch` or configure the New Code period in project settings.

### Duplications threshold exceeded
Check `sonar.cpd.exclusions` to exclude generated code or vendor libraries.

## Branch Analysis
- Branch analysis requires Developer Edition or higher
- Pull Request decoration requires webhook configured in the SCM provider
- `sonar.pullrequest.key`, `sonar.pullrequest.branch`, `sonar.pullrequest.base` must all be set for PR analysis

## Performance
- Large projects: increase scanner heap via `SONAR_SCANNER_OPTS=-Xmx2g`
- Parallel analysis not supported — run one scanner at a time per project

## Token Management
- Tokens expire based on server policy — rotate before expiry
- Use Project Analysis Tokens (not user tokens) for CI pipelines
