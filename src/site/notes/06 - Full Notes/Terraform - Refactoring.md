---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-refactoring/","noteIcon":""}
---

# Tags
- [[03 - Tags/Terraform\|Terraform]]
---
# 핵심 필기
## Refactoring
테라폼 코드를 작성하다가 모듈이 너무 커지는 경우가 있을 것이다.
그러나, 이미 `terraform apply` 커맨드를 통해서 인프라 객체를 만들었다면 코드를 옮기는 순간, 서로 다른 리소스로 판단하여 다음 `terraform apply` phase 때 기존 인프라 객체를 제거하고 새로 만들 것이다.
``` hcl
# 의사코드임.
# /modules/ec2
resource "instance" "web" {
	instance_type = "t2.micro"
	subnet = subnet.web
}

resource "subnet" "web" {
	name = "web"
	cidr_blocks = "172.16.0.0/24"
}
```
위 코드는 인스턴스를 위한 서브넷을 모듈 내에서 정의한다.
이를 의존성 주입을 통해서 Decoupling 해보겠다.
```
# /modules/ec2
data "subnet" "web" {
	name = "web"
}
resource "instance" "web" {
	instance_type = "t2.micro"
	subnet = data.subnet.web.id
}

# /main.tf
resouce "subnet" "web" {
	name = "web"
	cidr_blocks = "172.16.0.0/24"
}

module "ec2" {
	source = "./modules/ec2"
	depends_on = subnet.web
}
```
위처럼 의존성을 줄이는 코드로 리팩토링을 하는 경우가 있을 것이다.
위같이 코드를 다른 모듈로 옮기는 경우엔, 서로 다른 리소스로 판단하여 기존 인프라인 `subnet.web`을 제거한 후에 새로 subnet을 만든다.
이를 방지하기 위해 사용하는 것이 **`moved` 블록**이다.
## `moved` 블록 문법
``` hcl
moved {
	from = aws_instance.a
	to = aws_instance.b
}
```
- `from`과 `to`로 이뤄진 간결한 문법
- `plan` phase에서 `from`으로 설정된 `aws_instance.a`가 존재하는지 확인하다.
- 만약에, `aws_instance.a`가 존재한다면 이름을 `aws_instance.b`로 변경한다.
- `plan`의 결과로는 `aws_instance.b`가 새로 만들어진 것처럼 보이지만 실제론 `.tfstate` 파일에서 이름만 변경된다.
![Pasted image 20250207114459.png](/img/user/image/Pasted%20image%2020250207114459.png)
---
## `moved` 사용 예제
### `count`나 `for_each` 블록으로 이전하기
``` hcl
resource "aws_instance" "a" {
	ami = "ami-124124124124124124124124"
}

resource "aws_instance" "b" {
	for_each = toset(["small", "medium"])
}

# moved 블록으로 a를 b의 small로 이전 가능
moved {
	from = aws_instance.a
	to = aws_instance.b["small"]
}

resource "aws_instance" "b" {
	for_each = toset(["small", "medium"])
}
```

### module 이전하기
``` hcl
module "load-balancer" {
	source = "./modules/load-balancer"
}

moved {
	from = module.a
	to = module.b
}

# A 모듈의 리소스를 B 모듈로 옮기기
# modules/a/ec2.tf
resource "aws_instance" "a_web" {}
moved {
	from = aws_instance.a_web
	to = module.b.aws_instance.b_web
}
```
## `moved`  블록 제거
- 웬만하면 `moved` 블록을 남기는 것을 권장
- `moved` 블록을 통해 리소스를 이전하지 않은 인프라가 있을 때, moved 블록이 없다면 리소스를 삭제하고 재생성할것이다.
- 따라서, 웬만하면 기록용으로 남겨놔라.
# References
https://developer.hashicorp.com/terraform/language/modules/develop/refactoring