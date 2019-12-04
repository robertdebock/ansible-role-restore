restore
=========

<img src="https://docs.ansible.com/ansible-tower/3.2.4/html_ja/installandreference/_static/images/logo_invert.png" width="10%" height="10%" alt="Ansible logo" align="right"/>
<a href="https://travis-ci.org/robertdebock/ansible-role-restore"> <img src="https://travis-ci.org/robertdebock/ansible-role-restore.svg?branch=master" alt="Build status"/></a> <img src="https://img.shields.io/ansible/role/d/29918"/> <img src="https://img.shields.io/ansible/quality/29918"/>

The purpose of this role is to restore objects to your system.

Example Playbook
----------------

This example is taken from `molecule/resources/playbook.yml` and is tested on each push, pull request and release.
```yaml
---
- name: Converge
  hosts: all
  become: yes
  gather_facts: yes

  roles:
    - robertdebock.restore
```

The machine you are running this on, may need to be prepared, I use this playbook to ensure everything is in place to let the role work.
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

After running this role, this playbook runs to verify that everything works, this may be a good example how you can use this role.
```yaml
---
- name: Verify
  hosts: all
  become: yes
  gather_facts: yes

  roles:
    - role: {{ galaxy_namespace }}.mysql
      mysql_databases:
        - name: test_db
          encoding: utf8
          collation: utf8_bin
    - role: {{ galaxy_namespace }}.backup
      backup_format: tar
      backup_objects:
        - name: home
          type: directory
          source: /home
        - name: test_db
          type: mysql
          source: test_db
          format: zip
    - role: {{ galaxy_namespace }}.restore
      restore_objects:
        - name: home
          type: directory
          destination: /home
        - name: test_db
          type: mysql
          destination: test_db

```

Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

Role Variables
--------------

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

restore_objects:
  - name: varspool
    type: directory
    destination: /var
#  - name: drupal
#    type: mysql
#    destination: drupal
```

Requirements
------------

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

Context
-------

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/drawings/artifacts/restore.png "Dependency")


Compatibility
-------------

This role has been tested on these [container images](https://hub.docker.com/):

|container|tag|allow_failures|
|---------|---|--------------|
|amazonlinux|latest|no|
|debian|unstable|yes|
|debian|latest|no|
|centos|7|no|
|centos|latest|no|
|fedora|latest|no|
|fedora|rawhide|yes|
|opensuse|latest|no|
|ubuntu|latest|no|

This role has been tested on these Ansible versions:

- ansible>=2.8, <2.9
- ansible>=2.9
- git+https://github.com/ansible/ansible.git@devel

Exceptions
----------

Some variarations of the build matrix do not work. These are the variations and reasons why the build won't work:

| variation                 | reason                 |
|---------------------------|------------------------|
| amazonlinux:1 | Testing mysql fails, because a dependecy (ansible-role-mysql) is not met. |
| alpine | Testing mysql fails, because a dependecy (ansible-role-mysql) is not met. |


Testing
-------

[Unit tests](https://travis-ci.org/robertdebock/ansible-role-restore) are done on every commit, pull request, release and periodically.

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

Modules
-------

This role uses the following modules:
```yaml
---
- copy
- file
- find
- include_tasks
- mysql_db
- set_fact
- unarchive
```

License
-------

Apache-2.0


Author Information
------------------

[Robert de Bock](https://robertdebock.nl/)
