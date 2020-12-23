# Configure RHEL/CentOS
Copyright (C) 2020 Dmitriy Prigoda deamon.none@gmail.com This script is free software: Everyone is permitted to copy and distribute verbatim copies of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License. [![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

Протестировано на:
- CentOS 8 
- Ansible = 2.9.5

## Общее описание
Набор базовых ролей ansible по настройке операционных систем RHEL/CentOS 7/8.

Для использования необходимо сделать клон проекта, настроить параметры в файле `inventory`, запустить `playbook`

```
    # Склонировать проект
    > git clone https://github.com/D34m0nN0n3/ansible-ssr-os.git
    # Перейти в директорию с проектом
    > cd ansible-ssr-os
    # Создать файл с параметрами
    > vim inventory/hosts
    # Запустить playbook
    > ansible-playbook -i inventory/hosts playbook.yml --ask-pass --become --ask-become-pass
```
## Параметры и теги
Все параметры и теги приведены в отдельных разделах документации по ролям:

1.  [infra-system.linux.os.ntp](docs/roles/ntpclient.md)
2.  [infra-system.linux.os.auditd](docs/roles/auditd.md)
3.  [infra-system.linux.os.sysconfig](docs/roles/sysconfig.md)
4.  [infra-system.linux.os.sshd](docs/roles/sshd.md)
5.  [infra-system.linux.os.cockpit](docs/roles/cockpit.md)
6.  [infra-system.linux.os.users](docs/roles/users.md)
7.  [infra-system.linux.os.mpuser](docs/roles/mpuser.md)
8.  [infra-system.linux.os.firewall](docs/roles/firewall.md)
9.  [infra-system.linux.os.selinux](docs/roles/selinux.md)
10. [infra-system.linux.os.itma](docs/roles/itma.md)
11. [infra-system.linux.os.netbackup](docs/roles/netbackup.md)
12. [infra-system.linux.os.kaspersky](docs/roles/kav.md)

## Дополнительные материалы

- [Ansible installation guide](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
- [Ansible Documentations](https://docs.ansible.com/)
- [Ansible tags](https://docs.ansible.com/ansible/latest/user_guide/playbooks_tags.html)
- [Connection methods](https://docs.ansible.com/ansible/latest/user_guide/connection_details.html)
- [Ansible passing sudo](https://8gwifi.org/docs/ansible-sudo-ssh-password.jsp)
