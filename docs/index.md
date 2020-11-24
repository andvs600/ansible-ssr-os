# Общее описание
Данная документация описывает параметры при использование Ansible Roles для базовой настройки серверов на базе операционных систем Red Hat. Роли содержат сценарии по автоматизации ручных операций. За счет автоматизации уменьшаются время и трудозатраты на подготовку ПТК, снижаться риск влияния “человеческого фактора”.

!!! tip
    Для создания закладки в браузере на данное руководство: ++ctrl+d++

## Структура проекта

blockdiag {
  orientation = portrait

  Roles -> Auditd;
  Roles -> Cockpit;
  Roles -> Firewall;
  Roles -> NTPclient;
  Roles -> SELinux;
  Roles -> SSHD;
  Roles -> Sysconfig;
  Roles -> Users account;
  Roles -> ITMA;
  Roles -> NetBackup;
  Roles -> MPuser;
}

## Дополнительные материалы

- [Ansible installation guide](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
- [Ansible Documentations](https://docs.ansible.com)
- [Ansible tags](https://docs.ansible.com/ansible/latest/user_guide/playbooks_tags.html)
- [Connection methods](https://docs.ansible.com/ansible/latest/user_guide/connection_details.html)
- [Ansible passing sudo](https://8gwifi.org/docs/ansible-sudo-ssh-password.jsp)