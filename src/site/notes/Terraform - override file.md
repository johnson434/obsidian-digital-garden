---
{"dg-publish":true,"permalink":"/terraform-override-file/","noteIcon":""}
---

[[03 - Tags/Terraform\|Terraform]]
# \_override.tf
- 해당하는 이름으로 끝나는 파일은 덮어쓰기를 진행한다.
- 설정을 읽을 때, 이 파일은 스킵하고 마지막에 읽는다.
- 마지막에 이미 선언된 리소스가 있는지 파악하고 있다면 해당 파일에 덮어쓴다.
- 가독성을 해치므로 웬만하면 쓰지 말 것. (한두개면 읽기 쉽겠지만 인프라가 커지면 커질수록 찾기 힘들어질 것.)
- 내 생각으론 진짜진짜 필요한 상황 아니면 쓰지 말아야 할 기능 같다. 어떤 파일에서 덮어썼는지도 찾기 힘들고, 가독성 너무 해친다. 이 파일 때문에 버그 잘 날 느낌. 
- 따라서, 깊게 공부할 필요 없어보임.
``` hcl
# ec2_override.tf
resource "aws_instance" "test" {
  instance_type = "t2.medium"
}
```
``` hcl
resource "aws_instance" "test" {
	ami = "ami-124124"
  instance_type = "t2.micro"
}
```


---
https://developer.hashicorp.com/terraform/language/files/override