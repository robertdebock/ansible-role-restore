restore
=========

[![Build Status](https://travis-ci.org/robertdebock/ansible-role-restore.svg?branch=master)](https://travis-ci.org/robertdebock/ansible-role-restore)

Provides restore for your system.

[Unit tests](https://travis-ci.org/robertdebock/ansible-role-restore) are done on every commit and periodically.

If you find issues, please register them in [GitHub](https://github.com/robertdebock/ansible-role-restore/issues)

To test this role locally please use [Molecule](https://github.com/metacloud/molecule):
```
pip install molecule
molecule test
```
There are many scenarios available, please have a look in the `molecule/` directory.

Context
--------
This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

The "other part" of this role is the [backup role](https://galaxy.ansible.com/robertdebock/backup). Here is an example of the two working together.

backup.yml:
```
- name: backup
  hosts: all:!localhost

  roles:
    - role: robertdebock.backup
      backup_objects:
        - name: home
          type: directory
          source: /home
          exclude_path: /home/vagrant
        - name: mydatabase
          type: mysql
          source: mydatabase
```

restore.yml:
```
- name: restore
  hosts: all:!localhost

  roles:
    - robertdebock.restore
      restore_objects:
        - name: home
          type: directory
          destination: /
          exclude /vagrant
        - name: mydatabase
          type: mysql
          destination: mydatabase
```

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/drawings/artifacts/restore.png "Dependency")

Requirements
------------

- A system installed with required packages to run Ansible. Hint: [bootstrap](https://galaxy.ansible.com/robertdebock/bootstrap).
- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the last 3 release of Ansible.)

Role Variables
--------------

- restore_parameter: Description of values. [default: value]

Dependencies
------------

- None known.

Compatibility
-------------

This role has been tested against the following distributions and Ansible version:

|distribution|ansible 2.4|ansible 2.5|ansible 2.6|
|------------|-----------|-----------|-----------|
|alpine-edge|yes|yes|yes|
|alpine-latest|yes|yes|yes|
|archlinux|yes|yes|yes|
|centos-6|yes|yes|yes|
|centos-latest|yes|yes|yes|
|debian-latest|yes|yes|yes|
|debian-stable|yes|yes|yes|
|fedora-latest|yes|yes|yes|
|fedora-rawhide|yes|yes|yes|
|opensuse-leap|yes|yes|yes|
|opensuse-tumbleweed|yes|yes|yes|
|ubuntu-artful|yes|yes|yes|
|ubuntu-latest|yes|yes|yes|

Example Playbook
----------------

```
---
- name: restore
  hosts: all
  gather_facts: no
  become: yes

  roles:
    - role: robertdebock.bootstrap
    - role: robertdebock.restore
      restore_objects:
        - name: home
          type: directory
          destination: /
```

Nota bene: This role is not idempotent, because it's a list of actions, not a state.

To install this role:
- Install this role individually using `ansible-galaxy install robertdebock.restore`

Sample roles/requirements.yml: (install with `ansible-galaxy install -r roles/requirements.yml
```
---
- name: robertdebock.bootstrap
- name: robertdebock.restore
```
ate.

License
-------

Apache License, Version 2.0

Author Information
------------------

[Robert de Bock](https://robertdebock.nl/) <robert@meinit.nl>
