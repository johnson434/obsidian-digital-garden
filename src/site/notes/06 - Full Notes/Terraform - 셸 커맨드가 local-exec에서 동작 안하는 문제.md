---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-local-exec/","noteIcon":""}
---

### Tags
- [[03 - Tags/Terraform\|Terraform]]
### 문제 상황
- 터미널에서 동작하는 문법이 테라폼의 `provisioner local-exec`에서 동작 안함.
``` bash
# 아래와 유사한 코드
if (( 1 = 1)); then
	echo "do something"
fi
```
- 그런데, 다른 if문에선 동작함
``` bash
if [ 1 -eq 1]; then
	echo "do something"
fi
```
### 문제 원인
- `local-exec`를 통해서 command를 실행하면 디폴트로 `/bin/sh`을 통해서 실행한다.
- `if(())`는 bash 문법으로 POSIX 표준 문법이 아님.
- 따라서, 내 bash에선 실행되는데 `/bin/sh`로는 실행이 불가능
### 문제 해결 방법
#### shell을 명시하여 특정 shell에서 실행되도록 설정한다.
장점
- 코드 수정 불필요
단점
- 실행하는 사용자 환경에 따라서 해당 shell이 설치되지 않았을 수도 있음.
- 특정 shell에 종속되는 문법을 사용해야 된다.
#### POSIX 표준에 맞는 문법 사용
장점
- 어느 사용자더라도 POSIX 표준을 따르므로 동일하게 실행 보장.
단점
- bash 문법 사용 못함. (`function` 키워드나 `if(())`)
### 결론
- 어느 사용자더라도 동일한 사용을 보장하므로 POSIX 표준 따르는 게 맞다고 생각