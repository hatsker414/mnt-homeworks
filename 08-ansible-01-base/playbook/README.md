# Самоконтроль выполненения задания

1. Где расположен файл с `some_fact` из второго пункта задания?
    1. group_vars/all/examp.yml
2. Какая команда нужна для запуска вашего `playbook` на окружении `test.yml`?
    1. ansible-playbook site.yml -i inventory/test.yml
3. Какой командой можно зашифровать файл?
    1. ansible-vault encrypt group_vars/deb/examp.yml
4. Какой командой можно расшифровать файл?
    1. ansible-vault decrypt group_vars/deb/examp.yml
5. Можно ли посмотреть содержимое зашифрованного файла без команды расшифровки файла? Если можно, то как?
    1. ansible-vault view group_vars/deb/examp.yml
6. Как выглядит команда запуска `playbook`, если переменные зашифрованы?
    1. ansible-playbook --ask-vault-pass
7. Как называется модуль подключения к host на windows?
    1. winrm
8. Приведите полный текст команды для поиска информации в документации ansible для модуля подключений ssh
    1.  ansible-doc -t connection -l |grep ssh
9. Какой параметр из модуля подключения `ssh` необходим для того, чтобы определить пользователя, под которым необходимо совершать подключение?
    1. remote_user