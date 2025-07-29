---
{"dg-publish":true,"dg-permalink":"go-in-action-chapter-03","permalink":"/go-in-action-chapter-03/","noteIcon":""}
---

# Tags
- [[03 - Tags/Go in Action\|Go in Action]]
---
1. 모든 Go파일은 패키지에 속한다.
2. 동일한 디렉터리엔 모두 동일한 패키지를 선언한다.
3. 패키지 네이밍은 짧고 영어 소문자로만
4. main 함수가 들어간 Go파일의 패키지의 이름은 반드시 main으로 선언한다.
5. godoc `패키지명`을 통해서 도움말 보기 가능
6. 동일한 패키지명이면 별칭 가능. `myfmt "mylib/fmt"`
7. import한 패키지를 사용하지 않으면 컴파일 과정에서 에러가 난다.
8. import를 통해서 패키지의 init만 실행하는 경우엔 `_`(blank identifier)를 붙여라. 그러면 build를 통과한다.
9. `go doc 문서` 명령어로 package 문서를 볼 수 있다.