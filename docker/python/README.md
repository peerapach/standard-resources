# Docker Standards: Python

## Base Image
- **Image**: `python:3.12-slim`
- **Version**: 3.12

## Rules
- Use multi-stage builds: `builder`, `runner`
- Set `PYTHONDONTWRITEBYTECODE=1` and `PYTHONUNBUFFERED=1`
- Run as non-root user (`appuser`, uid 1001)
- Use `pip install --no-cache-dir` to reduce image size
- Expose port `8000`
- Include a `HEALTHCHECK` pointing to `/health`
- Pin all dependencies in `requirements.txt`

## Security
- Slim base image to minimize attack surface
- Non-root user mandatory
- No secrets or credentials in the image

## Port
- Default: `8000`
