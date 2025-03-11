---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-module/","noteIcon":""}
---

# **Tags**
- [[03 - Tags/Terraform\|Terraform]]
---

# **단서 질문 및 답변**

### **Q1: Terraform Module이란?**

✅ **Terraform 모듈(Module)은 `.tf` 또는 `.tf.json` 파일로 구성된 디렉터리 단위의 코드 재사용 단위**.  
✅ **하나의 디렉터리는 하나의 모듈을 나타내며, 루트 모듈(메인 디렉터리)과 하위 모듈(별도 디렉터리)이 존재**.  
✅ **Terraform Registry 또는 자체 저장소에 모듈을 배포 가능**.

```hcl
module "vpc" {
  source = "./modules/vpc"  # 로컬 모듈 참조
  vpc_cidr = "10.0.0.0/16"
}
```

---

### **Q2: Terraform 모듈을 사용해야 하는 이유는?**

✅ **코드 재사용성 증가** → 같은 인프라 구성을 여러 번 사용할 수 있음.  
✅ **코드 유지보수 용이** → 기능별로 구성하여 관리가 쉬움.  
✅ **구성 요소의 일관성 유지** → 동일한 인프라 패턴을 적용 가능.  
✅ **멀티 환경 지원** → Dev, Staging, Prod 등 환경에 따라 다른 매개변수로 재사용 가능.

---

# **핵심 필기**

## **1. Terraform 모듈의 기본 개념**

✅ **Terraform에서 모든 디렉터리는 기본적으로 모듈**.  
✅ **루트 모듈**: Terraform 실행을 시작하는 기본 디렉터리.  
✅ **하위 모듈**: 다른 디렉터리에서 불러오는 모듈.

```shell
├── main.tf      # 루트 모듈 (Root Module)
├── variables.tf
├── outputs.tf
├── modules/     # 하위 모듈 디렉터리
│   ├── vpc/     # VPC 관련 하위 모듈
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   ├── outputs.tf
│   ├── compute/ # EC2 관련 하위 모듈
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   ├── outputs.tf
```

---

## **2. Terraform 모듈 사용법**

✅ **하위 모듈을 호출할 때는 `source`를 사용**  
✅ **모듈에서 변수(`variables.tf`)와 출력 값(`outputs.tf`)을 설정 가능**

```hcl
module "vpc" {
  source = "./modules/vpc"  # 로컬 모듈 참조
  vpc_cidr = "10.0.0.0/16"
}
```

✅ **Terraform Registry에서 모듈 가져오기 (공개 모듈 활용 가능)**

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "3.19.0"
  cidr    = "10.0.0.0/16"
}
```

---

## **3. Terraform 모듈 출력 값 (Outputs) 사용하기**

✅ 모듈 내에서 `outputs.tf`를 정의하고 루트 모듈에서 참조 가능

### **모듈 내 `outputs.tf` 설정**

```hcl
output "vpc_id" {
  value = aws_vpc.main.id
}
```

### **루트 모듈에서 출력 값 참조**

```hcl
output "vpc_id" {
  value = module.vpc.vpc_id
}
```

---

## **4. Terraform 모듈 상태 관리 (`move` 블록 활용)**

✅ **Terraform은 `.tfstate` 파일을 기반으로 리소스를 관리**.  
✅ **모듈을 적용하면 리소스의 경로가 변경되므로 기존 리소스가 삭제되고 새로 생성될 위험이 있음**.  
✅ **이를 방지하기 위해 `move` 블록을 사용하여 `.tfstate` 내의 경로를 수정 가능**.

```hcl
move {
  from = aws_instance.test
  to   = module.web.aws_instance.test
}
```

✅ **이렇게 하면 기존 리소스를 새 모듈 내 리소스로 이동 가능**.  
✅ **불필요한 리소스 삭제 및 재생성을 방지**.

---

## **5. Terraform 모듈 내 특정 리소스만 재생성 (`replace` 명령어 사용)**

✅ **Terraform에서는 특정 리소스를 강제로 재생성 가능**

```shell
$ terraform plan -replace=module.example.aws_instance.example
```

✅ **전체 인프라 변경 없이 특정 리소스만 교체 가능**.  
✅ **리소스를 새로 만들어야 하는 경우 유용 (예: EC2 인스턴스 재배포)**.

---

## **6. Terraform 모듈 제거 (`removed` 블록 활용)**

✅ **`.tfstate`에서 모듈을 제거하되, 실제 인프라는 유지하려면 `removed` 블록을 사용**.  
✅ **특정 모듈을 제거할 때 유용 (예: `terraform destroy`를 실행하지 않고 모듈만 관리에서 제외할 경우)**.

```hcl
removed {
  from = module.eks
  lifecycle {
    destroy = false  # 인프라 삭제 없이 Terraform에서만 제거
  }
}
```

✅ **이렇게 하면 Terraform 상태 파일에서만 제거되고 실제 리소스는 유지됨**.

---

## **7. `count` 및 `for_each`를 사용한 모듈 반복 생성**

✅ **여러 개의 동일한 리소스를 생성할 때 `count` 또는 `for_each`를 활용 가능**.

### **`count`를 활용한 모듈 반복 생성**

```hcl
module "ec2_instances" {
  source = "./modules/ec2"
  count  = 3  # 3개의 EC2 인스턴스 생성
}
```

### **`for_each`를 활용한 모듈 반복 생성**

```hcl
locals {
  ec2_configs = {
    "web"  = { instance_type = "t2.micro" }
    "db"   = { instance_type = "t3.micro" }
  }
}

module "ec2" {
  source = "./modules/ec2"
  for_each = local.ec2_configs

  instance_type = each.value.instance_type
}
```

✅ **`for_each`를 사용하면 개별 설정이 가능하여 유연함**.  
✅ **`count`는 단순 반복에 적합하지만, 특정 설정이 다를 경우 `for_each` 사용이 더 적절**.

---

# **Terraform Module 정리 요약**

|**기능**|**설명**|**사용 예시**|
|---|---|---|
|**모듈 기본 개념**|`.tf` 파일이 존재하는 디렉터리 단위의 코드 재사용 블록|`module "vpc" { source = "./modules/vpc" }`|
|**출력 값 사용**|모듈에서 생성한 리소스의 ID 등을 참조|`module.vpc.vpc_id`|
|**모듈 내 리소스 이동**|기존 리소스를 모듈로 이동|`move { from = aws_instance.test to = module.web.aws_instance.test }`|
|**리소스 재생성**|특정 리소스만 교체|`$ terraform plan -replace=module.example.aws_instance.example`|
|**모듈 제거**|`.tfstate`에서만 제거, 실제 인프라는 유지|`removed { from = module.eks destroy = false }`|
|**모듈 반복 생성 (`count`)**|같은 설정의 여러 리소스 생성|`count = 3`|
|**모듈 반복 생성 (`for_each`)**|개별 설정이 다른 리소스 생성|`for_each = local.ec2_configs`|

---

# **참고 자료**

- [Terraform 공식 문서 - 모듈](https://developer.hashicorp.com/terraform/language/modules)
- [Terraform 공식 문서 - 모듈 상태 관리](https://developer.hashicorp.com/terraform/language/modules/syntax)
- [Terraform 공식 문서 - 리소스 이동 (`move`)](https://developer.hashicorp.com/terraform/language/state/move)

---

## 🚀 **결론**

- **Terraform 모듈을 활용하면 코드 재사용성과 유지보수성이 향상됨**.
- **`move` 블록을 활용하면 리소스를 안전하게 모듈로 이동 가능**.
- **모듈 제거 시 `removed` 블록을 활용하면 리소스를 삭제하지 않고 Terraform에서만 관리 해제 가능**.
- **반복 생성이 필요할 경우 `count` 또는 `for_each`를 적절히 활용하여 관리! 🚀**