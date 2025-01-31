---
{"dg-publish":true,"permalink":"/06-full-notes/github-draft-pull-request/","noteIcon":""}
---

[[03 - Tags/Github\|Github]] [[03 - Tags/Github Action\|Github Action]] [[03 - Tags/DevOps\|DevOps]]
# Draft Pull Request

> [!important]  
> Draft : 초안  

초안 Pull Request라는 뜻에 맞게 초안으로 작성된 Pull Request를 뜻한다.
해당 Pull Request로 수정된 파일의 권한을 가진 Code Owner에게 Review 요청이 가지 않는다.
![Untitled 2.png](/img/user/image/Untitled%202.png)

이후에 Ready for Review 버튼을 통해 보통 PR로 교체가 가능하다.

# Code Owner란

해당 코드의 수정이 일어난 PR(Pull Request)가 작성되면 자동으로 리뷰어로 할당된다.
CODEOWNERS라는 설정 파일은 깃허브 액션과 동일하게 .github 디렉터리에 만들거나 docs 디렉터리에 만들면 된다.
또한, Pull Request의 대상(base)이 디폴트 브랜치여야 한다.