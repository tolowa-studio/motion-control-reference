# MOTION Control Reference

An opinionated reference architecture for a small operations control plane:

- GitHub Actions supplies an isolated execution environment and GitHub OIDC identity.
- An external scheduler supplies the clock for work that must run predictably.
- A third-party dead-man's switch detects silence, not only explicit failures.
- A cloud secret manager remains the only source for credentials.

This repository is intentionally a reference implementation. It contains no production
service inventory, account identifiers, workflow exports, backup destinations, or
credentials. Adapt the examples to your own environment before use.

## What is included

- `examples/github-actions/health-check.yml` — an OIDC-authenticated, off-box health check
  with a dead-man's switch.
- `examples/github-actions/backup-dispatch.yml` — a manually or externally dispatched
  backup job template with concurrency protection.
- `docs/operating-model.md` — the reliability and scheduling model.
- `docs/security-model.md` — the public-repo security boundary.

## Core design

```
External scheduler ──workflow_dispatch──> GitHub Actions
                                             │
                                             ├── OIDC ──> cloud identity provider
                                             ├── reads ─> secret manager
                                             ├── probes ─> protected service
                                             └── pings ─> independent dead-man's switch
```

The external scheduler gives predictable timing. GitHub Actions supplies short-lived
identity. The dead-man's switch remains outside both systems so a failed scheduler or
CI platform becomes visible.

## Before adopting

1. Create a dedicated cloud service account with the smallest permissions possible.
2. Configure GitHub OIDC trust to allow only this repository and branch.
3. Store credential values in a provider secret manager; never commit them or put them
   in scheduler headers.
4. Replace every `REPLACE_ME` value in the examples.
5. Enable secret scanning and push protection on the repository.

## License

MIT. See [LICENSE](LICENSE).
