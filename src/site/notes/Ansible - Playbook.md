---
{"dg-publish":true,"permalink":"/ansible-playbook/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ansible\|Ansible]]
---
# 단서 질문

---
# 핵심 요약

---
# 핵심 필기
- YAML 문법 사용한다.
- 노드를 정의하고 해당 매니지드 노드에 실행할 task를 정의한다.
- Action 정의 순서대로 동작한다.
``` yaml
- name: Update web servers
  hosts: webservers
  remote_user: root

  tasks:
  - name: Ensure apache is at the latest version
    ansible.builtin.yum:
      name: httpd
      state: latest

  - name: Write the apache config file
    ansible.builtin.template:
      src: /srv/httpd.j2
      dest: /etc/httpd.conf

- name: Update db servers
  hosts: databases
  remote_user: root

  tasks:
  - name: Ensure postgresql is at the latest version
    ansible.builtin.yum:
      name: postgresql
      state: latest

  - name: Ensure that postgresql is started
    ansible.builtin.service:
      name: postgresql
      state: started
```

`remote_user`와 같은 `Playbook` 키워드[^1]로 Ansible 다음과 같은 설정이 가능합니다.
- 커넥션 플러그인을 조절
- 권한 향상[^2]
- 에러 핸들링

## Task execution
- 디폴트로 실행 전략이 `linear`로 Play의 모든 Host에 동작이 끝나지 않으면 다음 Play를 실행하지 않습니다.[^3]
- 특정 호스트에서 task가 실패하면 해당 호스트는 나머지 playbook에서 해당 호스트가 제거됩니다.

---
# 참고 자료
- https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_intro.html
# 주석

[^1]: https://docs.ansible.com/ansible/latest/reference_appendices/playbooks_keywords.html#playbook-keywords

[^2]: https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_privilege_escalation.html#become

[^3]: free 전략은 다른 Play가 끝나질 기다리지 않는다. https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_strategies.html#selecting-a-strategy
