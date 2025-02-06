---
{"dg-publish":true,"permalink":"/terraform-module/","noteIcon":""}
---

# Tags
- [[03 - Tags/Terraform\|Terraform]]
---
# 핵심 필기
## Module이란
- `.tf`나 `.tf.json` 파일로 이뤄진 디렉터리.
- 디렉터리가 모듈의 단위다. 서로 다른 디렉터리는 서로 다른 모듈.
- module 저장소에 모듈을 배포 가능
- `count`나 `for_each`를 사용해서 모듈로 여러 인스턴스 생성 가능
## 모듈의 출력물 접근하기
- module.[모듈명].[outpus이름]으로 접근 가능하다.
``` hcl
resource "aws_elb" "example" {
  # ...
  instances = module.servers.instance_ids
}
```
## 리소스 상태를 다른 모듈로 옮기기
- 리소스를 다른 모듈로 옮기면 `.tfstate` 파일을 따로 관리하기 때문에 서로 다른 리소스로 판단한다.
- 따라서, 기존 리소스가 삭제되고 새로 리소스를 만든다.
- 이를 방지하기 위해 `refactoring blocks`를 사용한다.
	- `move` 블록으로 이전할 위치를 설정하면 `.tfstate` 내의 경로가 수정됨.
``` hcl
move {
	from = aws_instance.test
	to = module.[module_name].aws_instance.test
}
```
## Replacing resources within a module
- 특정 리소스를 재생성할 수 있다.
``` bash
$ terraform plan -replace=module.example.aws_instance.example
```
## Removing Modules
- 특정 리소스를 `.tfstate`에서만 제거하고 실제 인프라 객체는 유지하기 위해서 lifecycle 설정에서 `destroy` 값을 false로 줬다.
- 모듈도 `removed` 블록을 통해서 `.tfstate`에선 제거하지만 인프라 객체는 유지할 수 있다.
``` hcl
removed {
	from = module.eks
	lifecycle {
		destroy = false
	}
}
```

---
# References
https://developer.hashicorp.com/terraform/language/modules
https://developer.hashicorp.com/terraform/language/modules/syntax
