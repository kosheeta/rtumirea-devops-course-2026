# Учебный сервер DevOps

## Ansible

Проект находится в каталоге `ansible/`. Управляющий узел — macOS с Ansible, управляемый — отдельная ВМ Ubuntu Server в Parallels. В `inventory/bootstrap.ini` используется `student` и пароль, вводимый при запуске. В `inventory/prod.ini` используется `devops` с ключом `~/.ssh/devops_vm` и неинтерактивным sudo.

- `base` устанавливает системные зависимости и Docker Engine из официального APT-репозитория.
- `ssh` устанавливает открытый ключ, проверяет sudoers и sshd, отключает парольный вход и настраивает UFW.
- `nginx` проверяет конфигурацию перед перезагрузкой и проксирует HTTP на контейнер.
- `app` загружает образ `ghcr.io/kosheeta/devops-ci-app` по полному SHA из `group_vars/all.yml`, публикует порт только на `127.0.0.1:5000` и ожидает HTTP 200 на `/health`.

Из каталога `ansible/`:

```sh
ansible-galaxy collection install -r requirements.yml
ansible-playbook -i inventory/bootstrap.ini --ask-pass --ask-become-pass site.yml
ansible-playbook site.yml
ansible-playbook verify.yml
```

На Ubuntu 26.04 для первичного запуска со старым `ansible-core` добавьте `-e ansible_become_exe=/usr/bin/sudo.ws`, если стандартный sudo-rs меняет формат приглашения пароля. Требуется установленный `sudo.ws`. Повторный запуск через `devops` использует штатный sudo без запроса пароля.
