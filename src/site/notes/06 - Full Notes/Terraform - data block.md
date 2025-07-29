---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-data-block/","noteIcon":""}
---

# **Tags**
- [[03 - Tags/Terraform\|Terraform]]

---

# **단서 질문 및 답변**
### **Q1: Terraform `data` 리소스란?**
 **Terraform이 기존 리소스를 조회(`read-only`)하는 방법을 제공하는 기능**.  
 **관리형 리소스(`resource`)와 달리 새로운 인프라를 생성하지 않고, 기존 인프라의 정보를 가져오는 용도로 사용**.
### **Q2: Terraform `data` 리소스는 언제 `plan`에서 조회하고, 언제 `apply`까지 미루는가?**

 기본적으로 **`plan` 단계에서 데이터 조회**.  
 하지만 아래 경우에는 **`apply` 단계까지 조회를 미룸**:

1. `apply` 직전까지 예측할 수 없는 값인 경우.
2. **현재 `plan`에 의해 변경되는 리소스에 `data` 리소스가 의존하는 경우**.

 **예제 (예측 불가능한 값으로 인해 `apply`까지 조회 미룸)**
```hcl
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}

data "aws_instance" "web_info" {
  instance_id = aws_instance.web.id
}
```

- **`aws_instance.web.id`는 `plan` 단계에서는 알 수 없음**, 따라서 `apply`까지 `data` 조회를 미룸.

---

### **Q3: Terraform `depends_on`을 `data` 권장하지 않는 이유는?**

 `depends_on`을 사용하면 **항상 `apply`까지 조회를 미루게 됨**.  
 **불필요한 의존성이 생기면서 Terraform 실행 속도 저하 가능**.  
 Terraform 0.12 이하에서는 `depends_on`을 사용하면 `data` 리소스가 무조건 `apply` 단계까지 미뤄지기 때문에 **구성이 영원히 수렴하지 않을 가능성이 있음**.

 **잘못된 예제 (`depends_on` 사용)**

```hcl
data "aws_vpc" "selected" {
  depends_on = [aws_vpc.main]
}
```

 **권장하는 방식**
```hcl
data "aws_vpc" "selected" {
  id = aws_vpc.main.id
}
```

 **이렇게 하면 `plan`에서 조회할 수 있으면 조회하고, 그렇지 않다면 `apply`까지 기다림.**
---
### **Q4: Terraform `data` 리소스에서 지원하는 `meta-arguments`는?**
 **일반 리소스(`resource`)와 동일한 `meta-arguments`를 지원**.  
 단, **`lifecycle` 블록은 지원하지 않음**.
 **예제 (`count`, `for_each` 사용 가능)**
```hcl
data "aws_ami" "latest" {
  count = 2
  most_recent = true
  owners      = ["self"]
}
```

---

# **핵심 요약**
- **Terraform `data` 리소스는 기존 인프라에서 정보를 조회하는 용도로 사용됨**.
- **기본적으로 `plan` 단계에서 데이터를 조회하지만, 예측할 수 없는 값이나 변경되는 리소스에 의존하면 `apply`까지 조회를 미룸**.
- **`depends_on`을 사용하면 항상 `apply`까지 조회를 미루므로 권장되지 않음**.
- **관리형 리소스(`resource`)와 동일한 `meta-arguments`를 지원하지만, `lifecycle` 블록은 제외됨**.

---

# **핵심 필기**

## **1. Terraform `data` 리소스란?**
 Terraform이 **기존 인프라 정보를 조회**하는 기능을 제공.  
 **새로운 리소스를 생성하지 않고, 기존 리소스의 속성을 읽어오기 위해 사용됨**.  
 일반적으로 **`plan` 단계에서 데이터를 조회**하지만, **특정 경우에는 `apply` 단계까지 미룸**.

 **기본 문법**
```hcl
data "aws_ami" "latest" {
  most_recent = true
  owners      = ["self"]
}
```

 **`.tfstate` 파일에 저장되지 않고 매번 실행 시점에 데이터를 조회**.

---

## **2. Terraform `data` 리소스의 조회 타이밍**
 **`plan` 단계에서 조회 (기본 동작)**
```hcl
data "aws_availability_zones" "available" {}
```

 **`apply`까지 조회를 미루는 경우**
```hcl
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}

data "aws_instance" "web_info" {
  instance_id = aws_instance.web.id
}
```

 **예측할 수 없는 값(`aws_instance.web.id`)이 존재하므로 `apply` 단계까지 대기**.

---
## **3. Terraform `data` 리소스와 `depends_on` 문제점**
 **`depends_on` 사용 시 무조건 `apply`까지 대기하므로 비효율적**.  
 **권장되지 않는 방식 (`depends_on` 사용)**
```hcl
data "aws_vpc" "selected" {
  depends_on = [aws_vpc.main]
}
```

 **권장하는 방식 (`id`를 직접 참조)**
```hcl
data "aws_vpc" "selected" {
  id = aws_vpc.main.id
}
```
 **이 방식은 `plan`에서 조회 가능하면 조회하고, 그렇지 않다면 `apply`까지 미룸.**

---

## **4. Terraform `data` 리소스에서 지원하는 `meta-arguments`**
 **지원되는 `meta-arguments`**
- `count`
- `for_each`
- `provider`
 **지원되지 않는 `meta-arguments`**
- `lifecycle` (일반 리소스와 다름)
 **예제 (`count` 사용)**
```hcl
data "aws_ami" "latest" {
  count = 2
  most_recent = true
  owners      = ["self"]
}
```

---

## **5. Terraform `data` 리소스 주요 예제**
 **AWS VPC 정보 조회**
```hcl
data "aws_vpc" "selected" {
  id = "vpc-0abc12345def67890"
}
```
 **AWS EC2 인스턴스 정보 조회**
```hcl
data "aws_instance" "web" {
  instance_id = "i-0abcdef1234567890"
}
```
 **AWS S3 버킷 정보 조회**
```hcl
data "aws_s3_bucket" "my_bucket" {
  bucket = "my-bucket-name"
}
```
 **AWS Security Group 정보 조회**
```hcl
data "aws_security_group" "web_sg" {
  name = "web-sg"
}
```

---
# **Terraform `data` 리소스 주요 비교**

| **기능**                     | **설명**                                | **사용 사례**        |
| -------------------------- | ------------------------------------- | ---------------- |
| **기본 조회 (`plan`에서 실행)**    | 기본적으로 `plan` 단계에서 실행                  | AWS AZ 목록 조회     |
| **`apply`까지 조회 미룸**        | 값이 예측 불가능할 때 `apply` 단계에서 실행          | 신규 EC2 ID 조회     |
| **`depends_on` 사용 (권장 X)** | 항상 `apply`까지 대기                       | `apply` 지연 문제 발생 |
| **지원되는 `meta-arguments`**  | `count`, `for_each`, `provider` 사용 가능 | 다중 리소스 조회 가능     |

---
# **Terraform `data` 리소스 사용 시 주의점**
 **1. 가능하면 `plan`에서 조회하도록 코드 작성**.  
 **2. `depends_on`을 사용하면 항상 `apply`까지 대기하므로 사용을 지양해야 함**.  
 **3. `.tfstate`에 저장되지 않으므로 항상 최신 데이터를 조회**.  
 **4. `lifecycle` 블록은 지원되지 않음**.
--
# **참고 자료**
- [Terraform 공식 문서 - Data Sources](https://developer.hashicorp.com/terraform/language/data-sources)
- [Terraform Data Source Dependencies](https://developer.hashicorp.com/terraform/language/data-sources#data-resource-dependencies)