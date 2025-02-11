---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-expressions/","noteIcon":""}
---

# Tags
- [[03 - Tags/Terraform\|Terraform]]
---
# 핵심 필기
##  테라폼에서 지원하는 자료형
- string, number, bool, list, set, map

## Map 선언
``` hcl
# map 선언에 두 방식 모두 허용한다.
map = {
	"abc": "abc"
}
map = {
	abc = "abc",
	"ddd": "ddd" # 혼용도됨.
}
```
- 나는 JSON처럼 `:` 쓰는 방법이 취향이다.
- `key`엔 `""`를 쓰지 않아도 되는데 예외 케이스로 숫자로 시작하거나 공백이 있거나 특수문자가 있는 경우엔 써야됨.
- 또한, 한줄 맵에도 컴마를 사용해야됨.
## any
- 웬만하면 쓰지마라
- Java 지네릭스처럼 하나의 자료형으로 바뀜.
	- `list(any)`인데 값으로 ["11","22"]가 오면 `list(string)`으로 처리
## Object
- 객체다.
- 맵은 한 가지 타입을 사용하는데 `object`는 여러 타입을 key, value로 사용 가능
- optional 자료형을 통해서 입력값을 선택적으로 입력 받지 않게 만들 수 있다. 입력하지 않으면 값은 null로 들어간다.
```hcl
variable "student" {
  type = object({
    id = string
    age = optional(string, "default value")
  })
	description = "test"
  
  default = {
    id = "test"
  } 
}

output "student" {
  value = var.student
}
```
- optional의 default value를 먼저 대입하고 나중에 `default` 브록의 값을 대입한다.
``` hcl
variable "student" {
  type = object({
    id = string
    age = optional(string, "optional_default_value") 
  })
  description = "test"
  default = {
    id = "test", 
    age = "default_block default value"
  } 
}
# id는 test이고 age = "default_block default value"

```

---
# References
https://developer.hashicorp.com/terraform/language/expressions/types