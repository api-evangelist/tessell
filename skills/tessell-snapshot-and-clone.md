---
name: Snapshot a database and clone a Tessell service
description: >-
  Use a service's Availability Machine to take a manual snapshot, list recovery points, and clone
  a new DB service from the data.
api: openapi/tessell-openapi-original.json
generated: '2026-07-21'
method: generated
operations:
  - getAvailabilityMachines
  - getAvailabilityMachine
  - createDatabaseSnapshotRequest
  - getDatabaseSnapshots
  - createBackupRequest
  - getDatabaseBackups
  - cloneTessellService
  - restoreTessellService
---

# Snapshot a database and clone a Tessell service

Every Tessell DB service has an **Availability Machine** — the data-protection object that owns
its snapshots, backups, and access policies.

1. **Find the Availability Machine** — `getAvailabilityMachines` (`GET /availability-machines`)
   filtered by service, or `getAvailabilityMachine` by id.
2. **Take a snapshot** — `createDatabaseSnapshotRequest` creates a manual snapshot on the
   Availability Machine; list recovery points with `getDatabaseSnapshots`.
3. **Native backups** — `createBackupRequest` / `getDatabaseBackups` manage engine-native
   backups (the Dataflix catalog exposes them for consumption).
4. **Clone** — `cloneTessellService` (`POST /availability-machines/{availability-machine-id}/clones`) provisions a
   NEW service from a snapshot, backup, or point-in-time of the source. Poll the new service's
   status/timeline as with provisioning.
5. **Restore in place** — `restoreTessellService` restores the existing service from a chosen
   recovery point (destructive to current state; confirm before invoking).

## Rules

- Snapshot/backup/clone/restore are **async** — poll status endpoints; do not assume completion.
- Restore is destructive — require explicit human confirmation before calling
  `restoreTessellService`.
- No idempotency-key support: check for an in-flight snapshot/clone before re-submitting.
