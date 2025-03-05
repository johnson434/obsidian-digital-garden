---
{"dg-publish":true,"permalink":"/06-full-notes/ansible-how-to-build-your-inventory/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ansible\|Ansible]]
---
# 단서 질문
- Inventory가 뭐야?
- 그룹 선언하는 파일명은 상관 없지?
	- 파일명에 따라서 로드 되므로 순서가 중요하다.
	- 자식 그룹이 먼저 호출되어야 한다.
---
# 핵심 요약
- Ansible은 패턴으로 매니지드 노드를 지정할 수 있다.
- Inventory는 파일에 리터럴로 작성하는 정적 방식과 동적으로 구성하는 dynamic inventory 방식이 있다.
- 패턴은 정수 범위나 Regex와 같은 정규식 등 다양하다.
- 그룹은 아무 것도 설정하지 않아도 `all`과 `ungrouped`가 있다.
- `all`은 모든 호스트를 포함하고, `ungrouped`는 `all`을 제외한 어떠한 그룹에도 속하지 않은 호스트를 포함한다.
- 그룹 간에 변수를 공유할 수 있다.
- 우선도가 높은 변수는 가장 가까운 변수다.

---
# 핵심 필기
## How to build your inventory
Ansible은 패턴을 통해서 매니지드 노드를 지정할 수 있다.
default로 사용하는 인벤토리는 `/etc/ansible/hosts`를 사용한다. 이밖에 방법으로는.
- inventroy 디렉터리 만들고 여러 inventory 파일 사용
- dynamic inventory[^1]: 플러그인을 활용해서 특정 CSP의 특정 Tag를 가진 리소스를 가져오는 등 동적인 인벤토리 설정이 가능하다.
- 파일이랑 dynamic 인벤토리 동시에 사용도 가능하다.

## 인벤토리 기본: formats, hosts, groups
### Default groups
인벤토리 파일에 어떠한 그룹도 명시하지 않으면 디폴트 그룹으로 `all`과 `ungrouped`가 만들어집니다. 
`all`은 모든 호스트를 포함합니다. `ungrouped`는 `all` 외에 다른 어떠한 그룹에도 속하지 않은 그룹입니다.
따라서, 모든 호스트는 최소 2개 그룹에 속합니다. (all + specific_group or all + ungrouped)

### 여러 그룹에 넣기 가능
What, Where, When: 무엇을(web, db), 어디서(region, az), 언제(prod, test)
``` yaml
ungrouped:
  hosts:
    mail.example.com:
webservers:
  hosts:
    foo.example.com:
    bar.example.com:
dbservers:
  hosts:
    one.example.com:
    two.example.com:
    three.example.com:
east:
  hosts:
    foo.example.com:
    one.example.com:
    two.example.com:
west:
  hosts:
    bar.example.com:
    three.example.com:
prod:
  hosts:
    foo.example.com:
    one.example.com:
    two.example.com:
test:
  hosts:
    bar.example.com:
    three.example.com:
```

### 그룹핑 그룹: 부모/자식 관계
``` yaml
ungrouped:
  hosts:
    mail.example.com:
webservers:
  hosts:
    foo.example.com:
    bar.example.com:
dbservers:
  hosts:
    one.example.com:
    two.example.com:
    three.example.com:
east:
  hosts:
    foo.example.com:
    one.example.com:
    two.example.com:
west:
  hosts:
    bar.example.com:
    three.example.com:
prod:
  children:
    east:
test:
  children:
    west:
```

Child group은 다음과 같은 속성을 가진다.
- child group에 속하면 자동으로 parent group에 속한다.
- 여러 부모랑 여러 자식을 가질 수 있다. 단, 순환 관계는 안된다.
- 호스트도 여러 그룹에 속할 수 있다.

### Adding ranges of hosts
```
# ...
  webservers:
    hosts:
	    # www01, www02, www03
      www[01:50].example.com:
```

## Organizing inventroy in a directory
Ansible은 인벤토리를 ASCII 파일명 순서로 조회합니다. 
이 때, 부모 파일이 먼저 조회되면 에러가 발생합니다. `Unable to parse /path/to/source_of_parent_groups as an inventory source`
이를 해결하는 방법은 단순하게 파일명 앞에 prefix를 붙인다.
```
inventory/
	01-openstack.yml
cloud
	02-dynamic-inventory.py
	03-on-prem
	04-groups-of-groups # 부모 그룹
```

## Adding a variables to inventory
인벤토리에 특정 호스트나 그룹과 관련 있는 변수를 저장할 수 있다.
### 호스트 변수: 하나의 노드를 위해 변수 할당하기
아래처럼 특정 호스트를 위해 변수를 할당할 수 있다.
``` yaml
atlanta:
  hosts:
    host1:
      http_port: 80
      maxRequestsPerChild: 808
    host2:
      http_port: 303
      maxRequestsPerChild: 909
```

22번 포트를 사용하지 않으면 포트를 호스트에 특정하면 된다.
```
badwolf.example.com:5309
localhost:10000
```
### Inventory aliases
``` yaml
# ...
  hosts:
    jumper:
      ansible_port: 5555
      ansible_host: 192.0.2.50
```

### Assigning a variable to many machines: group variables
특정 그룹이 변수를 공유할 수도 있다.
```
atlanta:
  hosts:
    host1:
    host2:
  vars:
    ntp_server: ntp.atlanta.example.com
    proxy: proxy.atlanta.example.com
```

### Inheriting variable values: group variables for groups of groups
```
[atlanta]
host1
host2

[raleigh]
host2
host3

[southeast:children]
atlanta
raleigh

[southeast:vars]
some_server=foo.southeast.example.com
halon_system_timeout=30
self_destruct_countdown=60
escape_pods=2

[usa:children]
southeast
northeast
southwest
northwest
```

``` yaml
usa:
  children:
    southeast:
      children:
        atlanta:
          hosts:
            host1:
            host2:
        raleigh:
          hosts:
            host2:
            host3:
      vars:
        some_server: foo.southeast.example.com
        halon_system_timeout: 30
        self_destruct_countdown: 60
        escape_pods: 2
    northeast:
    northwest:
    southwest:
```
child 그룹의 변수가 parent 그룹의 변수보다 우선도가 높다.

## Organizing host and group variables
variables 파일에선 list와 hash 데이터도 사용이 가능하다.
변수 파일은 상대 경로로 검색한다.
만약에, `webservers`라는 호스트가 /etc/ansible/hosts에 명시되어 있다고 가정하면
``` yaml
# group1, group2에 속한 foosball 호스트가 명시된 inventory
/etc/ansible/hosts 

/etc/ansible/group_vars/group1
/etc/ansible/group_vars/group2
/etc/ansible/host_vars/foosball

# 여기서 추가적으로 
# 그룹에서 추가로 디렉터리를 구성도 가능하다.
/etc/ansible/group_vars/group1/db_settings
/etc/ansible/group_vars/group2/cluster_settings
```

`ansible-playbook` 커맨드에서 사용할 변수들을 playbook 디렉터리의 `group_vars`나 `host_vars` 디렉터리에 추가할 수 있다.
`ansible`이나 `ansible-console`에선 인벤토리 디렉터리의 `group_vars`랑 `host_vars` 디렉터리만 사용한다.
만약에, 그룹 변수나 호스트 변수를 playbook에서 사용하려면 `--playbook-dir` 옵션을 추가해서 명령어를 실행해라.

## 변수 병합 순서
동일한 변수 선언하면 나중에 오는 애들이 덮어쓴다.
- all group
- parent group
- child group
- host
`ansible_group_priority` 옵션으로 변수 우선도 설정 가능
``` yaml
a_group:
  vars:
    testvar: a
    ansible_group_priority: 10
b_group:
  vars:
    testvar: b
```

## 인벤토리 변수 조회 순서 관리하기
인벤토리를 여러 개 사용하면 해당 순서대로 변수를 덮어쓴다.
예를 들어, `myvar` 변수를 모든 노드에 적용하도록([all:vars]) production과 staging 인벤토리에 명시했다.
이러면 명령어에 인벤토리를 작성한 순서대로 변수를 덮어쓴다.
`-i staging -i production`이라면 production이 나중에 덮어쓴다.
`-i production -i staging`이라면 staging이 나중에 덮어쓴다.

```
inventory/
  01-openstack.yml          # configure inventory plugin to get hosts from Openstack cloud
  02-dynamic-inventory.py   # add additional hosts with dynamic inventory script
  03-static-inventory       # add static hosts
  group_vars/
    all.yml                 # assign variables to all hosts
```
If `01-openstack.yml` defines `myvar = 1` for the group `all`, `02-dynamic-inventory.py` defines `myvar = 2`, and `03-static-inventory` defines `myvar = 3`, the playbook will be run with `myvar = 3`.

## 인벤토리 파라미터
https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html#id21
ansible_host, ansible_port 같은 인벤토리가 받을 수 있는 매개변수들.
외울 필요는 없어 보여서 주소만 적어 놓음.

## 인벤토리 설정 예시
### 1. 환경 별로 인벤토리 설정
- 환경 별로 인벤토리 파일 생성
	- `inventory_test` 테스트 환경
	- `inventory_staging` 스테이징 환경
```
# 테스트 inventory_test 파일
dbservers:
	db01.test.example.com
	db02.test.example.com
appservers:
	app01.test.example.com
	app02.test.example.com
	
# 스테이지 inventory_stage 파일
dbservers:
	db01.stage.example.com
	db02.stage.example.com
appservers:
	app01.stage.example.com
	app02.stage.example.com
```

``` bash
$ ansible-playbook -i inventory_test -l appservers site.yml
```

### 2. 기능 별로 그룹화
``` yaml
- hosts: dbservers
  tasks:
  - name: Allow access from 10.0.0.1
    ansible.builtin.iptables:
      chain: INPUT
      jump: ACCEPT
      source: 10.0.0.1
```

### 3. 장소 별로 그룹화
``` yaml
dc1:
	db01.test.example.com
	app01.test.example.com
dc2:
	db02.test.example.com
```


---
# 참고 자료
- https://docs.ansible.com/ansible/latest/inventory_guide/intro_patterns.html#intro-patterns
# 주석

[^1]: https://docs.ansible.com/ansible/latest/inventory_guide/intro_dynamic_inventory.html#intro-dynamic-inventory https://docs.ansible.com/ansible/latest/plugins/inventory.html#inventory-plugins