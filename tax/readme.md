## Tables

Tax introduces a single key table to the `tax` schema:

* `tax.tenants` - Defines the tenants by which data is separated.

Additionally, Tax introduces these bridge tables:

* `tax.tenant_users` - Tracks which `auth.users` belong to which `tax.tenants`, as well as the status of a user in a tenant.

## Helper Functions

These depend heavily on the process we want to implement for user invitation.

### Tenant

* `tax.add_user_to_tenant(<auth.user.id>, <tax.tenant.id>)` - Adds a user to a tenant.
* `tax.remove_user_from_tenant(<auth.user.id>, <tax.tenant.id>)` - Removes a user from a tenant.

### Verification

* `tax.user_in_tenant(<auth.user.id>)` - Returns true if a user is in the tenant. Otherwise, returns false.

### Brax + Tax

* `tax.user_has_permission_in_tenant(<auth.user.id>, <brax.permissions.code>, <tax.tenant.id>)` - Returns true if a user has been assigned the specified permission via a user group that they belong to in the specified tenant. Otherwise, returns false.
