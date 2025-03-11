---
{"dg-publish":true,"permalink":"/06-full-notes/ansible-playbook/","noteIcon":""}
---

# Tags
- [[AnsiInventory 설정하는 법이 생각ble\|AnsiInventory 설정하는 법이 생각ble]]
---
# 단서 질문
- **Ansible Playbook이란 무엇이며, 어떤 역할을 하는가?**
- **Playbook의 주요 구성 요소(Play, Task 등)는 무엇인가?**
- **Playbook 실행 순서는 어떻게 결정되는가?**
- **Check Mode를 사용하는 이유는 무엇인가?**
- **Ansible-Pull은 기존의 Ansible Push 모델과 어떻게 다른가?**
- **Playbook을 실행하기 전에 검증하는 방법에는 어떤 것들이 있는가?**
- **Ansible-Lint는 무엇이며, 어떤 역할을 하는가?**
---
# 핵심 요약
- **Ansible Playbook**은 **반복 가능한 구성 관리(Configuration Management) 및 멀티 머신 배포 시스템**을 제공한다.
- **Playbook**은 **하나 이상의 Play로 구성**되며, **각 Play는 특정 호스트에서 실행될 여러 Task를 정의**한다.
- **Playbook은 위에서 아래로 순차적으로 실행**되며, Play 내의 Task도 **순서대로 실행**된다.
- **Check Mode** (`--check`)를 사용하면 Playbook이 변경을 적용하지 않고 **어떤 변경이 일어날지 미리 확인 가능**하다.
- **Ansible-Pull**을 사용하면 **클라이언트가 중앙 서버로부터 Playbook을 가져와 실행**할 수 있다.
- **Playbook 실행 전 검증 옵션**:
    - **`--syntax-check`**: 문법 오류 확인
    - **`--list-hosts`**: 실행 대상 호스트 목록 확인
    - **`--list-tasks`**: 실행될 작업 목록 확인
- **Ansible-Lint**를 활용하면 **Playbook의 코드 품질을 검사하고 Best Practice를 적용 가능**하다.
---
# 핵심 필기
## **Ansible Playbook 개요**  

### **Ansible Playbook이란?**  
Ansible Playbooks는 **반복 가능하고, 재사용 가능한 구성 관리(Configuration Management) 및 멀티 머신 배포 시스템**을 제공합니다.  
이를 통해 복잡한 애플리케이션을 배포할 수 있으며, 특정 작업을 여러 번 실행해야 할 경우 Playbook을 작성하여 **소스 제어(Git 등)**에 보관하는 것이 좋습니다.  

Playbook을 사용하면 다음과 같은 작업이 가능합니다:
- **구성(Configuration) 선언**
- **수동으로 실행하는 프로세스를 정의된 순서대로 자동화**
- **동기적(Synchronous) 또는 비동기적(Asynchronous) 작업 실행**

---

## **Playbook의 문법(Syntax)**  

Playbook은 **YAML 형식**으로 작성됩니다. YAML 문법이 익숙하지 않다면, Ansible의 [YAML 문법 개요](https://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html)를 참고하세요.  
Playbook은 하나 이상의 **플레이(Play)**로 구성됩니다.  
각 **Play**는 특정 목표를 수행하며, 하나 이상의 **작업(Task)**을 포함합니다.  

### **Playbook 예제**
```yaml
---
- name: 웹 서버 업데이트
  hosts: webservers
  remote_user: root

  tasks:
    - name: Apache 최신 버전 유지
      ansible.builtin.yum:
        name: httpd
        state: latest

    - name: Apache 설정 파일 배포
      ansible.builtin.template:
        src: /srv/httpd.j2
        dest: /etc/httpd.conf

- name: 데이터베이스 서버 업데이트
  hosts: databases
  remote_user: root

  tasks:
    - name: PostgreSQL 최신 버전 유지
      ansible.builtin.yum:
        name: postgresql
        state: latest

    - name: PostgreSQL 서비스 실행
      ansible.builtin.service:
        name: postgresql
        state: started
```
✅ **설명**  
- 첫 번째 `Play`: 웹 서버(webservers) 대상, Apache 웹 서버를 최신 버전으로 유지하고 설정 파일을 배포  
- 두 번째 `Play`: 데이터베이스 서버(databases) 대상, PostgreSQL을 최신 버전으로 유지하고 실행  

---

## **Playbook 실행 순서**  

✅ **Playbook 실행 순서**  
1. Playbook은 **위에서 아래로 순차적으로 실행**됩니다.  
2. Playbook 내에서 여러 개의 **Play**가 있을 경우, **각 Play는 순서대로 실행**됩니다.  
3. 각 Play 내의 **Task들도 순차적으로 실행**됩니다.  
4. **멀티 머신 배포**가 가능하며, 예를 들어:
   - 첫 번째 Play에서 웹 서버를 업데이트  
   - 두 번째 Play에서 데이터베이스 서버를 업데이트  
   - 세 번째 Play에서 네트워크 설정을 적용할 수도 있음  

✅ **Ansible 2.10 이후 버전에서는**  
Playbook에서 사용하는 모듈이 여러 개의 **컬렉션(Collections)**에서 동일한 이름으로 존재할 수 있으므로,  
**완전한 모듈 명칭(Fully-Qualified Collection Name, FQCN)을 사용해야 함.**  

예시:  
```yaml
ansible.builtin.yum:
```
위처럼 `ansible.builtin`을 붙여 명확하게 명시하는 것이 권장됨.

---

## **Playbook 실행**  
### **기본 실행**
```sh
ansible-playbook playbook.yml
```
- `playbook.yml` 파일을 실행하여 지정된 작업을 수행  

### **병렬 실행 (-f 옵션)**
```sh
ansible-playbook playbook.yml -f 10
```
- 한 번에 **최대 10개의 노드에서 병렬 실행**  

### **디버깅을 위한 상세 출력 (--verbose 옵션)**
```sh
ansible-playbook playbook.yml --verbose
```
- 성공 및 실패한 모듈의 자세한 출력 정보 제공  

---

## **체크 모드(Check Mode) 실행**
✅ **Ansible의 Check Mode**  
Playbook을 실행하기 전에 시스템을 변경하지 않고 **무엇이 변경될지 미리 확인**하는 기능.  
(예제 실행 시, 변경 사항만 출력하고 실제로 적용하지 않음.)  

### **실행 방법**
```sh
ansible-playbook --check playbook.yml
```
또는
```sh
ansible-playbook -C playbook.yml
```
✅ **Check Mode 특징**  
- Playbook이 **어떤 변경 사항을 적용할지 미리 확인 가능**  
- 시스템에 **아무런 영향을 주지 않고 "가상 실행" 가능**  
- 파일 수정, 명령 실행, 모듈 호출과 같은 작업을 **예측하여 보고서 형태로 출력**  

---

## **Ansible-Pull**  
✅ **기존 Ansible 실행 방식**:  
Ansible이 중앙 관리 서버에서 **클라이언트로 설정을 "푸시"** (Push)하는 방식.  

✅ **Ansible-Pull의 역할**:  
클라이언트 노드에서 **중앙 서버로부터 "풀(Pull)" 방식으로 Playbook을 가져와 실행**.  
이 방식은 **중앙 서버의 부하를 줄이고, 노드가 스스로 구성을 업데이트할 수 있도록** 해줌.  

### **Ansible-Pull 실행 예제**
```sh
ansible-pull -U https://github.com/your-repo/ansible-config.git
```
✅ **특징**
- `ansible-pull`은 Git 저장소에서 **설정 파일을 가져온 후 실행**  
- 부하 분산을 위해 **로드 밸런서를 활용 가능**  
- **대규모 환경에서도 쉽게 확장 가능**  

---

## **Playbook 검증 및 오류 검사**  
✅ **실행 전 Playbook을 검증하여 문법 오류 및 문제점 탐색 가능**  

### **1. 기본 검증 옵션**
```sh
ansible-playbook playbook.yml --syntax-check
```
- 문법 오류가 있는지 확인  

```sh
ansible-playbook playbook.yml --list-hosts
```
- Playbook이 어떤 호스트에서 실행될지 확인  

```sh
ansible-playbook playbook.yml --list-tasks
```
- 실행될 Task 목록 출력  

---

## **Ansible-Lint (Playbook 코드 품질 검사)**  
✅ **Ansible-Lint는 Ansible Playbook을 분석하여 코드 품질을 높이는 도구**  
- Playbook 실행 전에 **잘못된 구문, 보안 문제, 비효율적인 코드**를 자동으로 감지  

### **실행 예제**
```sh
ansible-lint playbook.yml
```
✅ **출력 예시**
```plaintext
[403] Package installs should not use latest
verify-apache.yml:8
Task/Handler: ensure apache is at the latest version
```
✅ **해결 방법**
- `state: latest` 대신 `state: present`로 변경하는 것이 권장됨.  
```yaml
- name: Apache 최신 버전 유지
  ansible.builtin.yum:
    name: httpd
    state: present  # 최신 버전이 아닌, 현재 존재하는 버전 사용
```

✅ **장점**
- 문법 오류 사전 예방  
- 베스트 프랙티스 적용  
- 보안 취약점 감지  

---

## **결론**
✅ **Ansible Playbook의 주요 개념 정리**

| 개념 | 설명 |
|------|------|
| **Playbook** | Ansible에서 작업을 정의하는 YAML 기반 구성 파일 |
| **Task** | 특정 Ansible 모듈을 호출하는 개별 작업 |
| **Check Mode** | 실제 변경 없이 Playbook을 시뮬레이션하는 기능 |
| **Ansible-Pull** | 클라이언트가 중앙 서버로부터 Playbook을 가져와 실행 |
| **Playbook 검증** | `--syntax-check`, `--list-tasks`, `--list-hosts` 등을 사용 |
| **Ansible-Lint** | 코드 품질을 검사하고 Best Practice를 적용 |

🚀 **Ansible Playbook을 활용하면 자동화된 배포, 시스템 설정, 유지 관리 등을 효과적으로 수행 가능!**

# 주석

[^1]: https://docs.ansible.com/ansible/latest/reference_appendices/playbooks_keywords.html#playbook-keywords

[^2]: https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_privilege_escalation.html#become

[^3]: free 전략은 다른 Play가 끝나질 기다리지 않는다. https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_strategies.html#selecting-a-strategy

[^4]: 테스트 용도로 production에서 실행하기 전에 최종으로 하면 좋을듯. 실제 적용은 안되지만 동작 여부를 파악할 수 있으니까.

[^5]: https://docs.ansible.com/ansible/latest/community/other_tools_and_programs.html#validate-playbook-tools
