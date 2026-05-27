# Multi-tenancy

!!! tip "Enterprise Edition"

    Multi-tenancy is an **OpenAEV Enterprise Edition** feature. A valid EE license is required to create and manage tenants.
    See [Enterprise Edition](enterprise.md) for activation instructions.

Multi-tenancy allows a single OpenAEV instance to host multiple **isolated workspaces**, called tenants. Each tenant has its own data, users, roles, groups, integrations, and infrastructure — completely separated from other tenants on the same platform.

This is the recommended deployment model for:

- **MSSPs** managing multiple customers from a single platform
- **Large organizations** isolating business units or subsidiaries
- **Service providers** offering OpenAEV as a managed service

---

## Concepts

### Tenant

A tenant is a fully isolated workspace within the OpenAEV platform. It contains:

- Its own **users, groups, and roles**
- Its own **scenarios, simulations, and atomic tests**
- Its own **assets, teams, and players**
- Its own **integrations** (injectors, collectors, executors)
- Its own **dedicated message queues** (RabbitMQ) and **file storage** (S3/MinIO bucket prefix)

Data from one tenant is never visible to users of another tenant.

### Platform vs. Tenant scope

Some resources in OpenAEV are **platform-wide** (shared across all tenants) while others are **tenant-scoped** (private to a tenant):

| Resource | Scope |
|---|---|
| Threat arsenal actions (payloads) | Platform-wide |
| Injector contracts | Platform-wide |
| Data packs | Platform-wide |
| Taxonomies (tags, kill chains) | Platform-wide |
| Scenarios, Simulations | Tenant-scoped |
| Assets, Teams, Players | Tenant-scoped |
| Findings, Dashboards | Tenant-scoped |
| Users, Groups, Roles | Tenant-scoped |
| Integrations (connectors) | Tenant-scoped |

!!! info "Dual-scope resources"

    Some resources like **Users** and **Groups** exist at both levels: platform administrators manage them globally, while tenant administrators manage their own members independently.

---

## Managing tenants

### Creating a tenant

To create a new tenant:

1. Log in as a **platform administrator** (user with `Manage platform settings` capability).
2. Go to **Settings → Tenants**.
3. Click **Create tenant**.
4. Fill in the tenant name and optional description.
5. Save.

Once created, the tenant is immediately active. Built-in integrations (injectors, collectors) are automatically registered for the new tenant.

!!! warning "EE license required"

    The **Create tenant** button is only available if a valid Enterprise Edition license is active on the platform.

### Updating a tenant

1. Go to **Settings → Tenants**.
2. Click the ellipsis menu (⋮) next to the tenant.
3. Select **Edit**.
4. Update the name or description.
5. Save.

### Deleting a tenant

Tenant deletion is a **soft-delete** operation. The tenant and all its data are retained for **30 days** before permanent purge. During this period, the tenant can be reactivated.

To delete a tenant:

1. Go to **Settings → Tenants**.
2. Click the ellipsis menu (⋮) next to the tenant.
3. Select **Delete**.
4. Confirm the deletion.

!!! danger "Permanent deletion after 30 days"

    After 30 days, the tenant and **all its data** (scenarios, simulations, assets, findings, documents) are permanently purged and cannot be recovered.

### Reactivating a tenant

If a tenant has been deleted but the 30-day retention period has not elapsed:

1. Go to **Settings → Tenants**.
2. Locate the deleted tenant (shown with a deleted status).
3. Click **Reactivate**.

---

## Users and access in a multi-tenant setup

### Assigning users to a tenant

Users are assigned to tenants through **groups**. A group belongs to a specific tenant and carries roles that define what its members can do within that tenant.

To add a user to a tenant:

1. Go to **Settings → Security → Groups** (within the target tenant context).
2. Create or edit a group.
3. Add the user to the group.
4. Assign a role to the group.

A user can belong to **multiple tenants** simultaneously. Their permissions are evaluated independently in each tenant context.

### Roles and capabilities per tenant

Roles and capabilities work the same way as in single-tenant mode — see [Users and RBAC](users-and-rbac.md) for the full reference.

Each tenant manages its own roles. A role defined in Tenant A is not visible in Tenant B.

### Platform administrators

Platform administrators have access to the global **Settings** area where tenants are managed. They are **not** automatically members of any tenant — they must be explicitly added to a tenant group if they need access to tenant data.

---

## SSO and tenant mapping

When using Single Sign-On (OpenID Connect or SAML2), you can automatically assign users to a specific tenant at login using the `tenant_id` parameter.

### Configuration

Add the following property for your SSO provider registration:

| Parameter | Environment variable | Description |
|---|---|---|
| `openaev.provider.{registrationId}.tenant_id` | `OPENAEV_PROVIDER_{registrationId}_TENANT_ID` | Tenant ID to assign users to when they log in via this SSO provider |

**Example** — mapping an Azure AD SAML2 provider to a specific tenant:

```properties
OPENAEV_PROVIDER_AZURE_TENANT_ID=<your-tenant-uuid>
```

!!! tip "Multiple SSO providers"

    You can configure multiple SSO providers, each mapped to a different tenant. Users logging in via provider A are placed in tenant A; users logging in via provider B are placed in tenant B.

### Group management via SSO

To manage group membership automatically from your identity provider:

| Parameter | Environment variable | Description |
|---|---|---|
| `openaev.provider.{registrationId}.groups_management` | `OPENAEV_PROVIDER_{registrationId}_GROUPS_MANAGEMENT` | Enable automatic group assignment from SSO claims |

See [Authentication](../deployment/authentication.md) for the full SSO configuration reference.

---

## Integrations in a multi-tenant setup

### Built-in integrations

When a new tenant is created, OpenAEV automatically registers all **built-in integrations** (injectors, collectors, executors) for that tenant. No manual configuration is required.

### Per-tenant queues

Each tenant gets its own **RabbitMQ queues** and **S3/MinIO storage prefix**, ensuring complete isolation of data flows between tenants:

- Inject execution messages are routed to the tenant's dedicated queue
- Files (documents, implant binaries) are stored under a tenant-specific prefix in the S3 bucket

### Connector instances

Connector instances (e.g. OpenCTI, CrowdStrike, SentinelOne) are scoped to a tenant. A connector configured in Tenant A is not visible in Tenant B. Each tenant manages its own connector instances independently.

---

## Configuration reference

The following parameters control multi-tenancy behavior. All can be set as environment variables (replace `.` with `_` and prefix with the key in uppercase).

### SSO tenant mapping

| Parameter | Environment variable | Default | Description |
|---|---|---|---|
| `openaev.provider.{id}.tenant_id` | `OPENAEV_PROVIDER_{ID}_TENANT_ID` | _(none)_ | Assigns users authenticating via this SSO provider to the specified tenant (UUID) |
| `openaev.provider.{id}.groups_management` | `OPENAEV_PROVIDER_{ID}_GROUPS_MANAGEMENT` | _(none)_ | Enables automatic group assignment from SSO provider claims |

### Tenant lifecycle

Tenant creation, deletion, and purge are managed through the platform UI (Settings → Tenants). There is no configuration parameter to change the **30-day soft-delete retention period** — contact Filigran support if you need a custom retention window.

### Feature flag

Multi-tenancy is gated by the Enterprise Edition license. No additional feature flag is required beyond a valid EE license.

---

## Frequently asked questions

**Can a user belong to multiple tenants?**  
Yes. A user can be a member of groups in multiple tenants. Their session automatically switches context depending on which tenant they are navigating.

**Can platform-wide resources (payloads, injector contracts) be restricted per tenant?**  
No. Platform-wide resources are visible to all tenants. Access control at the tenant level applies only to tenant-scoped resources.

**What happens to tenant data when a tenant is deleted?**  
Data is soft-deleted and retained for 30 days. After that, it is permanently purged. Reactivation is possible within the 30-day window.

**Is tenant isolation enforced at the database level?**  
Yes. OpenAEV uses a Hibernate filter (`tenantFilter`) applied automatically to every database query. Queries without a valid tenant context cannot access tenant-scoped data.

**Can I migrate data from one tenant to another?**  
There is no built-in migration tool between tenants. Contact Filigran support for assistance.
