# Roles

Roles are all about security! Roles determine what someone is allowed or not allowed to do.

A role can be an individual user, or a group of individuals.

Roles have the ability to grant membership to another role, depending on how the role is set up.

Roles have attributes and privileges
- Attributes define privileges, but there are privileges that aren't attributes
- you can have an attribute of super user that gives you the privilege to do whatever you like, but you can give individual privileges that have nothing to do with an attribute

## Role Attribute and Creation

When a role is created, it is given certain attributes, which can determine the privileges of that role.

Attributes can be things like whether a user can create a db, create a role, whether they are a superuser or whether they can login.

Login privilege - a role with the LOGIN attribute ccan be considered the same thing as a "database user"

Superuser status - superusers bypass all permission checks

Database creation - a role must be given explicit permission to create databases

Role creation - a role must be given explicit permission to create more roles

Passord - only signifcant if given LOGIN privilege

**ALWAYS ENCRYPT WHEN STORING A ROLE THAT CAN LOGIN**
```node
CREATE ROLE readonly WITH LOGIN ENCRYPTED PASSWORD 'readonly';

// we are creating a role called `readonly` and we are giving it LOGIN ability with an encrypted password of "readonly"
```

To view roles that are available to us, use `\du` (in the command line when in postgres land)

By default, only the creator of the database or a superuser has access to its objects, anyone else needs to be granted access.