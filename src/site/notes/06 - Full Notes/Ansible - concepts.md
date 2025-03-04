---
{"dg-publish":true,"permalink":"/06-full-notes/ansible-concepts/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ansible\|Ansible]]
---
## Control Node
- Ansible CLI를 실행하는 노드
- Execution Environments라는 컨테이너에서 Ansible을 실행할 수도 있다.
- 컨트롤 노드로 여러 개를 사용할 수 있지만 Ansible이 이들을 관리하진 않는다. 이러한 기능은 `AAP`를 확인해라.
## Managed Nodes
- `hosts`라고도 불리는 Ansible을 통해 대상이 되는 관리 노드다.
- `ansible-pull`을 사용하지 않는 이상, `ansible`이 설치되어 있지 않다. (`ansible-pull`은 추천되지 않는 방법)
## Inventory
- `hosts`를 명시하는 소스 파일들(`hostfile`이라고도 부른다.)
- 각 호스트의 IP 주소, 포트 번호를 명시하며 그룹도 할당 가능하다. 
## Playbooks
- `plays`라는 Ansible 실행 유닛을 포함한다.
- 실행 컨셉이면서 동시에 `ansible-playbook`의 작업을 파일로 작성하는 방법이다.
### Plays
- Ansible 실행의 핵심 문맥(context)
- 이 playbook 오브젝트는 `hosts`를 작업과 매핑한다.
- 변수, 역할, 재실행 가능한 실행 순서대로 정렬된 작업들이 포함된다. (variables, roles, tasks)
-  It basically consists of an implicit loop over the mapped hosts and tasks and defines how to iterate over them.
- mapped hosts와 tasks에 대해 암묵적인 반복으로 구성되며, 이들을 어떻게 반복할지 정의한다.
### Roles
- Ansible Context에서 재사용 가능한 제한된 배포판으로 Play 내에서 사용된다. (라이브러리 같은 느낌)
- Role을 사용하기 위해선 `play`에 Import가 필요하다.
### Tasks
- `managed node`에 실행될 `action`을 정의한다.
- `ansible`이나 `ansible-console`을 통해서 간단한 task를 바로 실행 가능하다.
### Handlers
- 이전 `task`가 `changed` 상태일 때, `notify`하면 실행되는 `task`
## Modules
- task에 정의된 작업을 완수하기 위해서 각 `managed node`에 복사되거나 실행되는 코드 혹은 바이너리
- 각 모듈은 특정 용도가 있습니다.
	- 예: 특정 데이터베이스에서 유저를 관리, 특정 네트워크 기기에서 VLAN 인터페이스를 관리 등
## Plugins
- Ansible 코어 기능을 확장하는 코드다.
- `managed node`에 연결하는 방법을 조절하거나(connection plugins: ssh), 데이터를 조작하거나(filter plugins), 콘솔에 무엇이 보일지 조작할 수 있다.(callback plugins)
## Collections
- Ansible 컨텐츠의 배포판으로 playbooks, roles, modules, plugins 등을 포함할 수 있다.
- Ansible Galaxy를 통해 설치하고 사용이 가능하다.
- Role에 비해 더 큰 개념인듯. 더 큰 라이브러리 느낌?

---
# 참고 자료
https://docs.ansible.com/ansible/latest/getting_started/basic_concepts.html