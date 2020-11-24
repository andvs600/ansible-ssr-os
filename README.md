


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