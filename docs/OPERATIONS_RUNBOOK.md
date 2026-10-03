# Stellar Appliance CLI Operations Runbook

This CLI performs day-2 operations on Stellar Cyber KVM appliance hosts.

## Safety boundary

- Run mutating commands only on the intended appliance with sudo/root authorization.
- Prefer read-only `show` commands for health diagnosis.
- Network, ACL, service, patch, VM, hostname, and time changes require change-window and rollback planning appropriate to the host.
- Management-network changes must preserve persistent configuration and runtime convergence checks.
- Preserve evidence before remediation when a production-impacting failure is under investigation.

## Health check

On an approved appliance, run the installed CLI and verify read-only state:

```bash
printf 'show version\nshow hostname\nquit\n' | aella_cli
```

A non-zero exit, missing prompt/output, or failure to read expected appliance state is a health failure.

## Rollback

Use the product-specific rollback path for the affected host setting. For management-network changes, the CLI restores the previous persistent configuration when runtime verification fails. If that configuration cannot be re-applied, stop and perform manual network recovery rather than continuing mutation.
