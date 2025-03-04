---
{"dg-publish":true,"permalink":"/06-full-notes/ansible-serial/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ansible\|Ansible]]
---
# Ansible의 Serial이란?
동시에 최대 몇개의 호스트에서 진행이 가능한지 명시하는 playbook 옵션
```
- name: My first play
  hosts: region:ap_northeast_2:az_a:dev
  serial: 1
  tasks:
    - name: Ping my hosts
      ansible.builtin.ping:
    - name: Print message
      ansible.builtin.debug:
        msg: Hello world
```