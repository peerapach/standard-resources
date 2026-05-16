# Docker Standards: Node.js

## Base Image
- **Image**: `node:20-alpine`
- **Version**: 20 (LTS)

## Rules
- Use multi-stage builds: `deps`, `builder`, `runner`
- Run as non-root user (`nodeuser`, uid 1001)
- Use `npm ci` (not `npm install`) for reproducible installs
- Copy only production `node_modules` into the final image
- Expose port `3000`
- Include a `HEALTHCHECK` pointing to `/health`
- No secrets or `.env` files in the image

## Security
- Alpine base image to minimize attack surface
- Non-root user mandatory
- No `sudo` or privilege escalation

## Port
- Default: `3000`
