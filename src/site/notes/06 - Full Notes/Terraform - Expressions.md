---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-expressions/","noteIcon":""}
---

# **Tags**
- [[03 - Tags/Terraform\|Terraform]]

---

# **단서 질문 및 답변**

### **Q1: Terraform에서 지원하는 기본 자료형은?**

✅ **기본 스칼라 타입**

- `string` (문자열)
- `number` (숫자)
- `bool` (참/거짓)

✅ **복합 자료형 (Collections)**

- `list(T)` (리스트)
- `set(T)` (중복 불가능한 리스트)
- `map(T)` (Key-Value 구조)

✅ **구조적 자료형 (Structural Types)**

- `object({})` (객체)
- `tuple([])` (다양한 타입을 포함하는 리스트)

---

### **Q2: Terraform `map`을 선언하는 방식은?**

✅ **Terraform의 `map`은 Key-Value 구조를 가짐**.  
✅ **두 가지 방식으로 선언 가능**:

```hcl
map = {
  "abc": "abc"
}

map = {
  abc  = "abc",
  "ddd": "ddd"  # 혼용 가능
}
```

✅ **Key에 `""`(따옴표)를 반드시 써야 하는 경우**:  
1️⃣ **숫자로 시작하는 경우**

```hcl
map = {
  "123abc" = "value"
}
```

2️⃣ **공백이 포함된 경우**

```hcl
map = {
  "abc def" = "value"
}
```

3️⃣ **특수문자가 포함된 경우**

```hcl
map = {
  "@key!" = "value"
}
```

✅ **한 줄 맵에서도 `,`(쉼표)를 사용해야 함**

```hcl
map = { "key1": "value1", "key2": "value2" }
```

---

### **Q3: Terraform `any` 타입이란?**

✅ **모든 타입을 허용하는 자료형 (지양할 것)**  
✅ **Java의 Generics처럼 사용됨**  
✅ **리스트나 맵 등의 자료형이 결정되면 내부적으로 특정 타입으로 변환됨**

✅ **예제**

```hcl
variable "example" {
  type = list(any)
}

output "example_type" {
  value = var.example
}
```

✅ **입력 값이 `["11", "22"]`일 경우 `list(string)`으로 변환됨**

---

### **Q4: Terraform `object` 타입이란?**

✅ **Key-Value 구조지만, `map`과 달리 여러 타입을 Key-Value로 가질 수 있음**  
✅ **각 속성별 타입을 명시적으로 정의 가능**  
✅ **입력값을 선택적으로 받을 수 있도록 `optional` 속성 지원**

✅ **예제: 기본 `object` 타입**

```hcl
variable "student" {
  type = object({
    id  = string
    age = number
  })
}

output "student" {
  value = var.student
}
```

✅ **예제: `optional` 속성 사용**

```hcl
variable "student" {
  type = object({
    id  = string
    age = optional(number, 20)
  })
}

output "student" {
  value = var.student
}
```

✅ **`age`를 입력하지 않으면 기본값 `20`이 설정됨**

✅ **예제: `optional`과 `default`의 동작 순서**

```hcl
variable "student" {
  type = object({
    id  = string
    age = optional(string, "optional_default_value") 
  })
  description = "test"
  default = {
    id  = "test"
    age = "default_block default value"
  } 
}
```

✅ **결과**

- `optional`의 기본값(`optional_default_value`)이 먼저 적용됨.
- 이후 `default` 블록의 값(`default_block default value`)이 최종 적용됨.

---

# **핵심 요약**

- Terraform은 `string`, `number`, `bool`, `list`, `set`, `map`, `object`, `tuple` 타입을 지원.
- **`map`의 Key는 따옴표 없이 선언 가능하지만, 숫자로 시작하거나 공백이 포함된 경우 따옴표 필요**.
- **`any`는 모든 타입을 허용하지만, 변환 과정이 있으므로 지양하는 것이 좋음**.
- **`object`는 Key별로 타입을 명확하게 정의할 수 있으며, `optional` 속성을 사용하여 선택적 입력 가능**.

---

# **핵심 필기**

## **1. Terraform 기본 자료형**

✅ **스칼라(Scalar) 타입**

- `string`: `"hello"`
- `number`: `123`
- `bool`: `true` / `false`

✅ **복합(Collection) 타입**

- `list(T)`: `["a", "b", "c"]`
- `set(T)`: `["a", "b", "c"]` (중복 불가)
- `map(T)`: `{ "key1": "value1", "key2": "value2" }`

✅ **구조적(Structural) 타입**

- `object({})`: `{ key1 = "string", key2 = 123 }`
- `tuple([])`: `[ "string", 123, true ]`

---

## **2. Terraform `map` 사용법**

✅ **문법 (두 가지 방식 가능)**

```hcl
map = {
  "key1": "value1"
}

map = {
  key1  = "value1",
  "key2" = "value2"  # 혼용 가능
}
```

✅ **Key에 따옴표(`""`)를 써야 하는 경우**

```hcl
map = {
  "123abc" = "value"  # 숫자로 시작하는 경우
  "abc def" = "value" # 공백이 포함된 경우
  "@key!"   = "value" # 특수문자가 포함된 경우
}
```

✅ **한 줄 맵 사용 시 `,`(쉼표) 필요**

```hcl
map = { "key1": "value1", "key2": "value2" }
```

---

## **3. Terraform `any` 타입**

✅ **모든 타입을 허용하지만, 사용을 지양하는 것이 좋음**

```hcl
variable "example" {
  type = list(any)
}
```

✅ **입력값이 `["11", "22"]`일 경우 `list(string)`으로 변환됨**

---

## **4. Terraform `object` 타입**

✅ **Key별로 다른 타입을 가질 수 있음**

```hcl
variable "student" {
  type = object({
    id  = string
    age = number
  })
}
```

✅ **선택적 입력을 위한 `optional` 속성 사용 가능**

```hcl
variable "student" {
  type = object({
    id  = string
    age = optional(number, 20)
  })
}
```

✅ **입력값이 없으면 `age = 20`이 자동 설정됨**

✅ **`optional`과 `default`의 동작 순서**

```hcl
variable "student" {
  type = object({
    id  = string
    age = optional(string, "optional_default_value") 
  })
  default = {
    id  = "test"
    age = "default_block default value"
  } 
}
```

✅ **결과**

1. `optional`의 기본값(`optional_default_value`)이 먼저 적용됨.
2. 이후 `default` 블록의 값(`default_block default value`)이 최종 적용됨.

---

# **Terraform 자료형 비교**

|**자료형**|**설명**|**예제**|
|---|---|---|
|`string`|문자열|`"hello"`|
|`number`|숫자|`123`|
|`bool`|논리값|`true` / `false`|
|`list(T)`|리스트|`["a", "b", "c"]`|
|`set(T)`|중복 불가 리스트|`["a", "b", "c"]`|
|`map(T)`|Key-Value|`{ "key1": "value1", "key2": "value2" }`|
|`object({})`|여러 타입 포함|`{ key1 = "string", key2 = 123 }`|
|`tuple([])`|다른 타입 포함|`[ "string", 123, true ]`|

---

# **참고 자료**

- [Terraform 공식 문서 - Data Types](https://developer.hashicorp.com/terraform/language/expressions/types)

---

## 🚀 **결론**

- **Terraform은 다양한 자료형을 지원하며, `map`, `object` 등을 활용하면 구조적인 데이터 관리가 가능**.
- **`optional` 속성을 사용하면 입력값을 선택적으로 받을 수 있어 유연성이 증가**.
- **보안이나 코드 가독성을 위해 `any` 타입 사용을 지양하는 것이 좋음! 🚀**