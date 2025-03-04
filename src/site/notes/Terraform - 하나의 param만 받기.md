---
{"dg-publish":true,"permalink":"/terraform-param/","noteIcon":""}
---

# Tags
- [[03 - Tags/Terraform\|Terraform]]
---
# 이슈
- Security Group에 rule을 선언할 때, `src/dst`로 보안그룹, IP(IPv4, IPv6)를 지정 가능하다.
- 따라서, 이 중에서 하나의 Parameter만 입력을 받아야 한다.
``` hcl
# 문제가 발생한 코드
locals {
  sg_id = data.aws_security_group.security_group.id
  referenced_sg_id = length(data.aws_security_group.referenced_security_group[*]) == 1 ? data.aws_security_group.referenced_security_group[0].id : null 
}

resource "aws_vpc_security_group_egress_rule" "default" {
  count = var.is_ingress_rule == true ? 0 : 1
  security_group_id            = local.sg_id 
  referenced_security_group_id = local.referenced_sg_id
  cidr_ipv4                    = var.cidr_ipv4
  ip_protocol                  = var.ip_protocol
  from_port                    = var.from_port
  to_port                      = var.to_port
}
```
- 위 코드는 `local.referenced_sg_id`가 `null`이고 `cidr_ipv4`가 `10.0.0.0/16`이어도 에러가 발생한다.
- Terraform에선 `referenced_sg_id`와 `cidr_ipv4`가 동시에 존재할 수 있다고 판단한다.
- 따라서, 하나만 존재해야 하는 `src/dest`가 두 개 설정되므로 에러가 발생한다.
- 이를 해결하기 위해선 `dynamic`을 사용하거나 두 매개 변수가 하나의 변수에 의존하도록 만들어야한다.
# 해결 방법
``` hcl
resource "aws_vpc_security_group_ingress_rule" "default" {
  count = var.is_ingress_rule == true ? 1 : 0
  security_group_id            = local.sg_id 
  referenced_security_group_id = local.referenced_sg_id != null ? local.referenced_sg_id : null 
  cidr_ipv4                    = local.referenced_sg_id != null ? null : var.cidr_ipv4
  ip_protocol                  = var.ip_protocol
  from_port                    = var.from_port
  to_port                      = var.to_port
}
```

위 코드는 `referenced_security_group_id`와 `cidr_ipv4` 모두 `local.referenced_sg_id`에 의존한다. 따라서, 무조건 한 가지 값만 `src/dest`로 올 수 있다. 
따라서, 실행이 가능하다.
