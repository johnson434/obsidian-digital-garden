---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-resource/","noteIcon":""}
---

[[03 - Tags/Terraform\|Terraform]]
## meta-arguments
다음과 같은 `meta-arguments`를 사용 가능하다.
- [`depends_on`, for specifying hidden dependencies](https://developer.hashicorp.com/terraform/language/meta-arguments/depends_on)
- [`count`, for creating multiple resource instances according to a count](https://developer.hashicorp.com/terraform/language/meta-arguments/count)
- [`for_each`, to create multiple instances according to a map, or set of strings](https://developer.hashicorp.com/terraform/language/meta-arguments/for_each)
- [`provider`, for selecting a non-default provider configuration](https://developer.hashicorp.com/terraform/language/meta-arguments/resource-provider)
- [`lifecycle`, for lifecycle customizations](https://developer.hashicorp.com/terraform/language/meta-arguments/lifecycle)
- [`provisioner`, for taking extra actions after resource creation](https://developer.hashicorp.com/terraform/language/resources/provisioners/syntax)
## Removing Resources
- `removed` 블록은 Terraform v1.7 이상 version에서만 사용 가능하다. (이전 버전에선 `terraform state rm` CLI 커맨드를 사용하세요.)
- 실제 인프라 객체를 없애거나 `destroy` 옵션을 통해서  `.tfstate` 파일에서만 제거 할 수 있다. (언제 쓸까? 더 이상 해당 리소스를 테라폼으로 관리하고 싶지 않을 때)
``` hcl
removed {
  from = aws_instance.example

  lifecycle {
		# default가 true이며 인프라 object를 삭제함.
		# false 설정 시엔 인프라를 삭제하지 않고, tfstate에서만 제거한다.
    destroy = true
  }

	# Destroy time provisioner로 제거될 때, 프로비저너를 실행
	# 프로비저너는 when destroy 설정이 필요하고
	# removed 블록도 destroy를 true로 설정해야만
	# 프로비저너가 실행된다.
  provisioner "local-exec" {
    when    = destroy
    command = "echo 'Instance ${self.id} has been destroyed.'"
  }
}
```

## Custom Condition Checks
### precondition and postcondition
- `resource`와 `data`에 사용 가능하다.
- 이름 그대로 `pre-condition`은 리소스가 만들어지기 전에 평가한다.
- 이름 그대로 `post-condition`도 리소스가 만들어진 후에 평가한다.
- 조건과 error_message를 통해서 유지 보수가 편리해진다.
	- 조건문이나 에러 메시지를 보고 다른 개발자가 해당 데이터의 용도를 파악할 수 있음.
``` hcl
resource "aws_instance" "example" {
  instance_type = "t2.micro"
  ami           = "ami-abc123"

  lifecycle {
    # The AMI ID must refer to an AMI that contains an operating system
    # for the `x86_64` architecture.
    precondition {
      condition     = data.aws_ami.example.architecture == "x86_64"
      error_message = "The selected AMI must be for the x86_64 architecture."
    }
  }
}
```
## Timeouts
- 대부분의 리소스가 지원 안한다.
``` hcl
resource "aws_db_instance" "example" {
  # ...

  timeouts {
    create = "60m"
    delete = "2h"
  }
}
```


https://developer.hashicorp.com/terraform/language/resources/syntax