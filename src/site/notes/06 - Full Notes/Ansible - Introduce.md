---
{"dg-publish":true,"permalink":"/06-full-notes/ansible-introduce/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ansible\|Ansible]]
# 핵심 필기
## Ansible 구성 요소 3가지
### 1. Control Node
- `ansible`, `ansible-playbook` 등을 실행하는 주체가 되는 노드
### 2. Inventory
- `Control Node`가 관리할 노드 그룹이다.
- 관리되는 노드 그룹을 `Managed Node`라고 한다.
```YAML
myhosts:
	hosts:
		my_host_01:
			ansible_host: 192.0.2.50
		my_host_02:
			ansible_host: 192.0.2.51
		my_host_03:
			ansible_host: 192.0.2.52
```
### 3. Managed Node
- `ansible`에 의해 관리될 노드
- ssh 접속만 허용하면 되며, 추가적으로 패키지를 설치할 필요가 없다.
- 컨트롤 노드의 공개키를 매니저드 노드에 authorized_keys에 작성해야 한다.
- 또한, `Managed Node`에 sshd가 실행 되어야 한다.
## Ansible의 특징
**Agent-less 아키텍처**
- `Managed Node`에 추가적인 소프트웨어를 설치할 필요가 없다. 
- 따라서, 유지보수 오버헤드가 줄어든다.
- 예를 들어, AWS의 `System Manager`가 관리하는 노드들엔 `System Manager Agent`가 필요하다.
**간단함**
- YAML 문법으로 작성되어 읽기 편하며, 기존 OS 인증 방식을 사용하여 ssh로 접근한다.
**확장성과 유연성**
- 모듈식 디자인을 통해 쉽게 확장이 가능하다.
**멱등성과 예측 가능성**
- `playbook`을 여러 번 실행하더라도 동일한 상태를 보장한다. (Terraform이 실행되더라도 동일한 리소스를 만드는 것처럼)
**내가 생각하는 단점**
- `ssh` 포트 허용이 필요하다. 이를 SG나 NACL을 통해서 외부 접속 차단이 가능하지만 잘못 설정했다간 외부에서 공격할 수 있는 진입점이 될 수 있다.
# 참고 자료
https://docs.ansible.com/ansible/latest/getting_started/introduction.html