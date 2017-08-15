Users role
=========

An Ansible Role for managing user and group accounts.

Requirements
------------

None.

Role Variables
--------------

Available variables are listed below, along with default values (see `defaults/main.yml`):

```yaml
users:
   - name: ""
     group:
     groups:          # comma separated string, ansible < 2.3
     append: no
     home:
     createhome: yes
     shell:
     comment: ""
     key:
     password:
     update_password:
     expires:
     system: no
     generate_ssh_key: no

removed_users:
   - testuser

user_groups:
  - name: admin
    sudo: "ALL=(ALL) NOPASSWD:ALL"

removed_user_groups:
  - name: testgroup
```

Dependencies
------------

None.

Example Playbook
----------------

```yaml
- hosts: all
  roles:
    - role: users
  vars_files:
    - vars/users.yml
```

Inside `vars/users.yml`:

```yaml
users:
  - name: john
    groups: "admin"
    comment: john.doe@domain.tld
    key: "{{ lookup('file', 'files/public_keys/john.pub') }}"
  - name: pavel
    groups: "developer"
    comment: pavel@domain.tld
    key: "{{ lookup('file', 'files/public_keys/pavel.pub') }}"
user_groups:
  - name: admin
    sudo: "ALL=(ALL) NOPASSWD:ALL"
  - name: developer
    sudo: "ALL=(ALL) NOPASSWD:/bin/ls, /bin/cat, /bin/more, /bin/grep, /usr/bin/head, /usr/bin/tail, /usr/bin/less"
```

where `files/public_keys/john.pub` is a user public key.

License
-------

MIT
