# CI/CD Pipeline with Docker

[![CI](https://github.com/Karanyadav12082004/CICD-Pipeline-with-Docker/actions/workflows/ci.yml/badge.svg)](https://github.com/Karanyadav12082004/CICD-Pipeline-with-Docker/actions/workflows/ci.yml)

Automated **build → test → push** pipeline for a NestJS app using **GitHub Actions**, **Docker multi-stage builds** and **GitHub Container Registry (GHCR)**.

## Pipeline

```
git push / git tag
      │
      ▼
 Job 1: test    → npm ci → npm test → npm run build
      │ (only if it passes)
      ▼
 Job 2: docker  → build image → push to GHCR
```

## Tech

- NestJS (TypeScript)
- Docker (multi-stage build, non-root user, `.dockerignore`)
- GitHub Actions (`needs`, `permissions`, `GITHUB_TOKEN`, layer cache)
- GHCR (`ghcr.io`)

## Image tagging strategy

| Tag | Created when | Mutable? | Use for |
|---|---|---|---|
| `1.0.1` | Git tag `v1.0.1` | No | Production (pinned, safe rollback) |
| `1.0`, `1` | Git tag `v*.*.*` | Yes | Auto patch / minor updates |
| `latest` | Git tag `v*.*.*` | Yes | Last stable release |
| `edge` | Push to `main` | Yes | Latest development build |
| `sha-<commit>` | Every build | Practically no | Debugging a specific commit |

## Run the image

```bash
docker run -p 3000:3000 ghcr.io/karanyadav12082004/cicd-pipeline-with-docker:latest
```

Open http://localhost:3000

## Release a new version

```bash
git tag v1.0.2
git push origin v1.0.2
```

## What I learned

- Why multi-stage builds give smaller, safer images
- How Docker layer caching speeds up builds (188s → 15s locally)
- Difference between `npm install` and `npm ci`
- How tags are pointers, while the digest is the real identity of an image
- How `needs` stops broken code from reaching the registry