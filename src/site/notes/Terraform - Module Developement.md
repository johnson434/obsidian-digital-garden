---
{"dg-publish":true,"permalink":"/terraform-module-developement/","noteIcon":""}
---

# Tags
- [[03 - Tags/Terraform\|Terraform]]
- [[Terraform - Module\|Terraform - Module]]
---
# 핵심 필기
- 모듈을 개발할 때, 신경써야할 점들을 서술했다.
## 의존성 역전 (Dependency Inversion)
- 만약, 아래와 같이 cluster 모듈을 작성했다.
``` hcl
module "network" {
  source = "./modules/aws-network"

  base_cidr_block = "10.0.0.0/8"
}

module "consul_cluster" {
  source = "./modules/aws-consul-cluster"

  vpc_id     = module.network.vpc_id
  subnet_ids = module.network.subnet_ids
}
```
- 위에 방법의 대처법은 `consul_cluster`가 사용하는 `vpc_id`와 `subnet_ids`를 consul_cluster에 사용하는 것이다.
- 그렇게 된다면 생기는 문제점은 무엇일까? 다른 모듈이 `consul_cluster` 모듈과 겹치는 네트워크 대역을 사용한다면 둘 중의 하나의 네트워크 대역을 변경해야 한다.
- 따라서, 외부에서 vpc_id를 주입하는 방식으로 동작하게 작성하는 것이 더 편하다.
``` hcl
data "aws_vpc" "main" {
  tags = {
    Environment = "production"
  }
}

data "aws_subnet_ids" "main" {
  vpc_id = data.aws_vpc.main.id
}

module "consul_cluster" {
  source = "./modules/aws-consul-cluster"
	# vpc를 외부에서 주입하므로 vpc IP 대역이 겹쳐서
	# 코드를 수정할 필요가 없음.
  vpc_id     = data.aws_vpc.main.id
  subnet_ids = data.aws_subnet_ids.main.ids
}
```

## Data 조회만으로 이뤄진 모듈
- `data` 블록을 통해서 조회만 하는 모듈을 사용하는 것도 좋다.
- 값이 변경되더라도 `data` 모듈만 바꾸면 된다.

---
# References
https://developer.hashicorp.com/terraform/language/modules/develop/composition