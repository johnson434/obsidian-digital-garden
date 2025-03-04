---
{"dg-publish":true,"permalink":"/06-full-notes/ansible-inventory/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ansible\|Ansible]]
---
# 핵심 필기
## Inventory
- `.ini`랑 `.yaml` 형식 둘 다 지원. 개인적으로 `YAML`이 더 읽기 좋다.
## Inventory 확인하기
1. 작성된 Inventory를 통해서 `Managed Node` 확인하기
``` bash
ansible-inventory -i INVENTORY_FILE_NAME --list
```
2. `ping`을 통해서 연결 확인
``` bash
ansible HOST_GROUP_NAME [--private-key path/to/key] -m ping -i INVENTORY_FILE_NAME
```
`.ssh/id_rsa` 디폴트 키를 사용하지 않고, 새로 만든 개인 키를 통해서 접속했다.
![Pasted image 20250214125552.png](/img/user/image/Pasted%20image%2020250214125552.png)

## Inventory 작성 팁
- 중앙화된 파일들에서 `Managed Node`에 네트워크 주소와 시스템 정보를 저장하여 관리한다.
- **그룹명은 유일해야 하며** 대소문자를 구분한다.
- 공백과 하이픈을 사용하지 않고 숫자로 시작하면 안된다.
- 그룹은 `무엇을`, `어디서`, `언제`에 따라 나눈다.
- `무엇을`:  네트워크 토폴로지에 따라 나눈다.
	- 예시: db, web, leaf, spine
- `어디서`: 노드가 호스팅 되는 지역에 따라 나눈다.
	- 예시: region, az, datacenter, floor
- `언제`: stage에 따라 나눈다.
	- 예시: dev, test, production
## Use metagroups
- 메타그룹을 통해서 그룹을 조직할 수 있다.
``` yaml
metagroupname:
	children:
```
- 다음은 내가 만든 예시다.[^1]
``` yaml
region:
  children:
    ap_northeast_2:

ap_northeast_2:
  children:
    az_a:

az_a:
  children:
    dev:
    
dev:
  hosts:
    container01:
      ansible_user: root 
      ansible_host: localhost
      ansible_port: 10000
    container02:
      ansible_user: root 
      ansible_host: localhost
      ansible_port: 10001
    container03:
      ansible_user: root 
      ansible_host: localhost
      ansible_port: 10002
```
그래프 출력 결과
``` bash
$ ansible-inventory -i ./inventory.yml --graph
@all:
  |--@ungrouped:
  |--@region:
  |  |--@ap_northeast_2:
  |  |  |--@az_a:
  |  |  |  |--@dev:
  |  |  |  |  |--container01
  |  |  |  |  |--container02
  |  |  |  |  |--container03
  |  |  |  |--@prod:
  |  |  |  |--@stage:
```
## Create variables
- `managed nodes`를 위한 변수 설정이 가능하다.
	- 예: IP 주소, FQDN, OS, SSH user
``` yaml
group:
	hosts:
		webserver01:
			ansible_host: 192.168.0.1
			http_port: 80 # 해당 호스트만 사용
	vars:
		ansible_user: my_server_user # group에 속하는 모든 호스트가 공유
```
---
# 참고 자료
- https://docs.ansible.com/ansible/latest/getting_started/get_started_inventory.html

[^1]: 잘못된 예시인 게 그룹명은 무조건 유니크하다. 즉,  ap_northest_1:az_a 이런 식으로 불가능하다. ap_northeast2:az_a와 ap_northeast_1:az_a 중에 하나만 설정된다.
