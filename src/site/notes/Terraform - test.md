---
{"dg-publish":true,"permalink":"/terraform-test/","noteIcon":""}
---

[[03 - Tags/Terraform\|Terraform]]

---
## tftest.hcl
- 테라폼의 테스트 파일이다.
## 테스트 실행
`terraform test`로 테스트를 실행할 수 있지만, 현재 `working directory`에 있는 테스트나 `working_directory/tests` 폴더에 있는 테스트만 실행합니다.
모듈에 따로 테스트를 만들어로 실행하지 않습니다. (아무래도 Terraform이 디렉터리 자체를 모듈로 판단하다 보니까 생기는 일인듯)
따라서, `--test-directory=path` 플래그를 통해서 추가해줘야 합니다.

---
# References
[Files and Directories - test files](https://developer.hashicorp.com/terraform/language/files/tests)
https://developer.hashicorp.com/terraform/language/tests
https://developer.hashicorp.com/terraform/tutorials/configuration-language/test