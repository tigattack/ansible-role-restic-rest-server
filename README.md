# Ansible Role: restic_rest_server

[![Build Status][build_badge]][build_link]
[![Ansible Galaxy][galaxy_badge]][galaxy_link]

Deploy restic's [Rest Server](https://github.com/restic/rest-server) in a Docker container.

Install the role: `ansible-galaxy role install tigattack.restic_rest_server`

## Requirements

None.

## Role Variables

> [!TIP]
> Once installed, you can run `ansible-doc -t role tigattack.restic_rest_server` to see role documentation.

### `restic_rest_server_version`

| Type   | Default  |
|--------|----------|
| string | `latest` |

Rest Server Docker image version. Can be `latest` or any other valid image tag (e.g. `0.13.0`).

### `restic_rest_server_container_name`

| Type   | Default       |
|--------|---------------|
| string | `rest-server` |

Rest Server container's name.

### `restic_rest_server_user`

| Type   | Default |
|--------|---------|
| string | `rest`  |

Username for Rest Server authentication.

### `restic_rest_server_password`

| Type   | Default |
|--------|---------|
| string |         |

Password for Rest Server authentication. This variable has no default value; you must specify one.

### `restic_rest_server_port`

| Type   | Default |
|--------|---------|
| string | `8000`  |

The host port on which to expose Rest Server.

### `restic_rest_server_options`

| Type         | Default |
|--------------|---------|
| list[string] | `[]`    |

Additional options to pass to Rest Server.

See [restic/rest-server/README#usage](https://github.com/restic/rest-server/blob/master/README.md#usage) for available options.

### `restic_rest_server_data_path`

| Type   | Default                                    |
|--------|--------------------------------------------|
| path   | `/opt/<restic_rest_server_container_name>` |

Rest Server data path on the host. The generated htpasswd file is stored in this path.

By default, this is set to `/opt/` followed by the name of the container.

For example:
* If both `restic_rest_server_container_name` and `restic_rest_server_data_path` are left default, the path would be `/opt/rest-server`.
* If `restic_rest_server_container_name` is set to `foo_bar` and `restic_rest_server_data_path` is left default, the path would be `/opt/foo_bar`.

### `restic_rest_server_backup_path`

| Type   | Default                                  |
|--------|------------------------------------------|
| path   | `<restic_rest_server_data_path>/backups` |

Rest Server backup destination path on the host.

By default, this is set to the data path described above (`restic_rest_server_data_path`) followed by `/backups`.

### `restic_rest_server_certificates_path`

| Type   | Default                                |
|--------|----------------------------------------|
| path   | `<restic_rest_server_data_path>/certs` |

Rest Server certificates path on the host.

By default, this is set to the data path described above (`restic_rest_server_data_path`) followed by `/certs`.

This is only relevant if TLS options are defined in `restic_rest_server_options`.

## Dependencies

* [community.docker](https://galaxy.ansible.com/ui/repo/published/community/docker/) Ansible collection
* [community.general](https://galaxy.ansible.com/ui/repo/published/community/general/) Ansible collection

## Example Playbooks

**Bare Minimum:**

```yml
---
- name: Deploy restic Rest Server
  hosts: server
  roles:
    - role: tigattack.restic_rest_server
      vars:
        restic_backup_server_password: _!_CHANGE_ME_!_
```

**With Prometheus metrics endpoint enabled without authentication:**

```yml
---
- name: Deploy restic Rest Server
  hosts: server
  roles:
    - role: tigattack.restic_rest_server
      vars:
        restic_backup_server_password: _!_CHANGE_ME_!_
        restic_backup_server_options:
          - --prometheus
          - --prometheus-no-auth
```

**With TLS using a Let's Encrypt certificate and a custom port:**

```yml
---
- name: Deploy restic Rest Server
  hosts: server
  roles:
    - role: tigattack.restic_rest_server
      vars:
        restic_backup_server_password: _!_CHANGE_ME_!_
        restic_backup_server_port: '8080'
        restic_backup_server_certificates_path: /etc/letsencrypt
        restic_backup_server_options:
          - --tls
          - --tls-cert /certs/live/my_domain/fullchain.pem
          - --tls-key /certs/live/my_domain/privkey.pem
```

## License

MIT

[build_badge]:  https://img.shields.io/github/actions/workflow/status/tigattack/ansible-role-restic-rest-server/test.yml?branch=main&label=Lint%20%26%20Test
[build_link]:   https://github.com/tigattack/ansible-role-restic-rest-server/actions?query=workflow:Test
[galaxy_badge]: https://img.shields.io/ansible/role/d/tigattack/restic-rest-server
[galaxy_link]:  https://galaxy.ansible.com/tigattack/restic-rest-server
