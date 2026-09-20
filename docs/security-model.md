# Security model

## Public code, private operations

This reference repository may be public because it holds patterns and placeholders,
not a live operational map. Keep the following out of public repositories:

- credential values, connection strings, signed URLs, and webhook tokens;
- exported automation workflows that contain customer data or credential references;
- production hostnames, project IDs, service inventories, and recovery locations;
- incident logs, backups, and monitoring history.

## Identity

Use GitHub OIDC with a dedicated service account. Bind trust to the exact repository
and protected branch. Grant only the permissions each workflow needs; a backup job
should not also be able to deploy or administer other services.

## Workflow safety

- Keep `permissions` explicit and minimal.
- Use `pull_request` rather than `pull_request_target` for untrusted fork code.
- Pin third-party actions to reviewed versions or commit SHAs.
- Use protected branches for workflow changes.
- Enable GitHub Secret Scanning and push protection before accepting contributions.

## Secret incident response

If a credential appears in Git history, revoke or rotate it before rewriting history.
History cleanup reduces future exposure; it does not invalidate a credential that may
already have been copied.
