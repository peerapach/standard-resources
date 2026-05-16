# Docker Standards: Go

## Base Image
- **Builder**: `golang:1.22-alpine`
- **Runner**: `scratch` (distroless)
- **Version**: 1.22

## Rules
- Use multi-stage builds: `builder` (compile), `runner` (scratch)
- Build with `CGO_ENABLED=0` for a fully static binary
- Copy only the compiled binary and CA certificates into the final image
- No shell, no package manager in the runner stage
- Expose port `8080`
- Include a `HEALTHCHECK`

## Security
- `scratch` base: zero attack surface
- Static binary: no shared library dependencies
- No non-root user needed (no OS in scratch)

## Port
- Default: `8080`
