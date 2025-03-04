---
{"dg-publish":true,"permalink":"/06-full-notes/ansible-pattern/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ansible\|Ansible]]
---

| Pattern(s)                    | Targets                                             |
| ----------------------------- | --------------------------------------------------- |
| all (or *)                    |                                                     |
| host1                         |                                                     |
| host1:host2 (or host1, host2) |                                                     |
| webservers                    |                                                     |
| webservers:dbservers          | all hosts in webservers plus all hosts in dbservers |
| webservers:!atlanta           | all hosts in webservers except those in atlanta     |
| webservers:&staging           | any hosts in webservers that are also in staging    |
예: `webservers`:`dbservers`:`&staging`:`!phoenix`
## 패턴의 제한
Inventory에 명시되지 않은 호스트는 사용이 불가능하다.
예: 10.0.0.23와 같은 구체적인 IP를 명시하더라도 해당 IP가 인벤토리에 없으면 사용 불가능.

## 패턴 처리 순서 (Pattern processing order)
1. `:`이랑 `,`
2. `&`
3. `!`
## 심화 패턴 옵션
앞서 설명한 패턴으로 대부분의 인벤토리 명시가 가능하지만, Ansible은 이외에도 호스트와 그룹을 효율적으로 정의하는 다른 방법을 제공합니다.

### 패턴 내에 변수 사용
`ansible-playbook` 커맨드의 `-e` 플래그로 변수를 입력할 수 있다.
``` yaml
webserbers: !{{ excluded }}:&{{ required }}
```

### 패턴 내에 그룹 포지션 사용
```
[webservers]
cobweb
webbing
weber
```

#### 특정 아이템 슬라이싱
- 연산자: `s[i]`
- 결과: i번째 노드를 리턴한다.
```
webservers[0] = cobweb
webservers[-1] = weber
```
### 정규식 사용
`~`를 써서 정규식을 사용할 수 있다.
```
~(web|db).*\.example.\.com
```

## 패턴과 `ad-hoc` 명령어 사용
``` bash
$ ansible all -m <module> -a "<module options>" --limit "host1"

$ ansible all -m <module> -a "<module options>" --limit "host1,host2"

# 명령어로 인식안하려고 '' 사용
$ ansible all -m <module> -a "<module options>" --limit 'all:!host1'
```

## 패턴과 `ansible-playbook` 플래그
커맨드 라인 옵션에서 `-i` 플래그로 패턴을 변경할 수 있다.
예: `hosts: all`를 패턴으로 정의한 playbook을 사용할 때 `-i 127.0.0.2`를 사용하면 `127.0.0.2`가 타겟이 된다.

`--limit` 플래그로 해당 playbook의 인벤토리를 제한할 수 있다.
``` bash
ansible-playbook site.yml --limit @retry_hosts.txt
```

`RETRY_FILES_ENABLED` 옵션이 `True`면 실패한 호스트에 대해서 `.retry` 파일이 생성된다. 
```
ansible-playbook site.yml --limit @site.retry
```

---