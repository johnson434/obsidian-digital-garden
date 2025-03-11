---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-lifecycle/","noteIcon":""}
---

# Tags
- [[03 - Tags/Terraform\|Terraform]]
---
# **단서 질문 및 답변**  

### **Q1: Terraform `lifecycle` 블록이란?**  
✅ **Terraform 리소스의 생성, 업데이트, 삭제 동작을 조정하는 `meta-arguments` 설정 블록**.  
✅ **리소스 내부에서 `lifecycle` 블록을 추가하여 동작을 제어할 수 있음**.  

---

### **Q2: Terraform `lifecycle` 블록 내부에서 사용 가능한 주요 설정은?**  
✅ **`create_before_destroy`**: 새 리소스를 먼저 생성 후 기존 리소스를 삭제  
✅ **`prevent_destroy`**: 리소스 삭제 방지  
✅ **`ignore_changes`**: 특정 속성 변경 무시  
✅ **`replace_triggered_by`**: 특정 리소스 변경 시 강제 재생성  
✅ **`precondition` / `postcondition`**: 리소스 생성 전후의 조건 체크  

---

### **Q3: `create_before_destroy`란?**  
✅ **Terraform의 기본 동작은 리소스를 수정할 때 기존 리소스를 먼저 삭제한 후 새로 생성**  
✅ **하지만 `create_before_destroy = true` 설정 시, 새 리소스를 먼저 생성한 후 기존 리소스를 삭제**  
✅ **적용 예시**  
```hcl
resource "aws_instance" "example" {
  ami           = "ami-123456"
  instance_type = "t2.micro"

  lifecycle {
    create_before_destroy = true
  }
}
```
✅ **주의사항**  
- `create_before_destroy = true`이면 **destroy provisioner는 실행되지 않음**.  
- `Name` 태그가 변경되면 Terraform은 리소스를 새로운 리소스로 간주하여 **즉시 삭제 후 생성**.  
- `for_each`를 사용하여 **식별자를 명확히 설정하면 제자리 업데이트(in-place update) 가능**.  

---

### **Q4: `prevent_destroy`란?**  
✅ **리소스 삭제를 방지하는 설정 (`terraform destroy` 명령어도 차단됨)**  
✅ **비싼 비용이 드는 리소스(DB, 프로덕션 서버 등)에 유용**  
✅ **적용 예시**  
```hcl
resource "aws_instance" "example" {
  ami           = "ami-123456"
  instance_type = "t2.micro"

  lifecycle {
    prevent_destroy = true
  }
}
```
✅ **주의사항**  
- 이 설정이 적용된 리소스는 **수정이 불가능한 경우가 있음** (예: EC2 AMI 변경 시 리소스를 삭제하고 다시 생성해야 하는데, `prevent_destroy`로 인해 불가능해짐).  

---

### **Q5: `ignore_changes`란?**  
✅ **Terraform이 특정 속성의 변경을 무시하도록 설정**  
✅ **사용 예시**  
- **AWS EKS 노드 그룹의 `desired_size`를 `Cluster Autoscaler`가 관리하는 경우**  
```hcl
resource "aws_eks_node_group" "example" {
  cluster_name    = aws_eks_cluster.example.name
  node_group_name = "example"
  node_role_arn   = aws_iam_role.example.arn

  scaling_config {
    desired_size = 1
    min_size     = 1
    max_size     = 15
  }

  lifecycle {
    ignore_changes = [scaling_config[0].desired_size]
  }
}
```
✅ **주의사항**  
- `ignore_changes`를 설정하면 Terraform은 해당 속성의 변경을 감지하지 않음.  
- 외부에서 설정이 변경되더라도 `terraform apply` 시 덮어쓰지 않음.  

---

### **Q6: `replace_triggered_by`란?**  
✅ **다른 리소스가 변경될 때 특정 리소스를 강제로 재생성**  
✅ **예제**  
```hcl
resource "aws_instance" "example" {
  ami           = "ami-123456"
  instance_type = "t2.micro"

  lifecycle {
    replace_triggered_by = [aws_ami.latest.id]
  }
}
```
✅ **주의사항**  
- `variable` 또는 `locals`는 `replace_triggered_by`의 값으로 사용할 수 없음.  
- 대신 `terraform_data` 리소스를 활용해야 함.  

---

### **Q7: `precondition`과 `postcondition`이란?**  
✅ **리소스 생성 전(`precondition`), 생성 후(`postcondition`) 특정 조건을 검증**  
✅ **예제**  
```hcl
resource "aws_instance" "example" {
  instance_type = "t2.micro"
  ami           = "ami-123456"

  lifecycle {
    precondition {
      condition     = data.aws_ami.example.architecture == "x86_64"
      error_message = "The selected AMI must be for the x86_64 architecture."
    }
  }
}
```
✅ **주의사항**  
- `precondition` 실패 시 리소스 생성 자체가 중단됨.  
- `postcondition` 실패 시 리소스는 생성되지만 에러가 발생할 수 있음.  

---

# **핵심 요약**  
- **Terraform `lifecycle` 블록을 활용하면 리소스의 생성/업데이트/삭제 동작을 제어 가능**.  
- **`create_before_destroy`** → 새 리소스를 먼저 생성한 후 기존 리소스 삭제.  
- **`prevent_destroy`** → 리소스 삭제 방지 (DB, 프로덕션 서버 등에 유용).  
- **`ignore_changes`** → 특정 속성 변경을 무시 (예: `desired_size` 등).  
- **`replace_triggered_by`** → 특정 리소스 변경 시 강제 재생성.  
- **`precondition` / `postcondition`** → 리소스 생성 전후 조건 검증.  
# 핵심 필기
## Lifecycle이란?
- `resource` 블록에 같이 사용할 수 있는 중첩 블록으로 내부엔 `meta-arguments`가 들어간다.
## Lifecycle 내부에 사용 가능한 블록
### `create_before_destroy`
- Terraform은 기본적으로 업데이트 되는 인프라 객체를 제자리에서 업데이트한다.
- 그러나, 수정 사항이 제자리에서 처리가 불가능한 경우엔 해당 인프라 객체를 삭제하고 새로운 객체를 만든다.
- `create_before_destroy`은 먼저 업데이트 되는 객체를 만들고 이후에 이전에 만들어진 객체(구버전 인프라)를 삭제한다.
- 만약, 리소스 A가 `create_before_destroy`가 활성화된 상태인데 리소스 B에 의존하고 있는 상태라면 리소스 B도 `create_before_destroy`가 암묵적으로 설정된다.
- `create_before_destroy`가 true이면 destroy provisioner는 동작하지 않는다.[^1]
- 참고로 tag에서 Name을 바꾸면 `create_before_destroy`가 동작하지 않는다.
	- Name이 바뀌면 서로 다른 리소스로 파악 -> 기존에 인스턴스를 제거하고 별개로 다른 인스턴스가 만들어진다고 판단 -> 제거와 생성이 동시에 이뤄짐.
``` hcl
locals {
  ec2_names = toset(["web4"]) # set이 식별자로 쓰이므로 이 값이 변경되면 리소스를 제거하고 별개로 인스턴스를 만듬.
}

resource "aws_instance" "test" {
  for_each      = local.ec2_names
  ami           = "ami-0a2c043e56e9abcc5"
  instance_type = "t2.micro"

  tags = {
    Name = "test-${each.value}"
  }

  lifecycle {
    create_before_destroy = true
  }
}
``` 

- 아래처럼 식별자를 `for_each`를 통해 식별자를 지정하면 별개의 리소스로 판단하지 않고 동일한 리소스로 판단하여 제자리 업데이트(in-place update)를 진행한다.
``` hcl
variable "ubuntu_ami_id" {
  type = string
  description = "Ubuntu Image id supported by aws"
  default = "ami-024ea438ab0376a47"
}

locals {
  ec2_spec = {
		# 이 최상위 키인 web1이 식별자로 쓰이므로 리소스가 변경되지 않았다고 판단. ami가 변경되면 create_before_destroy가 진행됨.
    "web1" = {
      name = "web11111",
      instance_type = "t2.micro"
      ami = var.ubuntu_ami_id 
    }
  } 
}

resource "aws_instance" "test" {
  for_each      = local.ec2_spec
  ami           = each.value.ami 
  instance_type = each.value.instance_type

  tags = {
    Name = "test-${each.value.name}"
  }

  lifecycle {
    create_before_destroy = true
  }
}
```
![Pasted image 20250206125342.png](/img/user/image/Pasted%20image%2020250206125342.png)
### `prevent_destroy`
- 매개 변수를 `boolean`으로 받는다.
- 이 `meta-arguments`를 가진 리소스가 destroy되는 plan을 모두 거절한다.
- Database 같이 재생성에 비싼 비용이 드는 리소스들에 사용하면 좋을 옵션
- 단, 이 옵션으로 인해 몇몇 설정이 `apply`가 불가능해질 수도 있음. (예를 들면, 해당 리소스를 무조건 제거하고 새로 만들 필요가 있으면 `prevent_destroy`가 업데이트를 막을 것)
	- 예를 들어, ec2의 ami 변경은 in-place 업데이트가 불가능하다. 따라서, ec2를 제거하고 새로 만들 필요가 있다. `prevent_destroy`는 이러한 업데이트를 위한 제거도 막는다.
- 이 옵션을 설정하면 `terraform destroy` 명령어 사용도 불가능해짐.
### `ignore_changes`
- 매개 변수를 `list(string)`으로 받는다.
- Terraform은 기본적으로 모든 변화를 알아챈다.
- 그러나, 가끔 Terraform이 아닌 외부에 의해 설정이 변경된다.
- 외부에 의해 설정이 변경된 상태에서 `terraform apply`를 적용한다면 변경된 상태를 원래대로 되돌리려고 할 것이다.
- 이를 방지하기 위해서 사용하는 것이 `ignore_changes`.
- 실제 예시
	- `aws_eks_node_group`의 노드 개수가 있다. 스케일링이 일어나면 노드 개수는 `clusterautoscaler`라는 pod에 의해 조절된다. 따라서, 노드 개수가 변경된다.
	- `ignore_changes`로 등록하지 않는다면 다음 실행 때, 스케일링된 노드 개수를 Terraform이 원상 복귀시킬 것이다.
``` hcl
resource "aws_eks_node_group" "sp-monitoring" {
  cluster_name    = aws_eks_cluster.sp-eks.name
  version         = var.eks_version
  node_group_name = "sp-monitoring"
  node_role_arn   = aws_iam_role.nodes.arn
	...
	...
  scaling_config {
    desired_size = 1
    min_size     = 1
    max_size     = 15
  }

  update_config {
    max_unavailable = 1
  }

  labels = {
    role = "sp-monitoring"
  }

  lifecycle {
    ignore_changes = [scaling_config[0].desired_size]
  }
}
```

### `replace_triggered_by`
- 매개변수를 `list(string)`으로 받는다. 
- `plan` 기반으로 동작하므로 `variable`이나 `locals`는 값으로 올 수 없다. 
	- `variable`이나 `locals`를 값으로 받고 싶다면 `terraform_data`[^2]를 사용해라.
- 특정 리소스의 변화가 발생하면 `replace_triggered_by`를 명시한 리소스를 재생성(replace)한다.
- Trigger 기준
	- 리소스를 명시한 경우, 여러 개의 인스턴스를 가진 `resource`가 한 개 이상의 인스턴스를 update하거나 replace를 하는 경우
	- 리소스를 명시한 경우, 한 개의 인스턴스를 가진 `resource`가 update하거나 replace 하는 경우
	- attribute를 명시한 경우, 해당 인스턴스의 attribute가 변경된 경우

---
### Condition Check
- `precondition`과 `postcondition`으로 직접 설정한 체크 가능
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

# References
https://developer.hashicorp.com/terraform/language/meta-arguments/lifecycle

[^1]: https://github.com/hashicorp/terraform/issues/13549

[^2]: https://developer.hashicorp.com/terraform/language/resources/terraform-data