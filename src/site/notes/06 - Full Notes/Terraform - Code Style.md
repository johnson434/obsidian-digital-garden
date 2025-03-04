---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-code-style/","noteIcon":""}
---

[[03 - Tags/Terraform\|Terraform]]
# Code Style
- `terraform fmt`으로 Linting 가능
- 명사로 작성
- 리소스명의 리소스 타입 쓰지 말 것
``` hcl
resource "aws_instance" "aws_instance_1" {}
```
- 문자 사이엔 언더스코어(`_`) 사용하기 
- 코드 자체로 동작하게 만들어라. 특정 리소스를 참조하는 리소스들은 해당 참조 리소스들을 먼저 작성하고 이후에 작성해라.
- variable에 type이랑 description 필수 작성
- variable, local 남용 금지 (data 같이 동적으로 처리 가능하면 하란 뜻일듯)
- meta-argument는 마지막 줄에 한 칸 띄워서 작성
``` hcl
resource "aws_instance" "example" {
  # meta-argument first
  count = 2

  ami           = "abc123"
  instance_type = "t2.micro"

  network_interface {
  }

  # meta-argument block last
  lifecycle {
    create_before_destroy = true
  }
}
```
- 탑레벨 블록은 서로 한칸씩 띄우고 중첩 블록도 한칸씩 띄움.
# File Names

```
.
├── examples              # 모듈 사용법이 들어있음.
│		└── example_a
│       └── rds.tftest
├── tests
│		├── eks_test.tftest
│		├── sqs.tftest
│   └── rds.tftest
├── modules
│   ├── function
│   │   ├── main.tf      # contains aws_iam_role,
│   │   ├── outputs.tf
│   │   └── variables.tf
│   ├── queue
│   │   ├── main.tf      # contains aws_sqs_queue
│   │   ├── outputs.tf
│   │   └── variables.tf
│   └── vpc
│       ├── main.tf      # contains aws_vpc, aws_subnet
│       ├── outputs.tf
│       └── variables.tf
├── main.tf
├── outputs.tf
└── variables.tf

```

# Resource order
1. `count` or `for_each`와 같은 meta-argument를 제일 먼저(존재한다면)
2. Resource-specific non-block parameters (ami = "111")
3. Resource-specific block parameters (tags = { })
4. `lifecycle` block
5. `depends_on`
# Variable
- 순서:
	1. Type
	2. Description
	3. Default (Optional)
	4. Sensitive (Optional)
	5. Validation blocks (Optional) [^1]
- Type, Description 정의
- 선택적 값이면 `default`값 명시
- 노출되면 안되는 값이면 `sensitive`를 true (sensitive true로 설정해도 Plain Text로 tfstate에 저장된다. 단, apply, plan 시에 노출 안됨.)
- **validation**: 입력값 검증이 가능
``` hcl
variable "var1" {
	validation {
		condition = var.var1 > 1
		error_message = "error message to display"
	}
}
```
# Outputs
1. type
2. description
3. sensitive (Optional)

의존성 
# Local values
- 변수화해서 여러 번 사용하려고 쓴다.
- locals 파일 따로 만들어서 써도됨.
- 만약, locals 변수가 특정 파일에 종속되면 해당 파일에 맨 위에 쓰셈.
- 더 많은 정보는 [local values documentation](https://developer.hashicorp.com/terraform/language/values/locals)랑  [Simplify Terraform configuration with locals](https://developer.hashicorp.com/terraform/tutorials/configuration-language/locals)에서 확인
```
locals {
	key = value
	key2 = value
}
```

# Provider aliasing
- `provider` 블록을 여러 개 사용 가능하게 만든다.
- 근데 왜 여러 개 쓰는데? => 여러 리전 사용할 때라든가
- `alias` 파라미터 명시하지 않은 `provider`가 default provider임.
``` hcl
# provider.tf
provider "aws" {
	region = "ap-northeast-2"
}

provider "aws" {
	alias  = "west"
	region = "us-west-2"
}

# main.tf
module "aws_vpc" {
	source = "./aws_vpc"
	providers = {
		aws = aws.west
	}
}

```
# Dynamic resource count
- 런타임 시에 동적으로 리소스를 여러 개 만든다.
- `count`와 `for_each`는 meta-arguments임.
- 거의 동일하면 `count`를 쓰고, 세부 설정이 다르면 `for_each` 사용
- `for_each`는 `map`이나 `set`을 받음.
``` hcl
variable "web_instances" {
  type        = list(string)
  description = "A list of instances for the web application"
  default = [
    "ui",
    "api",
    "db",
    "metrics"
  ]
}
resource "aws_instance" "web" {
  for_each = toset(var.web_instances)
  ami           = data.aws_ami.webapp.id
  instance_type = "t3.micro"
  tags = {
    Name = "web_${each.key}"
  }
}
output "web_private_ips" {
  description = "Private IPs of the web instances"
  value = {
    for k, v in aws_instance.web : k => v.private_ip
  }
}
output "web_ui_public_ip" {
  description = "Public IP of the web UI instance"
  value       = aws_instance.web["ui"].public_ip
}
```
# 환경 나누기
- 환경을 디렉터리별로 나눠라.
- 이 방법 장점이 좋은 이유
	- 나는 이전에 `.tfvars`라는 변수 파일로 환경을 나눴다.
	- `.tfstate` 파일을 공유해서 동시에 서로 다른 환경 배포가 불가능했음.
	- 이 방법처럼 환경을 디렉터리로 나눠버리면 서로 다른 상태 파일을 가지므로 별도로 관리가 가능함.
```
├── modules
│   ├── compute
│   │   └── main.tf
│   ├── database
│   │   └── main.tf
│   └── network
│       └── main.tf
├── dev
│   ├── backend.tf
│   ├── main.tf
│   └── variables.tf
├── prod
│   ├── backend.tf
│   ├── main.tf
│   └── variables.tf
└── staging
    ├── backend.tf
    ├── main.tf
    └── variables.tf
```
---
[^1]: https://developer.hashicorp.com/terraform/language/values/variables#custom-validation-rules
https://developer.hashicorp.com/terraform/language/style#linting-and-static-code-analysis