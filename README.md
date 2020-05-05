# restore

The purpose of this role is to restore objects to your system.

|Travis|GitHub|Quality|Downloads|
|------|------|-------|---------|
|[![travis](https://travis-ci.com/robertdebock/ansible-role-restore.svg?branch=master)](https://travis-ci.com/robertdebock/ansible-role-restore)|[![github](https://github.com/robertdebock/ansible-role-restore/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-restore/actions)|[![quality](https://img.shields.io/ansible/quality/29918)](https://galaxy.ansible.com/robertdebock/restore)|[![downloads](https://img.shields.io/ansible/role/d/29918)](https://galaxy.ansible.com/robertdebock/restore)|

## Example Playbook

This example is taken from `molecule/resources/converge.yml` and is tested on each push, pull request and release.
```yaml
---
- name: Converge
  hosts: all
  become: yes
  gather_facts: yes

  roles:
    - role: robertdebock.restore
```

The machine may need to be prepared using `molecule/resources/prepare.yml`:
```yaml
---
- name: Prepare
  hosts: all
  gather_facts: no
  become: yes

  roles:
    - role: robertdebock.bootstrap
    - role: robertdebock.core_dependencies
```

For verification `molecule/resources/verify.yml` run after the role has been applied.
```yaml
---
- name: Verify
  hosts: all
  become: yes
  gather_facts: yes

  pre_tasks:
    - name: create a directory to backup
      file:
        path: /home/test
        state: directory

  roles:
    - role: robertdebock.mysql

      mysql_databases:
        - name: test_db
          encoding: utf8
          collation: utf8_bin
      mysql_users:
        - name: restore
          password: rest0re
          priv: "test_db.*:ALL"
    - role: robertdebock.backup
      backup_format: zip
      backup_objects:
        - name: home
          type: directory
          source: /home
        - name: test_db
          type: mysql
          source: test_db
          format: zip
    - role: robertdebock.restore
      restore_mysql_username: restore
      restore_mysql_password: rest0re
      restore_objects:
        - name: home
          type: directory
          destination: /home
        - name: test_db
          type: mysql
          destination: test_db
```

Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

## Role Variables

These variables are set in `defaults/main.yml`:
```yaml
---
# defaults file for restore

# What directory are the objects to be restored saved?
restore_directory: backups

# The directory of objects which need to be placed on the managed node
# temporarily.
restore_remote_directory: /tmp

# A list of objects to restore.
# Each list item should have:
# - name to find the object locally.
#   type, either directory or mysql.
#   destination, a directory where to unpack the object.
#
# Nota bene; the [backup role](http://galaxy.ansible.com/robertdebock/backup) can be used to create restorable objects. The objects created with this role include the parent directory, so the destination mentioned here ~misses~ the last part of the directory

# Credentials to login to the mysql database, only require when restoring mysql
# objects.
restore_mysql_username: ""
restore_mysql_password: ""

restore_objects:
  - name: varspool
    type: directory
    destination: /var
#  - name: drupal
#    type: mysql
#    destination: drupal
```

## Requirements

- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the current, previous and next release of Ansible.)

The following roles can be installed to ensure all requirements are met, using `ansible-galaxy install -r requirements.yml`:

```yaml
---
- robertdebock.backup
- robertdebock.bootstrap
- robertdebock.core_dependencies
- robertdebock.mysql

```

## Context

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/drawings/artifacts/restore.png "Dependency")

## Compatibility

This role has been tested on these [container images](https://hub.docker.com/):

|container|tags|
|---------|----|
|debian|buster|
|el|7, 8|
|fedora|all|
|opensuse|all|
|ubuntu|all|

The minimum version of Ansible required is 2.8 but tests have been done to:

- The previous version, on version lower.
- The current version.
- The development version.

## Exceptions

Some variarations of the build matrix do not work. These are the variations and reasons why the build won't work:

| variation                 | reason                 |
|---------------------------|------------------------|
| amazonlinux:1 | Testing mysql fails, because a dependecy (ansible-role-mysql) is not met. |
| alpine | Testing mysql fails, because a dependecy (ansible-role-mysql) is not met. |


## Testing

[Unit tests](https://travis-ci.com/robertdebock/ansible-role-restore) are done on every commit, pull request, release and periodically.

If you find issues, please register them in [GitHub](https://github.com/robertdebock/ansible-role-restore/issues)

Testing is done using [Tox](https://tox.readthedocs.io/en/latest/) and [Molecule](https://github.com/ansible/molecule):

[Tox](https://tox.readthedocs.io/en/latest/) tests multiple ansible versions.
[Molecule](https://github.com/ansible/molecule) tests multiple distributions.

To test using the defaults (any installed ansible version, namespace: `robertdebock`, image: `fedora`, tag: `latest`):

```
molecule test

# Or select a specific image:
image=ubuntu molecule test
# Or select a specific image and a specific tag:
image="debian" tag="stable" tox
```

Or you can test multiple versions of Ansible, and select images:
Tox allows multiple versions of Ansible to be tested. To run the default (namespace: `robertdebock`, image: `fedora`, tag: `latest`) tests:

```
tox

# To run CentOS (namespace: `robertdebock`, tag: `latest`)
image="centos" tox
# Or customize more:
image="debian" tag="stable" tox
```

## License

Apache-2.0


## Author Information

[Robert de Bock](https://robertdebock.nl/)

Please consider [sponsoring me](https://github.com/sponsors/robertdebock).
