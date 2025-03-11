---
{"dg-publish":true,"permalink":"/06-full-notes/ansible-creating-a-playbook/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ansible\|Ansible]]
---
# 핵심 필기
## Creating a playbook
- Playbook은 YAML 형식에 자동화 블루프린다.
- Ansible은 Playbook을 이용하여 배포 및 설정을 세팅한다.
**Playbook**
- `play`라는 액션의 리스트로 위에서부터 순서대로 실행됩니다.
**Play**
- 정렬된 작업 리스트로 `inventory`의 `managed node`와 매핑됩니다.
**Task**
- Ansible이 실행할 작업을 정의한 모듈 하나를 참조합니다. (최소 작업 단위)
**Module**
- `managed node`에서 실행될 이진 코드입니다.
- Ansible의 각 모듈은 FQDN을 사용하여 컬렉션에 모여있습니다.
``` bash
# playbook.yaml
- name: My first play
  hosts: region:ap_northeast_2:az_a:dev
  tasks:
   - name: Ping my hosts
     ansible.builtin.ping:

   - name: Print message
     ansible.builtin.debug:
       msg: Hello world
```

```bash
$ ansible-playbook -i inventory.yaml playbook.yaml
```
---
# 참고 자료
https://docs.ansible.com/ansible/latest/getting_started/get_started_playbook.html