---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-terraform-data/","noteIcon":""}
---

# **Tags**

- [[03 - Tags/Terraform\|Terraform]]

---

# **단서 질문 및 답변**

### **Q1: `terraform_data`란?**

✅ `terraform_data`는 **리소스 라이프사이클을 따르는 데이터를 저장하는 용도**로 사용됨.  
✅ `terraform_data`는 **상태 파일(`.tfstate`)에서 관리되며, 리소스가 변경되면 함께 변경됨**.  
✅ **특정 리소스 변경(trigger)에 반응하여 추가 작업을 실행**하는 데 활용됨.

### **Q2: `terraform_data`는 언제 사용해야 하는가?**

✅ `replace_triggered_by`의 **값으로 `variable` 또는 `locals`를 직접 사용할 수 없을 때**.  
✅ `null_resource`를 **대체하여 특정 트리거를 기반으로 프로비저닝할 때**.  
✅ 특정 데이터 값을 **리소스 라이프사이클과 함께 변경하고 싶을 때**.

---

# **핵심 필기**

## **1. `terraform_data` 개요**

✅ Terraform에서 **리소스 라이프사이클을 따르는 데이터를 저장하는 특별한 리소스**.  
✅ Terraform 상태 파일(`.tfstate`)에서 관리되며, **일반적인 리소스처럼 동작**.  
✅ 다른 리소스와 **종속성을 가질 수 있으며, 변경 시 `replace` 트리거 가능**.

### **🔥 주요 특징**

- **리소스처럼 동작하므로 `lifecycle`을 정의할 수 있음**.
- **`replace_triggered_by`를 사용할 때 `variable`이나 `locals`를 직접 사용할 수 없지만, `terraform_data`를 통해 간접적으로 적용 가능`**.
- **기존 `null_resource`를 대체할 수 있음**.

---

## **2. 사용 예시**

### **🛠️ 1. `replace_triggered_by`에서 `variable` 및 `locals`를 간접적으로 사용하기**

✅ `replace_triggered_by`는 Terraform의 `plan` 단계에서 실행되므로, **`variable`이나 `locals`를 직접 사용할 수 없음**. [^1]
✅ 따라서 **`terraform_data`를 통해 간접적으로 참조**하여 사용 가능.

```hcl
variable "ubuntu_ami_id" {
  type = string
  description = "Ubuntu Image ID supported by AWS"
  default = "ami-024ea438ab0376a47"
}

locals {
  ec2_spec = {
    "web1" = {
      name          = "web1"
      instance_type = "t2.micro"
      ami           = var.ubuntu_ami_id
    }
  }
}

# terraform_data를 통해 local 값을 상태 파일에 저장하여 사용
resource "terraform_data" "replace" {
  input = local.ec2_spec
}

resource "aws_instance" "test" {
  ami           = terraform_data.replace.input["web1"].ami
  instance_type = terraform_data.replace.input["web1"].instance_type

  tags = {
    Name = terraform_data.replace.input["web1"].name
  }

  lifecycle {
    replace_triggered_by = [terraform_data.replace]
  }
}
```

✅ **변경 사항 발생 시(`local.ec2_spec` 수정 시), `aws_instance.test`가 자동으로 재생성됨**.  
✅ **Terraform의 `plan` 단계에서 값을 미리 알 수 있도록 `terraform_data`를 사용하여 해결**.

---

### **🛠️ 2. `null_resource` 대체 (`provisioner` 사용)**

✅ `null_resource`는 트리거 기반 작업을 실행할 때 사용되었으나, `terraform_data`로 대체 가능.  
✅ 특정 리소스의 변경을 감지하여 스크립트 실행 등 **추가 작업을 수행** 가능.

```hcl
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t3.micro"
}

resource "aws_instance" "database" {
  ami           = "ami-654321"
  instance_type = "t3.micro"
}

# 특정 인스턴스의 변경을 감지하여 로컬 프로비저너 실행
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

✅ **`triggers_replace`를 사용하여 특정 리소스(`aws_instance.web`, `aws_instance.database`)가 변경될 때 `local-exec` 프로비저너를 실행**.  
✅ **Terraform의 상태 파일(`.tfstate`)에서 관리되므로, 종속된 리소스 변경 시 자동 실행 가능**.

---

# **Terraform `terraform_data` 요약**

|**항목**|**설명**|
|---|---|
|**기능**|리소스 라이프사이클을 따르는 데이터 저장|
|**주요 활용**|`replace_triggered_by` 값으로 `variable`이나 `locals` 사용 시 간접 참조|
|**기존 대체 가능 기능**|`null_resource` 대체 (트리거 기반 프로비저닝)|
|**관리 방식**|`.tfstate`에서 Terraform 리소스처럼 관리됨|
|**사용 예시**|`replace_triggered_by` + `terraform_data`, `provisioner` 실행|

---

# **참고 자료**

- [Terraform 공식 문서 - terraform_data](https://developer.hashicorp.com/terraform/language/resources/terraform-data)
- [Terraform 공식 문서 - replace_triggered_by](https://developer.hashicorp.com/terraform/language/meta-arguments/lifecycle#replace_triggered_by)
- [Terraform 공식 문서 - provisioner](https://developer.hashicorp.com/terraform/language/resources/provisioners)

---

## 🚀 **결론**

✅ **`terraform_data`는 Terraform의 상태 관리 시스템을 활용하여 데이터를 저장하고 리소스 라이프사이클을 따르게 하는 기능**.  
✅ **`replace_triggered_by`에서 `variable` 및 `locals`를 사용할 수 없을 때 해결책으로 활용**.  
✅ **기존 `null_resource`를 대체하여 트리거 기반 프로비저닝 가능**.  
✅ **Terraform에서 특정 리소스 변경을 감지하고 추가 작업을 수행하는 데 유용함! 🚀**

[^1]: apply 시점에서 variable이나 locals가 변경될 수 있으므로, plan 시점에서 변경될 지 결정이 되어야 하는 `replace_triggered_by`에선 사용이 불가능하다. 
