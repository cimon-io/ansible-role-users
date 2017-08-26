# Ansible users role

An ansible role for managing user and group accounts. The role includes the following tasks:

1. Create groups specified by `users_groups`.
2. Set sudo rights for groups where necessary.
3. Create users from the `users_accounts` array with specified parameters.
4. Add users to groups.
5. Authorize users to login via SSH.

This role can be run under all versions of Ubuntu and Debian.

## Requirements

None

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

```yaml
users_accounts: []    # An array of user accounts
users_groups: []      # An array of user groups
```

### User accounts

Each user account can include some of the following parameters. The only required parameter is `name`:

```yaml
users_accounts:
  - name: ""                # A user name
```

To specify a primary group for an account use a `group` parameter. To put a user to a list of groups, use `groups` value as a string of group names separated with a comma. For ansible 2.2 version and below this is the only allowed format for several groups. Now it is also possible to use YAML lists. When the parameter is set to an empty string (`groups=`), the user is removed from all groups except the primary group.

```yaml
    group:                  # A name of the primary group to which the user belongs
    groups:                 # A string of the user group names separated with a comma
    append: yes             # If 'yes', will only add groups, not set them to just the list in 'groups'
```

You are able to set if a home directory should be created for the user when the account is created or if the home directory does not exist with a parameter `createhome`:

```yaml
    createhome: yes         # Set `no` if a home directory should not be created
    home:                   # The user's home directory
```

To add an SSH authorized key and a password set corresponding parameters. The possible values of `update_password` are `always` and `on_create`. 'Always' will update passwords if they differ; 'on_create' will only set the password for created users.

```yaml
    key:                    # The SSH public key as a string or (since 1.9) url
    generate_ssh_key: no    # Whether to generate a SSH key for the user (will not overwrite an existing SSH key)
    password:               # The user's password
    update_password:
```

The `state` parameter allows to manage a user account depending on whether it exists or not. If it's `present`, a new user will be created. If the value is `absent`, the user will be removed. The action is taken only when the current account state is different from what is set:

```yaml
    state: present          # Create the account if it is absent
```

Configure some other optional parameters if necessary:

```yaml
    shell: /bin/bash        # The user's shell
    comment: ""             # A description of the user account
    expires:                # An expiry time for the user
    system: no              # Create the user account as system (cannot be changed for existing users)
```

### User groups

User groups should be specified by parameters:

```yaml
users_groups:
  - name: admin                       # A group name
    state: present                    # Create a group if it is absent
    sudo: "ALL=(ALL) NOPASSWD:ALL"    # Set rights to the group
```

## Dependencies

None

## Example Playbook

```yaml
- hosts: all
  roles:
    - role: users
  vars_files:
    - vars/users.yml
```

Inside `vars/users.yml`:

```yaml
# Specify some groups:
users_groups:
  - name: admin
    sudo: "ALL=(ALL) NOPASSWD:ALL"
  - name: deploy
  - name: developer
    sudo: "ALL=(ALL) NOPASSWD:/bin/ls, /bin/cat, /bin/more, /bin/grep, /usr/bin/head, /usr/bin/tail, /usr/bin/less"
```

```yaml
# Specify user accounts:
users_accounts:
  - name: deploy
    group: deploy
    comment: Deploy user

  - name: alex
    groups: 'deploy,admin'
    comment: alexey@gmail.com
    key: "{{ lookup('file', 'files/public_keys/alex.pub') }}"

  - name: jack
    groups: 'deploy,admin'
    comment: jackson@mail.com
    key: "{{ lookup('file', 'files/public_keys/jack.pub') }}"

  - name: pavel
    groups: 'deploy,developer'
    comment: pavel@example.com
    key: "{{ lookup('file', 'files/public_keys/pavel.pub') }}"
```

where `files/public_keys/alex.pub` is a user public key.

## License

Licensed under the [MIT License](https://opensource.org/licenses/MIT).
