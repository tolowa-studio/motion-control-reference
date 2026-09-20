# Operating model

## Separate the clock from the identity

GitHub's scheduled events are best-effort. For a job with a real recovery objective,
use an external scheduler to call `workflow_dispatch` at the required cadence. Let the
workflow use GitHub OIDC to obtain short-lived cloud credentials; do not move a static
cloud credential into the scheduler.

## Avoid duplicate triggers

Pick one authoritative path for each cadence. If an external scheduler owns the hourly
backup, remove an equivalent native GitHub cron. Otherwise the same work can run twice,
increase cost, and create avoidable contention.

Keep lower-frequency native schedules only when they serve a distinct recovery tier.

## Treat silence as a failure

A job that never starts cannot report its own error. End every important workflow with
an `if: always()` dead-man ping to an independent service. Configure the check period
and grace window to match the expected cadence plus normal execution time.

## Verify from the destination

For backups, a successful job is not enough. Verify that the expected object exists at
the destination, apply a tested retention policy, and periodically test restore into an
isolated target.
