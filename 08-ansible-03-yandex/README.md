# Домашнее задание к занятию "08.03 Использование Yandex Cloud"

## Подготовка к выполнению
1. Создайте свой собственный (или используйте старый) публичный репозиторий на github с произвольным именем.
2. Скачайте [playbook](./playbook/) из репозитория с домашним заданием и перенесите его в свой репозиторий.

## Основная часть
1. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает kibana.
```
ansible-playbook -i inventory/prod site.yml
```
2. При создании tasks рекомендую использовать модули: `get_url`, `template`, `yum`, `apt`.
3. Tasks должны: скачать нужной версии дистрибутив, выполнить распаковку в выбранную директорию, сгенерировать конфигурацию с параметрами.
4. Приготовьте свой собственный inventory файл `prod.yml`.
5. Запустите `ansible-lint site.yml` и исправьте ошибки, если они есть.
6. Попробуйте запустить playbook на этом окружении с флагом `--check`.
7. Запустите playbook на `prod.yml` окружении с флагом `--diff`. Убедитесь, что изменения на системе произведены.
8. Повторно запустите playbook с флагом `--diff` и убедитесь, что playbook идемпотентен.
9. Проделайте шаги с 1 до 8 для создания ещё одного play, который устанавливает и настраивает filebeat.
10. Подготовьте README.md файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.
```
---
- name: Install Elasticsearch
  hosts: elasticsearch
  handlers:
    - name: restart Elasticsearch
      become: true
      service:
        name: elasticsearch
        state: restarted
      tags: elastic
  tasks:
    - name: "Download Elasticsearch's rpm"
      get_url:
        url: "https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-{{ elk_stack_version }}-x86_64.rpm"
        dest: "/tmp/elasticsearch-{{ elk_stack_version }}-x86_64.rpm"
      register: download_elastic
      until: download_elastic is succeeded
      tags: elastic
    - name: Install Elasticsearch
      become: true
      yum:
        name: "/tmp/elasticsearch-{{ elk_stack_version }}-x86_64.rpm"
        state: present
      tags: elastic
    - name: Configure Elasticsearch
      become: true
      template:
        src: elasticsearch.yml.j2
        dest: /etc/elasticsearch/elasticsearch.yml
      notify: restart Elasticsearch
      tags: elastic
- name: Install Kibana
  hosts: kibana
  handlers:
    - name: restart Kibana
      become: true
      service:
        name: kibana
        state: restarted
      tags: kibana
  tasks:
    - name: "Download Kibana rpm"
      get_url:
        url: "https://artifacts.elastic.co/downloads/kibana/kibana-{{ elk_stack_version }}-x86_64.rpm"
        dest: "/tmp/kibana-{{ elk_stack_version }}-x86_64.rpm"
      register: download_kibana
      until: download_kibana is succeeded
      tags: kibana
    - name: Install Kibana
      become: true
      yum:
        name: "/tmp/kibana-{{ elk_stack_version }}-x86_64.rpm"
        state: present
      tags: kibana
    - name: Configure Kibana
      become: true
      template:
        src: kibana.yml.j2
        dest: /etc/kibana/kibana.yml
      notify: restart Kibana
      tags: kibana
- name: Install filebeat
  hosts: filebeat
  handlers:
    - name: restart filebeat
      become: true
      service:
        name: filebeat
        state: restarted
      tags: kibana
  tasks:
    - name: "Download Filebeat rpm"
      get_url:
        url: "https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-{{ elk_stack_version }}-x86_64.rpm"
        dest: "/tmp/filebeat-{{ elk_stack_version }}-x86_64.rpm"
      register: download_filebeat
      until: download_filebeat is succeeded
      tags: filebeat
    - name: Install filebeat
      become: true
      yum:
        name: "/tmp/filebeat-{{ elk_stack_version }}-x86_64.rpm"
        state: present
      tags: filebeat
    - name: Configure filebeat
      become: true
      template:
        src: kibana.yml.j2
        dest: /etc/filebeat/filebeat.yml
      notify: restart filebeat
      tags: filebeat
    - name: Set filebeat systemwork
      become: true
      command:
        cmd: filebeat modules enable system
        chdir: /usr/share/filebeat/bin
      register: filebeat_modules
      changed_when: filebeat_modules.stdout != "Module system is alredy enabled"
#    - name: Load Kibana dashboard
#      become: true
#      command:
#        cmd: filebeat setup
#        chdir: /usr/share/filebeat/bin
#      register: filebeat setup
#      changed_when: false
#      until: filebeat setup is successed
```

11. Готовый playbook выложите в свой репозиторий, в ответ предоставьте ссылку на него.

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
