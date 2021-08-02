# Домашнее задание к занятию "08.02 Работа с Playbook"

## Основная часть
```yaml
- name: Install Kibana
  hosts: elasticsearch
  tasks:
    - name: Upload tar.gz Kibana from remote URL
      get_url:
        url: "https://artifacts.elastic.co/downloads/kibana/kibana-7.13.4-linux-x86_64.tar.gz"
        dest: "/tmp/kibana-7.13.4-linux-x86_64.tar.gz"
        mode: 0644
        timeout: 120
        force: false        
      tags: kibana
    - name: Create directrory for kibana
      file:
        state: directory
        path:  "{{ kibana_home }}"
      tags: kibana, skip_ansible_lint
    - name: Extract Kibana in the installation directory
      become: true
      unarchive:
        copy: false
        src: "/tmp/kibana-7.13.4-linux-x86_64.tar.gz"
        dest: "{{ kibana_home }}"
        extra_opts: [--strip-components=1]
        creates: "{{ kibana_home }}/bin/kibana"
      tags:
        - kibana 
    - name: Set environment Kibana
      become: true
      template:
        src: templates/kib.sh.j2
        dest: /etc/profile.d/kib.sh
      tags: kibana 
```
