filebeat role
=========

Роль для установки elasticsearch на хостах с ОС: Debian, Ubuntu, CentOS, RHEL.

Requirements
------------

Поддерживаются только ОС семейств debian и EL.

Role Variables
--------------

| Variable name | Default | Description |
|-----------------------|----------|-------------------------|
| Filebeat_version | "7.16.3" | Параметр, который определяет какой версии filebeat будет установлен |

Example Playbook
----------------

    - hosts: all
      roles:
         - { role: mnt-homeworks-ansible }

License
-------

BSD

Author Information
------------------
Alex Pazdnkov DevOps-9

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
