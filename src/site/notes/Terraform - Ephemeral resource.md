---
{"dg-publish":true,"permalink":"/terraform-ephemeral-resource/","noteIcon":""}
---

# Tags
- [[03 - Tags/Terraform\|Terraform]]
- [[Terraform - resource\|Terraform - resource]]
---
## Ephemeral resource란?
- 본질적으로(essentially) 일시적인 리소스
- 유니크한 라이프사이클을 가지며 리소스의 상태가 state에 저장되지 않는다.
- `resource`와 달리 `ephemeral`은 모듈 밖에서 의미가 없습니다. (동일한 모듈 내에서만 참조가 가능한듯)
- `Ephemeral resource`를 참조하는 리소스가 있다면 `Ephemeral resource`를 가장 먼저 프로비전한다.
- 임의의 `credentials`를 얻어서 리소스에 사용하고 싶을 때 쓰는 리소스인듯. expiration을 연장한다는 것도 그렇고.
- **일단 작성 보류(필요할 때 잠깐 보면 이해가능할 느낌)**
``` hcl
ephemeral "aws_secretsmanager_secret_version" "db_master" {
  secret_id = data.aws_db_instance.example.master_user_secret[0].secret_arn
}

locals {
  credentials = jsondecode(ephemeral.aws_secretsmanager_secret_version.db_master.secret_string)
}

provider "postgresql" {
  host     = data.aws_db_instance.example.address
  port     = data.aws_db_instance.example.port
  username = local.credentials["username"]
  password = local.credentials["password"]
}
```
---
# References
https://developer.hashicorp.com/terraform/language/resources/ephemeral