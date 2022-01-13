# Домашнее задание к занятию "08.02 Работа с Playbook"

## Подготовка к выполнению
1. Создайте свой собственный (или используйте старый) публичный репозиторий на github с произвольным именем.
2. Скачайте [playbook](./playbook/) из репозитория с домашним заданием и перенесите его в свой репозиторий.
3. Подготовьте хосты в соотвтествии с группами из предподготовленного playbook. 
4. Скачайте дистрибутив [java](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) и положите его в директорию `playbook/files/`. 

## Основная часть
1. Приготовьте свой собственный inventory файл `prod.yml`.
```---
      elasticsearch:
        hosts:
          el:
            ansible_connection: local
      kibana:
        hosts:
          kib:
            ansible_connection: local
```

2. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает kibana.
```
- name: Install kibana
  hosts: kibana
  tasks:
    - name: Upload tar.gz kibana from remote URL
        get_url:
          url: "https://artifacts.elastic.co/downloads/kibana/kibana-{{ kibana_version }}-linux-x86_64.tar.gz"
          dest: "/tmp/kibana-{{ kibana_version }}-linux-x86_64.tar.gz"
          mode: 0755
          timeout: 120
          force: true
          validate_certs: false
        register: get_kibana
        until: get_kibana is succeeded
        tags: kibana
      - name: Create directrory for kibana
        file:
          state: directory
          path: "{{ kibana_home }}"
        tags: kibana
      - name: Extract Kibana in the installation directory
        #become: true
        unarchive:
          copy: false
          src: "/tmp/kibana-{{ kibana_version }}-linux-x86_64.tar.gz"
          dest: "{{ kibana_home }}"
          extra_opts: [--strip-components=1]
          creates: "{{ kibana_home }}/bin/kibana"
        tags: kibana
      - name: Set environment Kibana
        #become: true
        template:
          src: templates/kib.sh.j2
          dest: /etc/profile.d/kib.sh
        tags: kibana
```
3. При создании tasks рекомендую использовать модули: `get_url`, `template`, `unarchive`, `file`.
4. Tasks должны: скачать нужной версии дистрибутив, выполнить распаковку в выбранную директорию, сгенерировать конфигурацию с параметрами.
5. Запустите `ansible-lint site.yml` и исправьте ошибки, если они есть.
6. Попробуйте запустить playbook на этом окружении с флагом `--check`.
7. Запустите playbook на `prod.yml` окружении с флагом `--diff`. Убедитесь, что изменения на системе произведены.
8. Повторно запустите playbook с флагом `--diff` и убедитесь, что playbook идемпотентен.
9. Подготовьте README.md файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.

###Install Java
установлены тэги java для дальнейшего использования и отладки установка переменных (facts) загрузка установочного пакета создние рабочего каталога распаковка пакета создание по шаблону переменных окружений (templates)

###Install Elastic
установлены тэги elastic для дальнейшего использования и отладки загрузка установочного пакета создание рабочего каталога распаковка в рабочий каталог из пакета создание по шаблону переменных окружений (templates)

###Install Kibana
установлены тэги kibana для дальнейшего использования и отладки загрузка установочного пакета создание рабочего каталога распаковка в рабочий каталог из пакета создание по шаблону переменных окружений (templates)
 
10. Готовый playbook выложите в свой репозиторий, в ответ предоставьте ссылку на него.

## Необязательная часть

1. Приготовьте дополнительный хост для установки logstash.
2. Пропишите данный хост в `prod.yml` в новую группу `logstash`.
3. Дополните playbook ещё одним play, который будет исполнять установку logstash только на выделенный для него хост.
4. Все переменные для нового play определите в отдельный файл `group_vars/logstash/vars.yml`.
5. Logstash конфиг должен конфигурироваться в части ссылки на elasticsearch (можно взять, например его IP из facts или определить через vars).
6. Дополните README.md, протестируйте playbook, выложите новую версию в github. В ответ предоставьте ссылку на репозиторий.

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
