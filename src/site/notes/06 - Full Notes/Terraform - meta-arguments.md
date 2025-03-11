---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-meta-arguments/","noteIcon":""}
---

# **Tags**

- [[03 - Tags/Terraform\|Terraform]]

---

# **단서 질문 및 답변**

### **Q1: `depends_on`이란? 언제 사용해야 하나?**

✅ **명시적으로 특정 `resource`나 `module`이 생성된 후에 리소스를 생성하도록 지정**.  
✅ **Terraform은 리소스 간 종속성을 자동으로 관리하지만, 강제 순서 지정이 필요할 때 사용**.  
✅ **최후의 수단으로 사용해야 하며, 가능한 Terraform의 기본 종속성 분석 기능을 활용하는 것이 좋음**.

```hcl
resource "aws_instance" "test" {
  depends_on = [
    aws_instance.db,  
    aws_instance.web
  ]
}
```

---

### **Q2: `count`와 `for_each`의 차이점은?**

✅ **`count`는 단순히 리소스 개수를 반복 생성**.  
✅ **`for_each`는 `map` 또는 `set(string)`을 기반으로 리소스를 개별 설정과 함께 생성**.  
✅ **주요 차이점**

|**기능**|**count**|**for_each**|
|---|---|---|
|**반복 기준**|정수 값 (`number`)|`map` 또는 `set(string)`|
|**리소스 식별 방식**|인덱스 (`[0]`, `[1]` 등)|키 (`each.key`)|
|**개별 설정**|동일한 설정|각 리소스마다 다른 설정 가능|
|**순서 변경 시 영향**|큰 영향 (인덱스 재정렬)|영향 없음 (키 기반)|

✅ **사용 가이드**

- **단순 반복이면 `count` 사용**
- **리소스별 개별 설정이 필요하면 `for_each` 사용**
- **Terraform 0.12+에서는 `for_each`이 `count`보다 선호됨**

---

# **핵심 필기**

## **1. Terraform `depends_on` (리소스 생성 순서 지정)**

✅ Terraform은 리소스 간의 종속성을 자동으로 분석하지만, 특정 상황에서는 `depends_on`을 사용하여 **강제적인 리소스 생성 순서 지정 가능**.  
✅ **배열 형태로 여러 리소스를 지정할 수 있음**.

```hcl
resource "aws_instance" "test" {
  depends_on = [
    aws_instance.db,
    aws_instance.web
  ]
}
```

✅ **주의사항**

- **Terraform은 리소스 간 종속성을 자동으로 분석하므로 `depends_on`은 가급적 사용을 피해야 함**.
- **`depends_on`을 사용하면 Terraform이 보수적으로 동작하여 병렬 실행이 줄어들어 배포 속도가 저하될 수 있음**.
- **모듈에도 적용 가능하지만, 보통 `output`을 활용한 의존성 관리가 더 적절함**.

```hcl
module "vpc" {
  source = "./vpc"
}

module "eks" {
  source     = "./eks"
  depends_on = [module.vpc]
}
```

---

## **2. Terraform `count` (리소스 개수 기반 반복 생성)**

✅ **리소스를 `count` 값만큼 반복 생성**.  
✅ **정적 값(숫자, `length()` 등)을 기반으로 반복**.  
✅ **리소스를 `[index]` 방식으로 참조해야 함**.

```hcl
variable "subnet_ids" {
  type = list(string)
}

resource "aws_instance" "server" {
  count = length(var.subnet_ids)  # 서브넷 개수만큼 EC2 생성

  ami           = "ami-a1b2c3d4"
  instance_type = "t2.micro"
  subnet_id     = var.subnet_ids[count.index]  # 개별 서브넷 ID 적용

  tags = {
    Name = "Server ${count.index}"
  }
}
```

✅ **주의사항**

- **Terraform 적용 시 인덱스를 기반으로 리소스를 관리하므로, 중간 값이 변경되면 모든 리소스가 재생성될 위험이 있음**.
- **`count.index`를 활용하여 개별 리소스 접근 가능하지만, 리소스 변경이 빈번하면 `for_each`가 더 적절함**.

---

## **3. Terraform `for_each` (맵 또는 세트 기반 반복 생성)**

✅ `for_each`는 `map` 또는 `set(string)`을 사용하여 반복 생성.  
✅ `each.key`와 `each.value`를 사용하여 개별 설정 가능.

```hcl
resource "aws_instance" "test" {
  for_each = toset(["abc", "efg"])  

  ami           = "ami-0a2c043e56e9abcc5"  
  instance_type = "t2.micro"  

  tags = {
    Name = "test-${each.value}"  
  }
}
```

✅ **Map을 활용한 `for_each` 예제**

```hcl
locals {
  ec2_instances = {
    "web1" = { ami = "ami-123456", instance_type = "t2.micro" }
    "web2" = { ami = "ami-789012", instance_type = "t3.micro" }
  }
}

resource "aws_instance" "example" {
  for_each = local.ec2_instances

  ami           = each.value.ami
  instance_type = each.value.instance_type

  tags = {
    Name = each.key
  }
}
```

✅ **주의사항**

- **리소스가 `key` 값으로 식별되므로 `count`보다 안정적**.
- **`map`의 `key` 또는 `set(string)` 값이 변경되지 않는 한, 불필요한 리소스 재생성이 발생하지 않음**.

---

## **4. `count` vs `for_each` 비교**

|**기능**|**count**|**for_each**|
|---|---|---|
|**반복 기준**|정수 값 (`number`)|`map` 또는 `set(string)`|
|**리소스 식별 방식**|인덱스 (`[0]`, `[1]` 등)|키 (`each.key`)|
|**개별 설정**|동일한 설정|각 리소스마다 다른 설정 가능|
|**순서 변경 시 영향**|큰 영향 (인덱스 재정렬)|영향 없음 (키 기반)|
|**참조 방식**|`resource.name[count.index]`|`resource.name["key"]`|
|**데이터 소스 활용**|제한적|유연함|

✅ **사용 가이드**

- **단순 반복이면 `count` 사용**.
- **리소스별 개별 설정이 필요하면 `for_each` 사용**.
- **Terraform 0.12+에서는 `for_each`가 `count`보다 선호됨**.

---

# **Terraform `depends_on`, `count`, `for_each` 요약 정리**

|**옵션**|**설명**|**사용 예시**|**주의사항**|
|---|---|---|---|
|**`depends_on`**|특정 리소스가 생성된 후 실행|`depends_on = [aws_instance.db]`|보수적으로 동작하여 배포 속도 저하 가능|
|**`count`**|정수 기반 반복 생성|`count = length(var.subnet_ids)`|중간 값 변경 시 리소스 전체 재생성 가능|
|**`for_each`**|`map` 또는 `set(string)` 기반 반복 생성|`for_each = toset(["web1", "web2"])`|`map`의 `key`가 변경되지 않으면 안정적|

---
# **참고 자료**

- [Terraform 공식 문서 - `depends_on`](https://developer.hashicorp.com/terraform/language/meta-arguments/depends_on)
- [Terraform 공식 문서 - `count`](https://developer.hashicorp.com/terraform/language/meta-arguments/count)
- [Terraform 공식 문서 - `for_each`](https://developer.hashicorp.com/terraform/language/meta-arguments/for_each)
