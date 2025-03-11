---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-functions/","noteIcon":""}
---

# **Tags**
- [[03 - Tags/Terraform\|Terraform]]
---

# **단서 질문 및 답변**

### **Q1: Terraform 네트워크 관련 함수는 어떤 것이 있는가?**

✅ **CIDR 관련 함수**

- `cidrhost`: 특정 IP 대역에서 n번째 호스트 IP 반환
- `cidrsubnet`: 기존 CIDR 블록을 서브넷으로 분할
- `cidrsubnets`: 여러 서브넷을 한 번에 생성

---

### **Q2: `cidrhost` 함수란?**

✅ **IP 대역에서 특정 호스트의 IP 주소를 반환**  
✅ **형식**

```hcl
cidrhost("CIDR 블록", N번째 호스트)
```

✅ **예제**

```hcl
cidrhost("10.0.0.0/22", 1)   # 10.0.0.1
cidrhost("10.0.0.0/22", 256) # 10.0.1.0
```

✅ **첫 번째 IP (`0`)는 네트워크 주소이므로 사용 불가**

---

### **Q3: `cidrsubnet` 함수란?**

✅ **기존 네트워크 블록을 서브넷으로 나누는 함수**  
✅ **형식**

```hcl
cidrsubnet("CIDR 블록", 추가 비트, N번째 서브넷)
```

✅ **예제**

```hcl
cidrsubnet("10.0.0.0/16", 8, 0) # 10.0.0.0/24
cidrsubnet("10.0.0.0/16", 8, 1) # 10.0.1.0/24
```

✅ **추가 비트에 따라 더 작은 서브넷을 생성**

---

### **Q4: `cidrsubnets` 함수란?**

✅ **여러 개의 서브넷을 한 번에 생성**  
✅ **형식**

```hcl
cidrsubnets("CIDR 블록", 추가 비트1, 추가 비트2, ...)
```

✅ **예제**

```hcl
cidrsubnets("10.0.0.0/16", 8, 8, 4)
```

✅ **결과**

```
10.0.0.0/24, 10.0.1.0/24, 10.0.16.0/20
```

✅ **3번째 서브넷(`10.0.16.0/20`)이 되는 이유**

- `10.0.0.0/20` → `10.0.0.0 ~ 10.0.15.255` 대역이 선점
- 다음 사용 가능한 블록이 `10.0.16.0/20`

---

# **핵심 요약**

- **`cidrhost`**: 특정 IP 대역에서 n번째 호스트 IP 반환
- **`cidrsubnet`**: 기존 CIDR 블록을 작은 서브넷으로 분할
- **`cidrsubnets`**: 여러 개의 서브넷을 한 번에 생성

---

# **핵심 필기**

## **1. Terraform 네트워크 함수**

### **✅ `cidrhost` (특정 호스트 IP 조회)**

```hcl
cidrhost("10.0.0.0/22", 1)   # 10.0.0.1
cidrhost("10.0.0.0/22", 256) # 10.0.1.0
```

---

### **✅ `cidrsubnet` (서브넷 분할)**

```hcl
cidrsubnet("10.0.0.0/16", 8, 0) # 10.0.0.0/24
cidrsubnet("10.0.0.0/16", 8, 1) # 10.0.1.0/24
```

---

### **✅ `cidrsubnets` (여러 개 서브넷 생성)**

```hcl
cidrsubnets("10.0.0.0/16", 8, 8, 4)
```

✅ **결과**

```
10.0.0.0/24, 10.0.1.0/24, 10.0.16.0/20
```

---

## **2. Terraform 문자열 관련 함수**

✅ **`trimspace` (공백 제거)**

```hcl
trimspace("  hello  ") # "hello"
```

✅ **`startswith` / `endswith` (문자열 시작/끝 체크)**

```hcl
startswith("terraform", "terra") # true
endswith("terraform", "form")    # true
```

✅ **`lookup` (맵에서 키 찾기, 기본값 지원)**

```hcl
variable "config" {
  type = map(string)
  default = {
    env  = "prod"
    user = "admin"
  }
}

output "env" {
  value = lookup(var.config, "env", "default")
} 
# 결과: "prod"
```

✅ **`try` (예외 발생 시 기본값 반환)**

```hcl
try(lookup(var.config, "missing_key"), "default_value")
```

---

## **3. Terraform 집합(Set) 관련 함수**

✅ **합집합 (`merge` & `setunion`)**

```hcl
merge({a = 1, b = 2}, {b = 3, c = 4}) # {a = 1, b = 3, c = 4}
setunion(["a", "b"], ["b", "c"])      # ["a", "b", "c"]
```

✅ **교집합 (`setintersection`)**

```hcl
setintersection(["a", "b"], ["b", "c"]) # ["b"]
```

✅ **차집합 (`setsubtract`)**

```hcl
setsubtract(["a", "b"], ["b", "c"]) # ["a"]
```

---

## **4. Terraform 암호화 및 해싱 함수**

✅ **`base64encode` / `base64decode`**

```hcl
base64encode("hello") # "aGVsbG8="
base64decode("aGVsbG8=") # "hello"
```

✅ **`sha256` / `sha512` (해싱)**

```hcl
sha256("password") # "5e884898da280471..."
sha512("password") # "b109f3bbbc244eb8..."
```

---

## **5. Terraform 시간 관련 함수**

✅ **현재 시간 조회 (`timestamp`)**

```hcl
timestamp() # "2025-02-11T10:00:00Z"
```

✅ **ISO 8601 형식의 날짜 파싱 (`timeadd`)**

```hcl
timeadd(timestamp(), "24h") # 24시간 후의 시간
```

---

# **Terraform 주요 함수 비교**

|**함수**|**설명**|**예제**|
|---|---|---|
|**`cidrhost`**|특정 CIDR 블록에서 n번째 IP 조회|`cidrhost("10.0.0.0/22", 1) # 10.0.0.1`|
|**`cidrsubnet`**|CIDR 블록을 서브넷으로 분할|`cidrsubnet("10.0.0.0/16", 8, 0) # 10.0.0.0/24`|
|**`cidrsubnets`**|여러 개의 서브넷 생성|`cidrsubnets("10.0.0.0/16", 8, 8, 4)`|
|**`trimspace`**|문자열 공백 제거|`trimspace(" hello ") # "hello"`|
|**`startswith`**|문자열 시작 여부 확인|`startswith("terraform", "terra") # true`|
|**`endswith`**|문자열 끝 여부 확인|`endswith("terraform", "form") # true`|
|**`lookup`**|맵에서 키 조회 (기본값 지원)|`lookup(var.config, "env", "default")`|
|**`base64encode`**|Base64 인코딩|`base64encode("hello") # "aGVsbG8="`|
|**`sha256`**|SHA256 해싱|`sha256("password")`|

---
# **참고 자료**
- [Terraform 공식 문서 - Functions](https://developer.hashicorp.com/terraform/language/functions)