## Tables

Tax introduces a single key table to the `tax` schema:

* `tax.tenants` - Defines the tenants by which data is separated.

Additionally, Tax introduces these bridge tables:

* `tax.tenant_users` - Tracks which `auth.users` belong to which `tax.tenants`, as well as the status of a user in a tenant.

## Helper Functions

These depend heavily on the process implemented for user invitation. Aka, is a user added to the tenant immediately a part of it? Do they enter some sort of "invited" stage? Do they have to accept the invite? Does an invite to a user that doesn't exist trigger an email to them?

### Tenant

* `tax.add_user_to_tenant(<auth.user.id>, <tax.tenant.id>)` - Adds a user to a tenant.
* `tax.remove_user_from_tenant(<auth.user.id>, <tax.tenant.id>)` - Removes a user from a tenant.

### Verification

* `tax.user_in_tenant(<auth.user.id>)` - Returns true if a user is in the tenant. Otherwise, returns false.

### Brax + Tax

* `tax.user_has_permission_in_tenant(<auth.user.id>, <brax.permissions.code>, <tax.tenant.id>)` - Returns true if a user has been assigned the specified permission via a user group that they belong to in the specified tenant. Otherwise, returns false.
* `tax.tenant_user_permissions(<tenant.id>, <auth.user.id>, <brax.permissions.code>, <tax.tenant.id>)` - Returns a unique result set containing the user's permissions in the specified tenant. If the user has no permissions in that tenant, returns an empty result set.
