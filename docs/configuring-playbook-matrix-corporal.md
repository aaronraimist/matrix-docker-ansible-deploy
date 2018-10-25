# Setting up Matrix Corporal

The playbook can install and configure [matrix-corporal](https://github.com/devture/matrix-corporal) for you.

See that project's documentation to learn what it does and why it might be useful to you.

If you decide that you'd like to let this playbook install it for you, you'd need to also [set up the Shared Secret Auth password provider module](configuring-playbook-shared-secret-auth.md).


## Playbook configuration
You would then need some configuration like this:

```yaml
matrix_corporal_enabled: true

matrix_corporal_policy_provider_config: |
  {
    "Type": "http",
    "Uri": "https://intranet.example.com/matrix/policy",
    "AuthorizationBearerToken": "SOME_SECRET",
    "CachePath": "/var/cache/matrix-corporal/last-policy.json",
    "ReloadIntervalSeconds": 1800
  }

# If you also want to enable Matrix Corporal's HTTP API..
matrix_corporal_http_api_enabled: true
matrix_corporal_http_api_auth_token: "AUTH_TOKEN_HERE"

# If you need to change the reconciliator user's id from the default (matrix-corporal)..
# In any case, you need to make sure this Matrix user is created on your server.
matrix_corporal_reconciliation_user_id_local_part: "matrix-corporal"
```

Matrix Corporal operates with a specific Matrix user on your server.
By default, it's `matrix-corporal` (controllable by the `matrix_corporal_reconciliation_user_id_local_part` setting, see above).
No matter what Matrix user id you configure to run it with, make sure that:

- the Matrix Corporal user is created by [registering it](registering-users.md). Use a password you remember, as you'll need to log in from time to time to create or join rooms

- the Matrix Corporal user is joined and has Admin/Moderator-level access to any rooms you want it to manage


## Matrix Corporal files

The following local filesystem paths are mounted in the `matrix-corporal` container and can be used in your configuration (or policy):

- `/matrix/corporal/config` is mounted at `/etc/matrix-corporal` (read-only)

- `/matrix/corporal/var` is mounted at `/var/matrix-corporal` (read and write)

- `/matrix/corporal/cache` is mounted at `/var/cache/matrix-corporal` (read and write)

As an example: you can create your own configuration files in `/matrix/corporal/config` and they will appear in `/etc/matrix-corporal` in the Docker container. Your configuration (stuff in `matrix_corporal_policy_provider_config`) needs to refer to these files via the local container path `/etc/matrix-corporal`