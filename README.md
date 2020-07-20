# Ansible flokoe.users role

Ansible role to manage users, groups, authorized keys and sudo entries.

**Attention: This role is in development stage.**

**Why another ansible user role?**

There are many roles to manage users on Galaxy, so why write another one?  
Well, simply put, they did not satisfy my standards, were missing some features or were to complicated.

## Installation

Using `ansible-galaxy`:

```bash
ansible-galaxy install flokoe.users
```

Using `requirements.yml`:

```yaml
- src: flokoe.users
```

## Requirements

None

## Role Configuration

| Parameter                           | Defaults      | Description                                                                                                                                                               |
| ----------------------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `use_global_users_group`            | `false`       | Set to true if you want to use a global users group as primary users group instead of creating an individual primary group for each user (same name as username).         |
| `users_group.name`                  | `'users'`     | Name of global users group.                                                                                                                                               |
| `users_group.gid`                   | None          | GID of global users group. Don't change this blindly.                                                                                                                     |
| `keep_existing_groups`              | `false`       | Default behavior of 'append' is to use groups from 'groups' exclusively. Set to true to keep existing groups. If set to false this setting can still be set individually. |
| `home_base_path`                    | `'/home/'`    | Home base directory.                                                                                                                                                      |
| `default_shell`                     | `'/bin/bash'` | Default shell.                                                                                                                                                            |
| `default_ssh_key_bits`              | `2048`        | Default SSH key bits.                                                                                                                                                     |
| `default_ssh_key_type`              | `'rsa'`       | Default SSH key type.                                                                                                                                                     |
| `skeleton_path`                     | None          | Home skeleton directory.                                                                                                                                                  |
| `default_home_mode`                 | `'755'`       | Default home directory permissions.                                                                                                                                       |
| `default_authorized_keys_exclusive` | `false`       | Default value if authorized_keys should be managed exclusively.                                                                                                           |

### Managing groups

Add a `group_list` variable containing the list of groups to add.

The following variables can be used to create a group:

| Parameter         | Defaults    | Description                                                      |
| ----------------- | ----------- | ---------------------------------------------------------------- |
| `name` *required* | None        | Name of group to create.                                         |
| `gid`             | None        | Group ID, will be determined automatically if omitted.           |
| `state`           | `'present'` | State of the group. Set to `absent` to remove group from system. |

#### Group example

```yaml
group_list:
  - name: group1
    gid: 1337
    state: absent
```

### Managing users

Add a `user_list` variable containing the list of users to add.
<!-- A good place to put this is in group_vars/all or group_vars/groupname if you only want the users to be on certain machines. -->

The following variables can be used to create a user:

| Parameter                   | Defaults                                     | Description                                                                                                                                                                       |
| --------------------------- | -------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `name` *required*           | None                                         | The username.                                                                                                                                                                     |
| `comment`                   | None                                         | The full name or description of the User (GECOS field).                                                                                                                           |
| `uid`                       | None                                         | User ID, will be determined automatically if omitted.                                                                                                                             |
| `group`                     | `name`                                       | Primary group of user. Will use `name` of user by default. Uses `users_group.name` if `use_global_users_group` is set to true.                                                    |
| `groups`                    | None                                         | List of groups the user should be added to.                                                                                                                                       |
| `append`                    | `false`                                      | Use groups in `groups` exclusively. Set to true to keep existing groups. See `keep_existing_groups` for global setting.                                                           |
| `home`                      | `home_base_path + name`                      | Path to home directory of user.                                                                                                                                                   |
| `create_home`               | `true`                                       | Create home directory.                                                                                                                                                            |
| `home_mode`                 | `default_home_mode`                          | Sets users's home directory permissions. Uses permissions specified in `default_home_mode` by default.                                                                            |
| `home_owner`                | `name`                                       | Sets owner of home directory. Will use `name` of user by default.                                                                                                                 |
| `home_group`                | `name`                                       | Sets group of home directory. Will use `name` of user by default. Uses `users_group.name` if `use_global_users_group` is set to true or `group` if specified.                     |
| `move_home`                 | `false`                                      | Attempt to move the user's old home directory to the specified directory if it isn't there already and the old home exists.                                                       |
| `skeleton`                  | None                                         | Home skeleton directory. Uses `skeleton_path` if defined.                                                                                                                         |
| `password`                  | None                                         | Password of User. Needs to be an encrypted value.                                                                                                                                 |
| `non_unique`                | `false`                                      | Allow non unique UIDs.                                                                                                                                                            |
| `expires`                   | None                                         | An expiry time for the user in epoch. You can remove the expiry time by specifying a negative value.                                                                              |
| `shell`                     | `default_shell`                              | Sets shell of user. Uses shell specified in `default_shell` by default.                                                                                                           |
| `generate_ssh_key`          | `false`                                      | Generate private SSH key for user.                                                                                                                                                |
| `ssh_key_bits`              | `default_ssh_key_bits`                       | Sets bit length for SSH key. Uses value specified in `default_ssh_key_bits` by default.                                                                                           |
| `ssh_key_passphrase`        | None                                         | Sets password for SSH key. If left blank, SSH key will have no password.                                                                                                          |
| `ssh_key_type`              | `default_ssh_key_type`                       | Sets key type of SSH key.  Uses value specified in `default_ssh_key_type` by default.                                                                                             |
| `ssh_key_comment`           | `'ansible-generated for $USER on $HOSTNAME'` | Sets comment for SSH key.                                                                                                                                                         |
| `authorized_keys_exclusive` | `default_authorized_keys_exclusive`          | If authorized_keys file should be managed exclusively. Uses value of `default_authorized_keys_exclusive` by default.                                                              |
| `authorized_keys`           | None                                         | List of keys. Item can be a string (entire key: options, key, comment) or reading a key file: `"{{ lookup('file', 'key_file.pub') }}"`.                                           |
| `system`                    | `false`                                      | When creating an account makes the user a system account. This setting cannot be changed on existing users.                                                                       |
| `remove`                    | `false`                                      | This only affects `state=absent`, removes home directory and mail spool.                                                                                                          |
| `force`                     | `false`                                      | This only affects `state=absent`, it forces removal of the user and associated directories. When used with `generate_ssh_key=true` this forces an existing key to be overwritten. |
| `state`                     | `'present'`                                  | Defines the state of the user. Use `absent` to remove user from system.                                                                                                           |

### User example

```yaml
user_list:
  - name: hjanmaat
    comment: Hein Janmaat
    uid: 1337
    group: hjanmaat
    groups:
      - developers
      - adm
    append: true
    home: /home/hjanmaat
    create_home: true
    home_mode: 700
    home_owner: root
    home_group: root
    move_home: false
    skeleton: local_skeleton_dir
    password: >-
      $6$mysecretsalt$qJbapG68nyRab3gxvKWPUcs2g3t0oMHSHMnSKecYNpSi3CuZm.GbBqXO8BE6EI6P1JUefhA0qvD7b5LSh./PU1
    non_unique: false
    expires: 31556926
    shell: /bin/bash
    generate_ssh_key: true
    ssh_key_bits: 4096
    ssh_key_passphrase: very_secret_secret
    ssh_key_type: rsa
    ssh_key_comment: a comment for ssh key
    default_authorized_keys_exclusive: true
    authorized_keys:
      - 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDGDKKpFlYiNPE7yo/sxcfyTw7Lah5BV2RCvyj2xrkNkUIhbIcLJTaSrawuF/+5wR/qcs3SmEU+5OvdvUohWPQCmaDOWz5RDGiAy1HVh9msim5zeMARkfloBoDyclqmjsULSyEeBeuiiNMfjxacd/2XjPZ7Th7uiyxoguI5F6EWXpsDu4dAAZITjPMXoktG5Zf2no7L/y0+Z49xwoTE59+kJ1gL5XYt8uLMTI53LUL0fcOGoNttG15adj46XriHsB/RsMjetb7br/q1DJnDuIxE7E/Gp/89ZMM0ugkNXx8AzZTmXT25QQ2GvhzMB6TMor12S0BMoQP/lObYx3/VzuxZ key1'
      - "{{ lookup('file', 'key2.pub') }}"
    system: false
    remove: false
    force: false
    state: present
```

## Dependencies

None

## Example Playbook

```yaml
---

- name: Playbook
  hosts: all

  roles:
    - flokoe.users
```

## Contributing

In lieu of a formal style guide, take care to maintain the existing coding style and follow these general guidelines:

1. Fork it
2. Create your topic branch
3. Write and add proper tests
4. Add and commit your new functionality or fixes
5. Push to Github
6. Create new Pull Request

### Branches

Choosing a proper name for a branch helps us identify its purpose and possibly find an associated bug or feature. Generally a branch name should include a topic such as fix or feature followed by a description and an issue number if applicable. Branches should have only changes relevant to a specific issue.

```bash
git checkout -b fix/service-template-typo-1234
git checkout -b feature/config-handling-1235
```

## Testing

If you add new functionality, make sure to write appropriate tests as well.

To install all required test dependencies in a local development environment you can execute the following:

```bash
# If not existent
python3 -m venv env

source env/bin/activate

pip3 install --upgrade -r requirements.txt
```

Run all tests:

```bash
molecule test
```

## Author Information

Florian Köhler

## License

This project is under the MIT License. See the [LICENSE](LICENSE) file for the full license text.
