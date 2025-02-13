---
{"dg-publish":true,"permalink":"/06-full-notes/ansible/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ansible\|Ansible]]
# 핵심 필기

## Ansible 구성 요소 3가지
### 1. Control Node
- Ansible이 설치되어 있는 노드로 Control Node에서 Ansible을 실행하여 Managed Node를 프로비저닝한다. ssh를 통해서 Managed Node와 연결된다.
### 2. Inventory
- Managed Node 리스트로 Control Node에 의해 관리되는 노드의 집합이다.
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
- Ansible이 컨트롤할 원격 시스템이나 호스트를 의미한다.
- ssh 접속만 허용하면 되며, 추가적으로 패키지를 설치할 필요가 없다.
- 컨트롤 노드의 공개키를 매니저드 노드에 authorized_keys에 작성해야 한다.
```JavaScript
controller-node@localhost: ~$ ssh-keygen # 키 생성
# id_rsa와 id_rsa.pub가 생성
# 연결할 노드(매니저드 노드)에 authrozied_keys에 id_rsa.pub 내용이 추가되어야 한다.

controller-node@localhost: ~$ ssh -i ./id_rsa 유저명@호스트
```
## Ansible의 특징
### Agent-less 아키텍처
- 추가적인 소프트웨어를 설치를 피함으로써 유지를 위한 오버헤드를 줄일 수 있다.
- 예를 들어, AWS의 System Manager는 System Manager Agent가 필요하다.
### 간단함
- YAML 문법으로 작성되어 읽기 편하며, 기존 OS 인증 방식을 사용하여 ssh로 접근한다.
## ansible vs ansible-playbook
- ansible은 하나의 task
- ansible-playbook은 여러 개의 playbook을 실행한다.
## Inventory 작성 팁
- 그룹명은 유일해야 하며 대소문자를 구분한다.
- 공백과 하이픈을 사용하지 않고 숫자로 시작하면 안된다.
- **What** Group hosts according to the topology, for example: db, web, leaf, spine.
- **Where** Group hosts by geographic location, for example: datacenter, region, floor, building.
- **When** Group hosts by stage, for example: development, test, staging, production.

	```YAML
	leafs:
		hosts:
			leaf01:
				ansible_host: 192.0.2.100
			leaf02:
				ansible_host: 192.0.2.110
	
	spines:
		hosts:
			spine01:
				ansible_host: 192.0.2.120
			spine02:
				ansible_host: 192.0.2.130
	
	network:
		children:
			leafs:
			spines:
	
	webservers:
		hosts:
			webserver01:
				ansible_host: 192.0.2.140
			webserver02:
				ansible_host: 192.0.2.150
	
	datacenter:
		children:
			network:
			webservers:
	```
## Inventory에 변수 사용
```YAML
webservers:
	hosts:
		webserver01:
			ansible_host: 192.0.2.140
			http_port: 80 # 해당 노드에선 해당 변수가 등록된다.
		webserver02:
			ansible_host: 192.0.2.150
			http_port: 443
---
webservers:
	hosts:
		webserver01:
			ansible_host: 192.0.2.140
			http_port: 80
		webserver02:
			ansible_host: 192.0.2.150
			http_port: 443
	vars:
		ansible_user: my_server_user # hosts에 속한 모든 노드가 변수를 공유한다.
```

# 참고 자료
https://docs.ansible.com/ansible/latest/getting_started/introduction.html