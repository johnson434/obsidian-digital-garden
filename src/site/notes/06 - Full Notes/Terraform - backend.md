---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-backend/","noteIcon":""}
---

[[03 - Tags/Terraform\|Terraform]]
## What is`backend` block
- 테라폼은 `.tfstate`라는 파일로 상태를 저장한다.
- 이를 통해서 Infra의 상태를 관리할 수 있습니다.
- 여러 사람이 코드를 통해 리소스를 프로비저닝한다면 상태 관리를 위한 `.tfstate` 파일을 공동 저장소에 올려둘 필요가 있습니다.
- 이를 `.backend` 블록으로 설정 가능합니다. (default값은 `local`)
``` hcl
terraform {
	backend "s3" {
		bucket = ""
		key    = ""
	}
}
```
- `backend` 블록 설정 후에 `.terraform`엔 백엔드 설정이 적용된 state 파일이 만들어진다. 내용엔 인증 방식 등이 작성되어 있다.(예: S3는 assumeRole 여부, token 등)
## S3 사용 시 주의
- state lock을 제공 안함.
- DynamoDB를 써야 lock이 가능.