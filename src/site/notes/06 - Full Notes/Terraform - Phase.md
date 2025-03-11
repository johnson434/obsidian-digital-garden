---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-phase/","noteIcon":""}
---

# **Tags**

- [[03 - Tags/Terraform\|Terraform]]
---
![Pasted image 20250206152636.png](/img/user/image/Pasted%20image%2020250206152636.png)
# **단서 질문 및 답변**

### **Q1: Terraform 실행 단계는 어떻게 구성되는가?**

✅ **Terraform의 실행은 `init → validate → plan → apply → destroy` 순서로 진행됨**.  
✅ **각 단계는 특정 작업을 수행하며, 순차적으로 실행해야 함**.  
✅ **주요 목표는 인프라를 코드로 정의하고, 적용하고, 필요하면 제거하는 것**.

### **Q2: Terraform의 주요 명령어(`init`, `validate`, `plan`, `apply`, `destroy`)의 역할은?**

✅ `terraform init` → **프로젝트 초기화 및 필요한 플러그인 다운로드**.  
✅ `terraform validate` → **구문 오류 및 설정 유효성 검사**.  
✅ `terraform plan` → **실제 변경 없이 변경 사항 미리보기**.  
✅ `terraform apply` → **계획된 변경 사항을 실제 인프라에 적용**.  
✅ `terraform destroy` → **모든 리소스를 삭제**.

---

# **Terraform 실행 단계별 정리**

## **1. `terraform init` (초기화 단계)**

✅ Terraform을 처음 실행할 때 **프로젝트 디렉터리를 초기화**.  
✅ **필요한 Provider 플러그인 및 모듈 다운로드**.  
✅ `.terraform` 디렉터리를 생성하여 Terraform 실행 환경 구성.  
✅ `terraform init -upgrade` 옵션 사용 시 **기존 Provider 버전 업데이트**.

```shell
$ terraform init
```

### **🛠️ 실행 예시**

```plaintext
Initializing provider plugins...
- Finding latest version of hashicorp/aws...
- Installing hashicorp/aws v5.84.0...
- Installed hashicorp/aws v5.84.0 (signed by HashiCorp)
```

### **🔥 `terraform init`의 주요 옵션**

|옵션|설명|
|---|---|
|`-upgrade`|Provider 플러그인을 최신 버전으로 업데이트|
|`-reconfigure`|기존 `.terraform` 디렉터리 삭제 후 재설정|
|`-backend=false`|원격 백엔드 설정 없이 로컬로 실행|

---

## **2. `terraform validate` (유효성 검사 단계)**

✅ Terraform 설정 파일(`.tf`)에 **문법 오류 및 논리 오류가 있는지 검사**.  
✅ **실제 인프라 변경 없이 구성 파일이 유효한지 확인**.  
✅ **구성 오류가 있으면 적용 전에 수정 가능**.

```shell
$ terraform validate
```

### **🛠️ 실행 예시**

```plaintext
Success! The configuration is valid.
```

### **🔥 `terraform validate`에서 발생할 수 있는 오류 예시**

✅ **변수 타입 오류**

```hcl
variable "instance_count" {
  type = number
}

resource "aws_instance" "example" {
  count = var.instance_count
}
```

❌ `Error: Invalid value for input variable "instance_count": Expected a number.`  
**🔹 해결 방법:** `"1"` → `1`로 변경하여 `number` 타입 맞추기.

---

## **3. `terraform plan` (계획 단계)**

✅ **실제 인프라 변경 없이 변경 사항을 미리 확인**.  
✅ **현재 상태(`.tfstate`)와 새로 정의한 코드(`.tf`)를 비교하여 변경 사항을 보여줌**.  
✅ **적용될 리소스 추가(+), 수정(~), 삭제(-) 내용을 표시**.

```shell
$ terraform plan
```

### **🛠️ 실행 예시**

```plaintext
Terraform will perform the following actions:

  # aws_instance.example will be created
  + resource "aws_instance" "example" {
      + ami           = "ami-123456"
      + instance_type = "t2.micro"
    }
```

### **🔥 `terraform plan`의 주요 옵션**

|옵션|설명|
|---|---|
|`-out=tfplan`|실행 계획을 파일로 저장하여 나중에 `apply`할 때 사용|
|`-var="key=value"`|특정 변수 값을 지정하여 실행|
|`-destroy`|리소스를 삭제하는 계획을 생성|

---

## **4. `terraform apply` (적용 단계)**

✅ `terraform plan`에서 확인한 변경 사항을 실제 인프라에 적용.  
✅ **사용자 확인 후(`yes` 입력) 변경 사항 실행**.  
✅ **자동 실행하려면 `-auto-approve` 옵션 사용**.

```shell
$ terraform apply
```

### **🛠️ 실행 예시**

```plaintext
Terraform will perform the following actions:

  # aws_instance.example will be created
  + resource "aws_instance" "example" {
      + ami           = "ami-123456"
      + instance_type = "t2.micro"
    }

Do you want to perform these actions?
  Terraform will apply the changes above.
  Enter a value: yes
```

### **🔥 `terraform apply`의 주요 옵션**

|옵션|설명|
|---|---|
|`-auto-approve`|사용자 확인 없이 즉시 적용|
|`-var="key=value"`|특정 변수 값을 지정하여 실행|
|`-parallelism=N`|동시에 실행할 최대 리소스 개수 설정|

---

## **5. `terraform destroy` (삭제 단계)**

✅ **모든 리소스를 삭제하여 클린업 수행**.  
✅ `.tfstate`에서 관리하는 모든 인프라 리소스를 제거.  
✅ **확인 과정이 있으므로 실수로 삭제하는 것을 방지 가능**.

```shell
$ terraform destroy
```

### **🛠️ 실행 예시**

```plaintext
Terraform will destroy all your managed infrastructure.
  - aws_instance.example

Do you really want to destroy all resources?
  Enter a value: yes
```

### **🔥 `terraform destroy`의 주요 옵션**

|옵션|설명|
|---|---|
|`-auto-approve`|사용자 확인 없이 즉시 삭제|
|`-target=resource`|특정 리소스만 삭제 (`terraform destroy -target=aws_instance.example`)|
|`-refresh=false`|기존 상태를 새로고침하지 않고 삭제|

---

# **Terraform 실행 단계별 요약**

|**단계**|**명령어**|**설명**|**주요 옵션**|
|---|---|---|---|
|**초기화**|`terraform init`|프로젝트 디렉터리 초기화 및 Provider 플러그인 다운로드|`-upgrade`, `-reconfigure`|
|**유효성 검사**|`terraform validate`|구문 및 논리 오류 확인|없음|
|**계획**|`terraform plan`|변경 사항 미리보기 (리소스 추가/수정/삭제)|`-out=tfplan`, `-destroy`|
|**적용**|`terraform apply`|실제 인프라 변경 적용|`-auto-approve`, `-parallelism=N`|
|**삭제**|`terraform destroy`|모든 리소스 삭제|`-auto-approve`, `-target=resource`|

---

# **참고 자료**

- [Terraform 공식 문서 - init](https://developer.hashicorp.com/terraform/cli/commands/init)
- [Terraform 공식 문서 - validate](https://developer.hashicorp.com/terraform/cli/commands/validate)
- [Terraform 공식 문서 - plan](https://developer.hashicorp.com/terraform/cli/commands/plan)
- [Terraform 공식 문서 - apply](https://developer.hashicorp.com/terraform/cli/commands/apply)
- [Terraform 공식 문서 - destroy](https://developer.hashicorp.com/terraform/cli/commands/destroy)

---

## 🚀 **결론**

- Terraform 실행은 **`init → validate → plan → apply → destroy`** 순으로 진행.
- **각 단계에서 코드 검증, 실행 계획, 변경 적용, 삭제가 이루어짐**.
- **옵션을 활용하여 자동화 및 세부 설정 가능**.
- **잘못된 실행을 방지하려면 항상 `terraform plan`을 먼저 확인하고 실행할 것! 🚀**