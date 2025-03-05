---
{"dg-publish":true,"permalink":"/ansible-playbook/","noteIcon":""}
---

# Tags
- [[AnsiInventory 설정하는 법이 생각ble\|AnsiInventory 설정하는 법이 생각ble]]
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
- `Play` 작업을 실패하면 `unreachable`이랑 `failures`는 분간된다.

## 요구 상태와 멱등성 (Desired state and `idempotency`)
- Ansible 모듈은 요구 상태에 이미 도달했다면 아무런 액션도 취하지 않는다.
- 따라서, 반복적으로 작업을 실행하더라도 최종 상태(final state)에서 변경이 일어나지 않는다.
- **그러나,** Ansible의 **모든 모듈이 멱등성을 제공하지 않는다.**
- 그러므로 멱등성을 제공하는지 먼저 테스트할 필요가 있다.

## Running playbooks in check mode
- check mode는 playbook을 실행하면서 시스템에 아무런 변화를 주지 않는다.[^4]
- 플래그 값은 `-c`나 긴 옵션은 `--check`
- 실제로 수정하진 않지만, 어떠한 변화가 일어날지 다음 내용이 포함된 리포트를 작성한다.
	- 파일 수정
	- 명령어 실행
	- 모듈 호출
- check mode는 시스템의 의도치 않은 변화를 초래하지 않으면서 실행 결과를 예측할 수 있는 이점이 있다.
## Ansible-Pull
- VCS에서 코드 가져와서 playbook 실행하는 거

## Verifying playbooks
실행하기 전에 먼저 문법 오류를 검증하는 툴들이 있다.[^5]
이외에 `playbook` 커맨드에도 `--check`, `--diff`, `--list-hosts`, `--list-tasks`, `--syntax-check` 등 옵션이 존재한다.

### ansible-lint
린팅 툴 존재한다.

## 추가적인 디테일한 내용
테스트나 변수 검증, 반복문 등은 굳이 정리하기보단 필요할 때 정리하는 게 나을듯.

https://docs.ansible.com/ansible/latest/playbook_guide/playbooks.html

---
# 참고 자료
- https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_intro.html
# 주석

[^1]: https://docs.ansible.com/ansible/latest/reference_appendices/playbooks_keywords.html#playbook-keywords

[^2]: https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_privilege_escalation.html#become

[^3]: free 전략은 다른 Play가 끝나질 기다리지 않는다. https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_strategies.html#selecting-a-strategy

[^4]: 테스트 용도로 production에서 실행하기 전에 최종으로 하면 좋을듯. 실제 적용은 안되지만 동작 여부를 파악할 수 있으니까.

[^5]: https://docs.ansible.com/ansible/latest/community/other_tools_and_programs.html#validate-playbook-tools
