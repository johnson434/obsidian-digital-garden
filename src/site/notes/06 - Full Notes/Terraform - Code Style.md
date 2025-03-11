---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-code-style/","noteIcon":""}
---

# **Tags**

- [[03 - Tags/Terraform\|Terraform]]

---

# **단서 질문 및 답변**

### **Q1: Terraform에서 `terraform fmt` 명령어의 역할은?**

✅ Terraform 코드의 **자동 정렬 및 스타일 일관성 유지**를 위해 사용됨.  
✅ 띄어쓰기, 들여쓰기, 줄바꿈 등을 표준 스타일로 맞춰줌.

---

### **Q2: Terraform에서 리소스 명명 규칙은 어떻게 해야 하는가?**

✅ **명사형으로 작성**.  
✅ 리소스명에 **리소스 타입을 포함하지 않음**.  
❌ 잘못된 예제:

```hcl
resource "aws_instance" "aws_instance_1" {}
```

✅ 올바른 예제:

```hcl
resource "aws_instance" "web_server" {}
```

✅ 리소스명 사이에는 **언더스코어(`_`) 사용**.

---

### **Q3: Terraform 코드 작성 시 변수(`variable`)는 어떻게 정의해야 하는가?**

✅ `type`과 `description`을 반드시 포함.  
✅ `default` 값은 선택 사항 (필요할 때만 추가).  
✅ 민감한 정보(`sensitive`)는 `true`로 설정.  
✅ 유효성 검사(`validation`)를 추가하여 입력값 검증 가능.

✅ **예제**

```hcl
variable "instance_count" {
  type        = number
  description = "Number of EC2 instances to create"
  default     = 2
}

variable "environment" {
  type        = string
  description = "Deployment environment"
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be one of 'dev', 'staging', or 'prod'."
  }
}
```

---

### **Q4: Terraform 코드에서 `meta-argument`의 위치는 어디에 두어야 하는가?**

✅ `count`, `for_each` 같은 **meta-argument를 가장 먼저 작성**.  
✅ `lifecycle` 블록은 마지막에 배치.

✅ **올바른 코드 스타일 예제**

```hcl
resource "aws_instance" "example" {
  count = 2  # meta-argument first

  ami           = "ami-123456"
  instance_type = "t2.micro"

  network_interface {
    device_index = 0
  }

  lifecycle {  # meta-argument block last
    create_before_destroy = true
  }
}
```

---

### **Q5: Terraform 코드에서 디렉터리 구조를 어떻게 구성하는 것이 좋은가?**
✅ **환경별 디렉터리 분리 (`dev`, `prod`, `staging`)**.  
✅ **모듈(`modules`)과 환경별 코드(`dev/`, `prod/`, `staging/`)를 분리**.
✅ **디렉터리 구조 예제**

```
.
├── modules              # 재사용 가능한 모듈
│   ├── compute
│   │   └── main.tf
│   ├── database
│   │   └── main.tf
│   └── network
│       └── main.tf
├── dev
│   ├── backend.tf
│   ├── main.tf
│   └── variables.tf
├── prod
│   ├── backend.tf
│   ├── main.tf
│   └── variables.tf
└── staging
    ├── backend.tf
    ├── main.tf
    └── variables.tf
```

✅ **이 방식의 장점**
- 각 환경이 **별도의 `.tfstate` 파일을 가지므로 충돌 없이 독립적으로 배포 가능**.
- 환경별 변수를 `.tfvars`로 관리하는 방식보다 **더 확장성이 좋음**.

---

# **핵심 요약**

- `terraform fmt`을 사용하여 코드 스타일을 통일해야 함.
- 리소스명은 **명사형**, **리소스 타입을 포함하지 않음**, **언더스코어 사용**.
- 변수(`variable`)는 **type, description 필수, 필요 시 default/sensitive 추가**.
- **meta-argument는 첫 줄, `lifecycle` 블록은 마지막 줄에 배치**.
- **환경별 디렉터리(`dev/`, `prod/`, `staging/`)를 만들어 상태 파일을 분리하여 관리**.

---

# **핵심 필기**

## **Terraform Code Style**

### **1. 코드 정렬 (`terraform fmt`)**

✅ **코드 스타일 자동 정렬 및 통일**

```shell
terraform fmt
```

✅ `terraform fmt`을 실행하면 **올바른 들여쓰기 및 스타일 적용됨**.

---

### **2. 리소스 명명 규칙**

✅ **명사로 작성, 리소스 타입 포함 금지**  
✅ **언더스코어(`_`) 사용**

✅ **예제 (올바른 코드 스타일)**

```hcl
resource "aws_instance" "web_server" {}
```

---

### **3. Terraform 파일 구조**

✅ **모듈(`modules`)과 환경별 코드(`dev/`, `prod/`, `staging/`)를 분리**

```
.
├── modules              
│   ├── compute
│   │   └── main.tf
│   ├── database
│   │   └── main.tf
│   └── network
│       └── main.tf
├── dev
│   ├── backend.tf
│   ├── main.tf
│   └── variables.tf
├── prod
│   ├── backend.tf
│   ├── main.tf
│   └── variables.tf
└── staging
    ├── backend.tf
    ├── main.tf
    └── variables.tf
```

✅ **각 환경마다 개별적인 `backend.tf` 파일을 가지므로 서로 다른 `.tfstate` 파일 사용 가능**.

---

### **4. 변수(`variable`) 작성 규칙**

✅ **순서**  
1️⃣ Type  
2️⃣ Description  
3️⃣ Default (Optional)  
4️⃣ Sensitive (Optional)  
5️⃣ Validation (Optional)

✅ **예제 (올바른 코드 스타일)**

```hcl
variable "instance_count" {
  type        = number
  description = "Number of EC2 instances to create"
  default     = 2
}

variable "environment" {
  type        = string
  description = "Deployment environment"
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be one of 'dev', 'staging', or 'prod'."
  }
}
```

---

### **5. Outputs 작성 규칙**

✅ **순서**  
1️⃣ Type  
2️⃣ Description  
3️⃣ Sensitive (Optional)

✅ **예제 (올바른 코드 스타일)**

```hcl
output "instance_id" {
  description = "ID of the EC2 instance"
  value       = aws_instance.web.id
}
```

---

### **6. Provider Aliasing**

✅ **여러 개의 provider를 사용할 때 `alias` 설정 가능**  
✅ **다른 AWS 리전을 사용해야 하는 경우 유용**

✅ **예제 (올바른 코드 스타일)**

```hcl
provider "aws" {
  region = "ap-northeast-2"
}

provider "aws" {
  alias  = "west"
  region = "us-west-2"
}

module "aws_vpc" {
  source = "./aws_vpc"
  providers = {
    aws = aws.west
  }
}
```

---

### **7. 동적 리소스(`count`, `for_each`) 사용 규칙**

✅ **거의 동일한 리소스를 여러 개 생성할 때 `count` 사용**  
✅ **세부 설정이 다르면 `for_each` 사용**

✅ **예제 (`for_each` 사용)**

```hcl
variable "web_instances" {
  type        = list(string)
  description = "List of web instances"
  default     = ["ui", "api", "db", "metrics"]
}

resource "aws_instance" "web" {
  for_each = toset(var.web_instances)
  ami      = data.aws_ami.webapp.id
  instance_type = "t3.micro"

  tags = {
    Name = "web_${each.key}"
  }
}
```

---

# **참고 자료**

- [Terraform 공식 스타일 가이드](https://developer.hashicorp.com/terraform/language/style)
- [Terraform Variables & Outputs Best Practices](https://developer.hashicorp.com/terraform/language/values/variables)

---

## 🚀 **결론**

- **코드 스타일을 통일하면 유지보수성이 높아지고 협업이 쉬워짐**.
- **환경별 디렉터리 분리를 통해 `.tfstate` 충돌 방지 가능**.
- **변수, 출력 값, Provider Aliasing 등의 규칙을 준수하여 코드 품질을 높이는 것이 중요**. 🚀