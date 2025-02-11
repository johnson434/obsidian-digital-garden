---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-workspace/","noteIcon":""}
---

# Tags
- [[03 - Tags/Terraform\|Terraform]]
---
# 핵심 필기
## Workspaces
- 모든 테라폼 설정은 `backend`와 협동한다.
- `backend`는 인증이나 `state`와 같은 테라폼 설정 데이터를 저장하는 곳을 정의한다.
- `backend`는 workspace에 속하는데 workspace는 단, 하나의 Terraform `state`만 포함한다.
- `backend`와 여러 workspace를 사용할 수 있다.
- 여러 workspace를 통해서 여러 Terraform `state`를 사용할 수 있다. (예: 개발환경, 운영환경도 workspace로 구분 가능.)
``` hcl
resource "aws_instance" "web" {
	count = terraform.workspace == "dev" ? 1 : 3
	tags = {
		Name = "${terraform.workspace}-web"
	}
}
```

---
# References
https://developer.hashicorp.com/terraform/language/state/workspaces