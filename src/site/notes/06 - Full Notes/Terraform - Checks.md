---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-checks/","noteIcon":""}
---

# **Tags**
- [[03 - Tags/Terraform\|Terraform]]

---

# **단서 질문 및 답변**

### **Q1: Terraform `check` 블록이란?**

✅ Terraform에서 `plan` 또는 `apply` 단계에서 특정 리소스의 상태를 검증하는 기능.  
✅ 외부 API, 모니터링 시스템, 네트워크 상태 등을 확인 가능.

---

### **Q2: Terraform `check` 블록을 사용하면 어떤 장점이 있는가?**

✅ 배포 전에 특정 조건을 검증하여 실패를 방지할 수 있음.  
✅ 인프라 상태를 동적으로 확인하여 **비정상 상태 감지 가능**.  
✅ `assert`를 사용하여 조건이 만족되지 않을 경우 에러를 반환할 수 있음.

---

### **Q3: Terraform `check` 블록의 주요 구성 요소는?**

✅ **`check` 블록**: 특정 검증을 수행하는 블록.  
✅ **`assert` 블록**: `condition`이 참인지 확인하고, 거짓이면 `error_message` 반환.  
✅ **`data` 블록**: 외부 데이터를 조회하여 검증하는 데 사용 가능.

---

### **Q4: Terraform `check` 블록은 어떤 상황에서 유용한가?**

✅ 외부 API나 웹사이트의 가용성을 확인하고 싶을 때.  
✅ **DNS 레코드가 정상적으로 설정되었는지 확인**할 때.  
✅ **데이터베이스 연결이 가능한지 검증**할 때.  
✅ 특정 클라우드 리소스가 올바르게 생성되었는지 확인할 때.

---

# **핵심 요약**

- Terraform `check` 블록은 **배포 과정에서 외부 리소스를 검증할 수 있도록 지원**.
- `plan` 또는 `apply` 단계에서 실행되며, **외부 API 상태, 네트워크 연결, HTTP 응답 코드 등을 검증 가능**.
- `assert`를 사용하여 조건이 충족되지 않으면 오류 메시지를 출력하여 배포 실패를 방지할 수 있음.
- Terraform의 다른 검증 도구(`precondition`, `postcondition`)와 함께 활용 가능.

---

# **핵심 필기**

## **Terraform `check` 블록 개요**

✅ Terraform의 `check` 블록은 **외부 리소스의 상태를 검증하는 기능**을 제공.  
✅ 배포 전 **리소스의 가용성, 네트워크 연결, API 응답 상태 등을 확인 가능**.

✅ **기본 문법 예제**

```hcl
check "health_check" {
  data "http" "terraform_io" {
    url = "https://www.terraform.io"
  }

  assert {
    condition = data.http.terraform_io.status_code == 200
    error_message = "${data.http.terraform_io.url} returned an unhealthy status code"
  }
}
```

✅ **설명**

- `data "http"` 블록을 사용하여 **Terraform 공식 웹사이트의 상태 코드 확인**.
- `assert` 블록에서 **HTTP 상태 코드가 200이 아니면 오류 메시지 출력**.

✅ **Terraform 실행 단계에서 검증됨**

```shell
terraform plan
terraform apply
```

---

## **Terraform `check` 블록 활용 예제**

✅ **1. 특정 API 응답이 유효한지 확인**

```hcl
check "api_check" {
  data "http" "example_api" {
    url = "https://api.example.com/health"
  }

  assert {
    condition = data.http.example_api.status_code == 200
    error_message = "API Health Check Failed"
  }
}
```

- **외부 API의 `/health` 엔드포인트가 200 응답을 반환하는지 확인**.

✅ **2. AWS RDS 데이터베이스가 올바르게 생성되었는지 확인**

```hcl
check "rds_check" {
  data "aws_db_instance" "mydb" {
    db_instance_identifier = "my-db-instance"
  }

  assert {
    condition = data.aws_db_instance.mydb.status == "available"
    error_message = "RDS instance is not available"
  }
}
```

- **Terraform이 AWS RDS 인스턴스 상태를 확인하여 "available"인지 검증**.

✅ **3. 특정 도메인의 DNS 레코드가 설정되었는지 확인**

```hcl
check "dns_check" {
  data "aws_route53_record" "example_com" {
    zone_id = "Z123456ABC"
    name    = "example.com"
    type    = "A"
  }

  assert {
    condition = data.aws_route53_record.example_com.records[0] != ""
    error_message = "DNS Record is missing for example.com"
  }
}
```

- **Route 53에서 특정 도메인의 A 레코드가 존재하는지 확인**.

✅ **4. 특정 Terraform 변수 값이 올바르게 설정되었는지 확인**

```hcl
check "variable_check" {
  assert {
    condition = length(var.environment) > 0
    error_message = "Environment variable must not be empty"
  }
}
```

- **환경 변수(`var.environment`)가 비어있으면 오류 출력**.

---

## **Terraform `check` 블록 vs. 다른 검증 도구 비교**

|검증 방법|설명|사용 사례|
|---|---|---|
|**`check` 블록**|외부 리소스 또는 API 상태 검증|HTTP 응답, DNS 레코드, DB 상태 확인|
|**`precondition`**|리소스 생성 전에 특정 조건 검증|VPC CIDR 범위 유효성 검사|
|**`postcondition`**|리소스 생성 후 특정 조건 검증|S3 버킷 생성 후 암호화 설정 확인|

---

# **Terraform `check` 블록 사용 시 주의점**

✅ **1. 외부 API 응답 시간이 길어지면 Terraform 실행 속도가 느려질 수 있음**

- API 응답 시간이 길면 Terraform `plan` 및 `apply` 과정이 느려질 수 있음.
- API 요청을 최소화하기 위해 `depends_on`을 활용하여 필요할 때만 실행하도록 설정 가능.

✅ **2. `check` 블록은 상태(state)에 저장되지 않음**

- `terraform apply` 후 `.tfstate` 파일에 기록되지 않으며, 매번 실행될 때 새로 검증 수행.

✅ **3. HTTP 요청이 실패하면 전체 Terraform 실행이 중단될 수 있음**

- API 장애로 인해 검증이 실패할 경우, Terraform 적용이 중단될 가능성이 있음.

---

# **Terraform `check` 블록 주요 비교**

|**기능**|**설명**|**사용 사례**|
|---|---|---|
|**HTTP 상태 검증**|외부 API 가용성 확인|REST API Health Check|
|**DNS 레코드 확인**|특정 도메인의 DNS 설정 확인|AWS Route 53에서 레코드 조회|
|**RDS 상태 체크**|DB 인스턴스 상태 확인|AWS RDS 가용성 체크|
|**변수 값 검증**|변수 값의 유효성 검사|환경 변수 설정 확인|

---

# **참고 자료**

- [Terraform Check Blocks 공식 문서](https://developer.hashicorp.com/terraform/language/checks)
- [Terraform Precondition & Postcondition](https://developer.hashicorp.com/terraform/language/meta-arguments/precondition)
- https://developer.hashicorp.com/terraform/language/checks
---

## 🚀 **결론**

- **Terraform `check` 블록을 사용하면 배포 과정에서 외부 리소스를 검증하여 인프라 안정성을 높일 수 있음**.
- **HTTP 상태 코드, DNS 레코드, 데이터베이스 상태 등을 확인하는 데 유용**.
- **`precondition`, `postcondition`과 함께 사용하면 인프라 배포의 신뢰성을 높일 수 있음**. 🚀