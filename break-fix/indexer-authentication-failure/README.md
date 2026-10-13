# Break/Fix — Wazuh Indexer Authentication Failure During Password Rotation

## Scenario

A Wazuh administrator password rotation was attempted in a Docker-based single-node deployment.

The password was changed in `docker-compose.yml` and a new bcrypt hash was generated for the `admin` user in `internal_users.yml`.

The new security configuration was then prepared for application with `securityadmin.sh`.

## Symptoms

After the change:

- Wazuh containers started
- The indexer logged repeated authentication failures for `admin`
- `securityadmin.sh` connected with the admin certificate but timed out while retrieving cluster state
- The dashboard/manager and indexer were no longer using matching credentials

Representative symptom:

```text
Authentication finally failed for admin
```

`securityadmin.sh` also reported a cluster-state timeout.

## Investigation

The troubleshooting path focused on determining whether the issue was:

- certificate related
- syntax related
- indexer availability related
- credential related

The admin certificate connection succeeded, which ruled out a basic certificate problem.

The repeated `admin` authentication failures in the indexer logs strongly indicated a credential mismatch between Docker services and the active OpenSearch security database.

## Root Cause

The new password had been placed in Docker configuration and hashed in `internal_users.yml`, but the OpenSearch security database had not successfully loaded the new credentials.

As a result:

```text
Manager / Dashboard → New password
Indexer security DB → Old password
```

This created continuous authentication failures.

## Recovery

Backups created before the rotation were restored:

```bash
cp docker-compose.yml.before-password-rotation docker-compose.yml
cp config/wazuh_indexer/internal_users.yml.before-password-rotation config/wazuh_indexer/internal_users.yml
```

The stack was then recreated:

```bash
docker compose down
docker compose up -d
```

No Docker volumes were deleted.

## Verification

Recovery was verified by:

1. Confirming all Wazuh containers were running.
2. Confirming the previous admin credential worked again.
3. Checking that repeated authentication failures stopped.
4. Confirming the Office 365 module started.
5. Confirming Microsoft 365 JSON alerts appeared again in `alerts.json`.
6. Confirming Microsoft 365 events returned to the Wazuh dashboard.

## Lessons Learned

- Always create configuration backups before credential rotation.
- Updating a password file is not the same as successfully applying that password to the live security database.
- A certificate connection succeeding does not prove user authentication is healthy.
- Repeated authentication failures in indexer logs can reveal configuration mismatches quickly.
- Rollback is often safer than continuing to modify a partially broken authentication state.
- Never use `docker compose down -v` during this type of recovery unless data deletion is intentional.
- After infrastructure recovery, validate business telemetry too, not just container status.

## SOC / Engineering Value

This incident provided practical experience with:

- Wazuh/OpenSearch authentication
- Docker service dependencies
- credential rotation
- bcrypt password hashes
- OpenSearch security administration
- log-based root-cause analysis
- rollback and service recovery
- end-to-end telemetry validation

## Sensitive Data Handling

No real password, bcrypt hash, private key, or secret is included in this document.
