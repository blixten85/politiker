# REPO.md

`politiker` is a Cloudflare Worker application with shared runtime code, D1 migrations, a log-archive Worker and Python contact-data tooling under `kontakter/`.

## Runtime and data ownership

- Cloudflare D1 is the canonical runtime data source. Do not use Git as a production database or export live D1 snapshots into the repository.
- `app/wrangler.jsonc` is the source of truth for versioned Worker configuration.
- Cloudflare Workers Builds owns production deployment from `main`; GitHub Actions validates but does not duplicate production deployment.
- The app Worker is the sole D1 migration owner. Schema changes use Wrangler-native migrations under `infra/migrations/`.
- `kontakter/` may collect and update contact data but must not provision Workers/resources or own D1 schema migrations.

## Security invariants

- Every account-owned database query must filter on `account_id`; admin endpoints require explicit server-side admin authorization.
- Credentials, OAuth/session/TOTP secrets and mail encryption keys must never be committed, logged or sent to clients.
- Contact-data helpers use least privilege and only the permissions needed for contact-data writes.
- Do not weaken TLS validation.

## Validation

Run the relevant Worker and Python tests/tooling for the changed area. Validate migrations and Wrangler configuration when they change.

The live repository rules currently require `CI / required`, `docker` and Trivy code-scanning policy. Do not rename or remove a required check/scanner without updating and verifying the live ruleset in the same migration.

Pin third-party GitHub Actions to full commit SHAs.
