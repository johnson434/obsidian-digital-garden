---
{"dg-publish":true,"permalink":"/terraform-backend/","noteIcon":""}
---

[[03 - Tags/Terraform\|Terraform]]
# What is`backend` block
- 테라폼은 `.tfstate`라는 파일로 상태를 저장한다.
- 이를 통해서 Infra의 상태를 관리할 수 있습니다.
- 여러 사람이 코드를 통해 리소스를 프로비저닝한다면 상태 관리를 위한 `.tfstate` 파일을 공동 저장소에 올려둘 필요가 있습니다.
- 이를 `.backend` 블록으로 설정 가능합니다.
``` hcl
terraform {
	backend "s3" {
		bucket = ""
		key    = ""
	}
}
```
