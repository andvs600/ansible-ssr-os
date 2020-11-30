# Общее описание
Данная документация описывает параметры при использование Ansible Roles для базовой настройки серверов на базе операционных систем Red Hat. Роли содержат сценарии по автоматизации ручных операций. За счет автоматизации уменьшаются время и трудозатраты на подготовку ПТК, снижаться риск влияния “человеческого фактора”.

## Название ролей

* infra-system.linux.os.auditd
* infra-system.linux.os.cockpit
* infra-system.linux.os.firewall
* infra-system.linux.os.itma
* infra-system.linux.os.mpuser
* infra-system.linux.os.netbackup
* infra-system.linux.os.ntpclient
* infra-system.linux.os.selinux
* infra-system.linux.os.sshd
* infra-system.linux.os.sysconfig
* infra-system.linux.os.users


!!! hint
    Найти проект можно в поиске по имени [D34m0nN0n3/ansible-ssr-os](https://gitlab-lb-01.gvc.oao.rzd/d34m0nn0n3/ansible-ssr-os) на [GitLab](https://git.rzd/).

!!! tip
    Для создания закладки в браузере на данное руководство: ++ctrl+d++

## Дополнительные материалы

- [Ansible installation guide](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
- [Ansible Documentations](https://docs.ansible.com)
- [Ansible tags](https://docs.ansible.com/ansible/latest/user_guide/playbooks_tags.html)
- [Connection methods](https://docs.ansible.com/ansible/latest/user_guide/connection_details.html)
- [Ansible passing sudo](https://8gwifi.org/docs/ansible-sudo-ssh-password.jsp)
