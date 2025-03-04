---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-meta-arguments/","noteIcon":""}
---

- [[03 - Tags/Terraform\|Terraform]]
- [[06 - Full Notes/Terraform - resource\|Terraform - resource]]

## depends_on
- 명시적으로 특정 `resource`나 `module`이 생성된 후에 리소스를 생성하는 기능
- `override` 파일처럼 최후의 수단으로 사용할 것.
- `depends_on`으로 명시적으로 순서를 정할 경우, 테라폼은 보수적으로 인프라 객체를 생성한다. (해당 순서에 맞춰서 인프라를 생성하므로)
- 배열을 값으로 받는다.
``` hcl
resource "blahblah" "test" {
	depends_on = [
		aws_instance.test,
		aws_instance.web
	]
}
```
## count
- `count`와 `for_each`는 동시에 사용 불가능.
- `count`의 값은 Terraform configuration이 적용 되기 전에 알 수 없는 값을 주면 안된다.
	- 변수의 size나 length나 리터럴 같은 정적인 값들은 가능하다.
	- Terraform configuration 적용을 통해 만들어진 리소스들의 값을 쓸 순 없다.
- `count.index`로 index에 접근 가능하다.
- `count`나 `for_each`를 통해 만들어진 리소스는 인덱스로 참조한다.
	- `resource_type.name[0]` or `module.ec2[0]`
- 단순히 리소스가 여러 개 필요하다면 `count`를, 리소스 간에 개별 설정이 필요하다면 `for_each`를 사용할 것.
- index 기반으로 리소스를 식별하므로 더 깨지기 쉽다.
``` hcl
variable "subnet_ids" {
  type = list(string)
}

# 아래와 같은 상황에서 subnet을 길이 3으로 설정했다면
# index 1: subnet-1
# index 2: subnet-2
# index 3: subnet-3
# 위처럼 인덱스 기반으로 파악한다. 이 상태에서 index 2인 subnet-2가 제거된다면
# index 1: subnet-1
# index 2: subnet-3 
# 위처럼 서브넷이 변경된 것으로 판단되어 subnet-3가 subnet-2로 재생성된다.
# 반면에, for_each는 value로 식별하므로 저런 일이 안생긴다.
resource "aws_instance" "server" {
  # Create one instance for each subnet
  count = length(var.subnet_ids)

  ami           = "ami-a1b2c3d4"
  instance_type = "t2.micro"
  subnet_id     = var.subnet_ids[count.index]

  tags = {
    Name = "Server ${count.index}"
	}
}
```
## for_each
- `map` or `set(string)`을 값으로 받는다.
``` hcl
resource "aws_instance" "test" {
  for_each = toset(["abc","efg"])
  ami = "ami-0a2c043e56e9abcc5" 
  instance_type = "t2.micro"
  tags = {
    Name = "test-${each.value}"
  }
}
```
- `each`로 key 또는 value에 접근 가능하다. 
- `each.key`의 값
	- map의 key
	- set의 값
- `each.value`의 값
	- map의 value
	- set의 값
### for_each의 한계
- `uid`, `bcrypt`, `timestamp`와 같은 순수 함수의 결과를 값으로 사용할 수 없다.
	- 이러한 함수들은 main 평가 과정에서 뒤로 deferred 되기 때문에
- `count`와 동일하게 remote API 등을 통해 만들어진 값을 참조가 불가능하다.
- 민감한 데이터를 사용해도 안된다. `sensitive input variable`과 같은 민감한 데이터들을 `for_each`의 값으로 주면 이후에 output으로 출력이 되므로 해당 값을 사용하면 에러가 발생한다.
```
# 이러한 값은 for_each에서 사용하면 안된다.
variable "sensitive_value" {
	type = string
	description = "sensitive data"
	sensitive = true 
}
```

# 참고자료
https://developer.hashicorp.com/terraform/language/meta-arguments/depends_on