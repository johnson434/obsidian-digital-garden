---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-ephemeral-resource/","noteIcon":""}
---

### **Terraform `Ephemeral Resource` 정리**

---

# **Tags**

- [[03 - Tags/Terraform\|Terraform]]
- [[Terraform Ephemeral Resource\|Terraform Ephemeral Resource]]
- [[Terraform Secrets\|Terraform Secrets]]
- [[Terraform Dynamic Credentials\|Terraform Dynamic Credentials]]
- [[06 - Full Notes/2025-02\|2025-02]]

---

# **단서 질문 및 답변**

### **Q1: Terraform `ephemeral` 리소스란?**

✅ **일시적인(Transient) 리소스**로, **Terraform `state` 파일에 저장되지 않음**.  
✅ `resource`와 달리, **모듈 내에서만 유효하며 외부에서 참조할 수 없음**.  
✅ **즉시 실행되며, 해당 리소스를 참조하는 다른 리소스보다 먼저 생성됨**.

---

### **Q2: Terraform `ephemeral` 리소스는 언제 사용하면 유용한가?**

✅ **임시 자격 증명(`credentials`)을 가져오거나 처리할 때**.  
✅ **보안 상 민감한 데이터를 Terraform 상태(`state`)에 저장하지 않고 사용할 때**.  
✅ **일시적으로 생성 및 사용할 값이 필요하지만, Terraform이 이를 관리할 필요가 없을 때**.

✅ **예제: AWS Secrets Manager에서 DB 자격 증명 조회**

```hcl
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

✅ **Terraform의 `state` 파일에 DB 자격 증명이 저장되지 않음**, 즉 **보안 강화**.

---

### **Q3: Terraform `ephemeral` 리소스는 어떻게 동작하는가?**

✅ **기본 동작**

- `ephemeral` 리소스는 **모듈 내에서만 사용 가능**.
- 생성된 값은 **다른 리소스가 이를 참조할 때 즉시 사용 가능**.
- **Terraform `plan`과 `state`에 저장되지 않음**.

✅ **생성 순서**

1. **`ephemeral` 리소스가 가장 먼저 프로비저닝됨**.
2. **이를 참조하는 다른 리소스가 프로비저닝됨**.
3. **Terraform 적용 후 `ephemeral` 리소스는 자동 삭제됨**.

✅ **보안성 강화 (민감한 데이터 보호)**

- 일반적인 Terraform 리소스(`resource`)는 `.tfstate`에 저장되지만, `ephemeral`은 저장되지 않음.
- **자격 증명, API 키 등의 보안 민감한 데이터를 관리할 때 유용**.

---

### **Q4: Terraform `ephemeral` 리소스는 일반 `resource`와 어떤 차이가 있는가?**

|**특징**|**`resource` (일반 리소스)**|**`ephemeral` (일시적 리소스)**|
|---|---|---|
|**Terraform `state` 저장 여부**|✅ 저장됨|❌ 저장되지 않음|
|**`plan` 단계에서 조회 가능 여부**|✅ 가능|❌ 불가능|
|**모듈 외부에서 참조 가능 여부**|✅ 가능|❌ 불가능|
|**프로비저닝 순서**|일반적인 순서|항상 가장 먼저 실행됨|
|**보안성**|민감한 데이터가 `state`에 저장될 가능성 있음|`state`에 저장되지 않아 보안성 높음|

✅ **즉, `ephemeral`은 Terraform 실행이 끝나면 사라지는 리소스**.

---

# **핵심 요약**

- **Terraform `ephemeral` 리소스는 일시적인(transient) 리소스로, Terraform `state`에 저장되지 않음**.
- **모듈 내에서만 유효하며, 다른 모듈이나 외부에서 참조할 수 없음**.
- **민감한 데이터(예: API 키, DB 자격 증명)를 보호하는 데 유용함**.
- **참조하는 리소스보다 항상 먼저 생성되며, Terraform 실행 후 사라짐**.

---

# **핵심 필기**

## **1. Terraform `ephemeral` 리소스란?**

✅ **Terraform에서 일시적으로 사용되는 리소스**.  
✅ Terraform **`plan`이나 `state`에 저장되지 않음**.  
✅ 일반 `resource`와 달리 **모듈 내에서만 의미가 있음**.

✅ **예제: AWS Secrets Manager에서 임시 자격 증명 조회**

```hcl
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

✅ **이 방식은 자격 증명을 `state` 파일에 저장하지 않으므로 보안성이 강화됨**.

---

## **2. Terraform `ephemeral` 리소스 동작 원리**

✅ **Terraform 적용 단계에서 실행 흐름**  
1️⃣ **Terraform `ephemeral` 리소스가 가장 먼저 프로비저닝됨**.  
2️⃣ **이를 참조하는 리소스가 프로비저닝됨**.  
3️⃣ **Terraform 적용(`apply`) 후 `ephemeral` 리소스는 삭제됨**.

✅ **예제: 동작 흐름**

```hcl
ephemeral "random_string" "session_id" {
  length  = 16
  special = false
}

output "session_id" {
  value = ephemeral.random_string.session_id.result
}
```

✅ **Terraform 실행 후 `session_id` 값은 확인할 수 있지만, `state`에는 저장되지 않음**.

---

## **3. Terraform `ephemeral` 리소스 사용 예제**

✅ **(1) AWS Secrets Manager에서 민감한 데이터 조회**

```hcl
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

✅ **자격 증명을 `state`에 저장하지 않으므로 보안 강화**.

✅ **(2) 임시 UUID 생성**

```hcl
ephemeral "random_uuid" "session_id" {}

output "session_id" {
  value = ephemeral.random_uuid.session_id.result
}
```

✅ **Terraform 실행 후 `session_id` 값만 사용되고 저장되지 않음**.

✅ **(3) API 인증 키를 임시로 사용**

```hcl
ephemeral "http" "get_token" {
  url = "https://auth.example.com/token"
  method = "POST"
  body = jsonencode({
    client_id     = "example-client"
    client_secret = "example-secret"
  })
}

locals {
  api_token = jsondecode(ephemeral.http.get_token.body)["access_token"]
}

provider "kubernetes" {
  host  = "https://k8s.example.com"
  token = local.api_token
}
```

✅ **API 요청을 통해 임시 토큰을 가져와 사용하지만, `state`에 저장되지 않음**.

---

# **Terraform `ephemeral` 리소스 주요 비교**

|**기능**|**일반 `resource`**|**`ephemeral`**|
|---|---|---|
|**State 저장 여부**|✅ 저장됨|❌ 저장되지 않음|
|**Plan 단계에서 확인 가능 여부**|✅ 가능|❌ 불가능|
|**모듈 외부에서 참조 가능 여부**|✅ 가능|❌ 불가능|
|**보안 민감한 데이터 관리**|❌ `state`에 저장됨|✅ 저장되지 않아 보안 강화|

---

# **참고 자료**

- [Terraform 공식 문서 - Ephemeral Resources](https://developer.hashicorp.com/terraform/language/resources/ephemeral)
- https://developer.hashicorp.com/terraform/language/resources/ephemeral