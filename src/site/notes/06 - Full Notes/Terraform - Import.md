---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-import/","noteIcon":""}
---

# **Tags**  
- [[03 - Tags/Terraform\|Terraform]]  
---

# **단서 질문 및 답변**  

### **Q1: Terraform `import`란?**  
✅ **Terraform 외부에서 생성된 리소스를 `.tfstate`에 추가하는 기능**.  
✅ **기존 인프라를 Terraform 코드로 관리 가능**.  
✅ **단, `terraform import`는 `.tf` 코드 자체를 생성하지 않음 → 수동으로 작성 필요**.  

---

### **Q2: Terraform `import`의 동작 방식은?**  
✅ **Terraform `import`를 실행하면 `.tfstate` 파일에 리소스 정보가 추가됨**.  
✅ **Terraform 코드(`.tf` 파일)는 직접 작성해야 함**.  
✅ **즉, Terraform은 기존 리소스를 가져와 관리할 수 있지만, 코드 자동 생성 기능은 없음**.  

---

### **Q3: Terraform `import`의 기본 문법은?**  
✅ **블록 방식** (`.tf` 파일에서 사용 가능)  
```hcl
import {
  to = aws_instance.example
  id = "i-abcd1234"
}
```
✅ **CLI 방식** (`terraform import` 명령어 사용)  
```sh
terraform import aws_instance.example i-abcd1234
```
✅ **`.tf` 파일에 리소스를 정의해야 Terraform에서 계속 관리 가능**  
```hcl
resource "aws_instance" "example" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
  # (other attributes...)
}
```

---

### **Q4: Terraform `import` 실행 후 주의할 점은?**  
✅ **Terraform `import`는 `.tfstate` 파일만 수정하고 `.tf` 파일은 변경하지 않음**  
✅ **따라서 `.tf` 코드가 `.tfstate`와 일치하는지 확인해야 함 (`terraform plan` 활용)**  
✅ **수정이 필요한 경우 `.tf` 파일을 직접 변경 후 `terraform apply` 실행**  

---

# **핵심 요약**  
- **Terraform `import`는 기존 리소스를 `.tfstate` 파일에 추가하여 관리 가능하게 함**.  
- **단, `.tf` 파일은 직접 작성해야 하며, 자동 생성되지 않음**.  
- **Terraform `plan`을 실행하여 상태가 일치하는지 반드시 확인 필요**.  

---

# **핵심 필기**  

## **1. Terraform `import` 개요**  
✅ **Terraform 외부에서 생성된 리소스를 가져와 관리할 수 있음**.  
✅ **하지만 `.tf` 파일은 자동 생성되지 않으므로 직접 작성해야 함**.  
✅ **즉, Terraform `import`는 `.tfstate`를 업데이트할 뿐, 코드 변경은 자동으로 이루어지지 않음**.  

---

## **2. Terraform `import` 사용법**  

✅ **방법 1: 블록 방식 (`.tf` 파일에서 사용)**  
```hcl
import {
  to = aws_instance.example
  id = "i-abcd1234"
}
```

✅ **방법 2: CLI 방식 (`terraform import` 명령어 사용)**  
```sh
terraform import aws_instance.example i-abcd1234
```

✅ **Terraform 코드 (`.tf` 파일) 예제**  
```hcl
resource "aws_instance" "example" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}
```

---

## **3. Terraform `import` 실행 흐름**  

1️⃣ **Terraform 외부에서 생성된 리소스가 존재**  
```sh
aws ec2 describe-instances --instance-ids i-abcd1234
```
✅ `i-abcd1234` 인스턴스가 존재한다고 가정  

2️⃣ **Terraform `import` 실행 (CLI 방식)**  
```sh
terraform import aws_instance.example i-abcd1234
```
✅ `.tfstate` 파일에 해당 리소스가 추가됨  

3️⃣ **Terraform 코드 (`.tf` 파일) 직접 작성**  
```hcl
resource "aws_instance" "example" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}
```

4️⃣ **Terraform 상태 확인 (`terraform plan`)**  
```sh
terraform plan
```
✅ **`.tfstate`와 `.tf` 파일이 일치하지 않는다면 수정 필요**  

---

## **4. Terraform `import` 실행 후 주의사항**  

✅ **`.tfstate`는 변경되지만 `.tf` 파일은 자동 생성되지 않음**  
✅ **따라서 `terraform plan`을 실행하여 상태를 확인해야 함**  
✅ **`.tf` 파일이 `.tfstate`와 다를 경우 직접 수정 후 `terraform apply` 실행**  

---

# **Terraform `import` 주요 비교**  

| **특징** | **설명** |  
|----------|---------|  
| **Terraform `import`** | 기존 리소스를 `.tfstate`에 추가 |  
| **자동 코드 생성 여부** | ❌ `.tf` 파일은 자동 생성되지 않음 |  
| **CLI 명령어 방식** | `terraform import aws_instance.example i-abcd1234` |  
| **`.tfstate` 반영 여부** | ✅ `.tfstate` 파일에 리소스 추가 |  
| **추가 작업 필요 여부** | `.tf` 파일을 직접 작성해야 함 |  

---

# **참고 자료**  
- [Terraform 공식 문서 - Import](https://developer.hashicorp.com/terraform/language/import)  