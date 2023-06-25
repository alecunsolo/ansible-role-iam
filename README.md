[![build status](https://github.com/alecunsolo/ansible-role-iam/actions/workflows/ci.yml/badge.svg)](https://github.com/alecunsolo/ansible-role-iam/actions/workflows/ci.yml)
[![pre-commit](https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit)](https://github.com/pre-commit/pre-commit)

Ansible Role: iam
=========

A role to manage basic user configuration for my homelab.

Requirements
------------

The `passlib` library must be installed on the controller to hash clear text passwords.

Role Variables
--------------

```yaml
regular_user_name:  # Mandatory
```
The username of the regular user.
```yaml
regular_user_password:  # Mandatory
password_salt:  # Mandatory
```
The clear text password of the regular user. The password is hashed with `passlib`: `password_hash('sha512', password_salt)`. To keep the hash consistent a salt must be provided.
```yaml
regular_user_shell: /bin/bash
```
The shell of the regular user.

```yaml
regular_user_import_authorized_keys: true
regular_user_github_username:  # Omit
```
Whether to import the regular user public ssh keys from github or not.An additional variable is provided in case the github username is different from the OS one.
```yaml
automation_user_name:  # Mandatory
automation_user_home:  # Omit
```
The username of the automation user. The path of the automation user home can be provided but is not mandatory. The role will try to move the home if needed.
```yaml
automation_user_authorized_keys: []
```
The list of the public ssh keys to add to the automation user `authorized_keys`.

```yaml
sudoers_remove: []
```
List of the boostrap files to remove from the `/etc/sudoers.d` directory.

Dependencies
------------

The role use the `ansible.posix.authorized_keys` from the `ansible.posix` collection.

Example Playbook
----------------

```yaml
- name: Converge
  hosts: all
  vars:
    automation_user_name: ansible
    regular_user_github_username: alecunsolo
    regular_user_name: testuser
    regular_user_password: password
    password_salt: zFNLFngTazUv
    automation_user_home: /var/lib/{{ automation_user_name }}
    automation_user_authorized_keys:
      - "{{ lookup('file', 'test_data/id_e25519_test.pub') }}"
  tasks:
    - name: Include iam.
      ansible.builtin.include_role:
        name: alecunsolo.iam
```

License
-------

MIT

Notes
-----

Testing with molecule (including the docker images used) is ~~stolen from~~ heavily inspired by [Jeff Geerling](https://www.jeffgeerling.com/). Watch [his video](https://youtu.be/FaXVZ60o8L8) (and the other ones as well).
