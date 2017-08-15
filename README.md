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
  - group: admin
    sudo: "ALL=(ALL) NOPASSWD:ALL"

removed_user_groups:
  - group: testgroup
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
    groups: "www-data,admin"
    comment: john.doe@domain.tld
    key: "{{ lookup('file', 'files/public_keys/john.pub') }}"
user_groups:
  - group: admin
    sudo: "ALL=(ALL) NOPASSWD:ALL"
```

where `files/public_keys/john.pub` is a user public key.

License
-------

MIT
