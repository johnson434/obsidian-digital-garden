---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-terraform-data/","noteIcon":""}
---

# Tags
- [[03 - Tags/Terraform\|Terraform]]
- [[06 - Full Notes/Terraform - resource\|Terraform - resource]]
- [[06 - Full Notes/Terraform - lifecycle\|Terraform - lifecycle]]
---
## `terraform_data`
- 데이터를 저장하고 싶은데, 리소스 라이프사이클을 따르는 데이터를 저장하고 싶을 때 사용한다.

## 사용 예
### 1. data for `replace_triggered_by`
- `lifecycle`의 `repliace_triggered_by`는 plan을 통하여 적용된다.
- `repliace_triggered_by` 값으로는 `variable`이나 `locals`가 사용 불가능하다.
- 따라서, `terraform_data`가 `variable`이나 `locals`를 참조하는 것으로 간접 사용이 가능하다.
``` hcl
locals {
  ec2_spec = {
    "web1" = {
      name          = "web1",
      instance_type = "t2.micro",
      ami           = var.ubuntu_ami_id
    }
  }
}

resource "terraform_data" "replace" {
  input = local.ec2_spec
}

resource "aws_instance" "test" {
  for_each      = local.ec2_spec
  ami           = each.value.ami 
  instance_type = each.value.instance_type

  tags = {
    Name = "test-${each.value.name}"
  }

  lifecycle {
    # create_before_destroy = true
    # prevent_destroy       = true
    replace_triggered_by = [terraform_data.replace]
  }
}
```
### 2. `null_resource` 대체
``` hcl
resource "aws_instance" "web" {
  # ...
}

resource "aws_instance" "database" {
  # ...
}

# A use-case for terraform_data is as a do-nothing container
# for arbitrary actions taken by a provisioner.
resource "terraform_data" "bootstrap" {
  triggers_replace = [
    aws_instance.web.id,
    aws_instance.database.id
  ]

  provisioner "local-exec" {
    command = "bootstrap-hosts.sh"
  }
}
```