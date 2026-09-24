# Brax: The Based Role Access Control System

Brax is your one-and-done solution for Supabase role based access control. It builds on top of native Supabase and postgres features to provide you all the mumbo-jumbo-permission-stuff you'd otherwise have to build in every enterprise system. This allows you to jump right into building the parts of your application that actually matter.

While it may get lonely, Brax can run all by itself. It can also be combined with its sibling tenant access control system Tax to add tenant separation and isolation to a single Supabase instance. You can also combine it with with Smax to leverage your Brax and/or Tax configurations to automatically configure secure access control systems and smack down any bad data security practices. (In case this is confusing... Brax can be run by itself, but is required to run either Tax or Smax.)

## What It Does

Brax adds a single `brax` schema to your Supabase postgres instance that gives you all the tools you need to create, read, update, and delete user groups, roles, and permissions. Building on top of the native `auth.user` object from Supabase, Brax even provides code you can add to your Supabase auth token hook to send a user's permissions to all token consumers along with all your user's authenticated requests.

## Tables

Brax introduces several key tables in the `brax` schema:

* `brax.groups` - Groups of users.
  * `id` - UUID of the group.
  * `name` - Friendly name of group.
  * `description` - Your opportunity to become verbose about the group, it's purpose, and what it was called into existence for.
* `brax.roles` - Collections of application permissions.
  * `id` - UUID of the role.
  * `name` - Friendly name of the role.
  * `description` - Your opportunity to eloquently describe the role's... er, description.
* `brax.permissions` - Application flags that correspond to application feature or data access.
  * `id` - Enum that identifies the permission's scope. Recommended to use dot notation, like `blogs.create` or `authors.name.read`.
  * `name` - Friendly name of the permission.
  * `description` - If you can't guess what this is for, you should probably just stop reading.

Additionally, Brax introduces these necessary bridge tables:

* `brax.group_users` - Tracks which `auth.users` belong to which `brax.groups`.
  * `brax.group.id` - UUID foreign key to the brax group the user belongs to.
  * `auth.user.id` - UUID foreign key to the auth user being added to the group.
* `brax.group_roles` - Tracks which `brax.roles` are applied to which `brax.groups`.
  * `brax.group.id` - UUID foreign key to the brax group the role is assigned to.
  * `brax.role.id` - UUID foreign key to the brax role being added to the group.
* `brax.role_permissions` - Tracks which `brax.permissions` are associated with which `brax.roles`.
  * `brax.role.id` - UUID foreign key to the brax role a permission is assigned to.
  * `brax.permission.id` - Enum foreign key to the brax permission being added to the role.

## Helper Functions

While you *can* modify `brax` tables directly, it's not recommended. Instead, you are encouraged to use the following helper functions. These functions contain what you might call "The Sauce".

By default, these functions are granted only to the supabase-created postgres admin role. However, you can use custom grants or wrapper functions to provide them to whoever you want.

### Groups

* `brax.add_user_to_group(<auth.user.id>, <brax.group.id>)` - Adds a user to a group.
* `brax.remove_user_from_group(<auth.user.id>, <brax.group.id>)` - Removes a user from a group.
* `brax.add_permission_to_group(<brax.permission.id>, <brax.group.id>)` - Adds a permission to a group.
* `brax.remove_permission_from_group(<brax.permission.id>, <brax.group.id>)` - Removes a permission from a group.

### Permissions

* `brax.create_permission(<brax.permissions.id>, <name>)` - Creates a new permission.
* `brax.delete_permission(<brax.permissions.id>)` - Deletes an existing permission.

### Verifications

* `brax.user_has_permission(<auth.user.id>, <brax.permissions.id>)` - Returns true if a user has been assigned the specified permission via a user group that they belong to. Otherwise, returns false.
* `brax.user_permissions(<auth.user.id>)` - Returns a unique result set containing the user's permissions. If the user has no permissions, returns an empty result set.
