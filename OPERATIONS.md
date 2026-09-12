# DNS reconciliation safety checklist

Use this checklist before enabling writes or investigating unexpected records.
The main risks are an overly broad zone selection and conflicting ownership.

## Review controller arguments

Read the [README](README.md) and your provider's guide under
[docs/tutorials](docs/tutorials).

| Setting | Review question |
| --- | --- |
| provider and credentials | Is the controller targeting the intended account and provider? |
| domain-filter | Does it restrict reconciliation to the intended domain? |
| txt-owner-id | Is the value unique and stable for this controller's ownership? |
| registry | Is TXT ownership tracking configured as intended? |
| policy | Are deletions allowed, or is upsert-only deliberately retaining records? |
| source | Are only the intended Kubernetes resource types being watched? |

Do not change the owner ID simply to adopt existing records. Domain filters are
not a substitute for provider-side IAM restrictions.

## Review a dry run first

Add --dry-run to the approved controller invocation before enabling writes.
Use --once for a single reconciliation pass if appropriate for your test.
A dry run can still read Kubernetes and the provider API and requires approved
credentials. Review its proposed creates, updates and deletes against the
intended zone and source resources.

## Investigate a mismatch

- A missing record: check the source resource, desired hostname, target and filters.
- A stale record: check policy; upsert-only deliberately does not delete records.
- An ownership conflict: compare TXT ownership and concurrent controllers.
- An apparently unchanged record: distinguish provider state from resolver caching and TTL.
- Repeated API failures: check permissions and provider limits before retrying aggressively.

Before a write-enabled rollout, record the current controller configuration and
an approved zone inventory. Reverting a manifest does not automatically restore
all DNS records changed by a previous reconciliation.

## Development note

This review guide was added with AI assistance. Upstream code, licenses and
contributor attribution remain unchanged.
