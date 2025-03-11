---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-lock-file/","noteIcon":""}
---

# **Tags**
- [[03 - Tags/Terraform\|Terraform]]

---

# **단서 질문 및 답변**

### **Q1: Terraform `lock file`이란?**

✅ Terraform에서 `.terraform.lock.hcl` 파일을 사용하여 **프로바이더 및 모듈의 버전을 고정하는 역할**.  
✅ `terraform init` 실행 시 자동 생성되며, 특정 모듈이 아니라 **Terraform 프로젝트 전체의 의존성 버전을 관리**.  
✅ Git에 커밋하여 팀원 간의 일관된 환경을 유지하는 것이 중요함.

---

### **Q2: Terraform `lock file`이 생성되는 위치는?**

✅ `.terraform/` 디렉터리 내부에 위치하는 `.terraform.lock.hcl` 파일.  
✅ 특정 모듈이 아니라 **Terraform 프로젝트 전체에 대한 버전 관리를 수행**.

```bash
john@john:~/Projects/Playground/IaC$ cat .terraform.lock.hcl 
# This file is maintained automatically by "terraform init".
# Manual edits may be lost in future updates.

provider "registry.terraform.io/hashicorp/aws" {
  version     = "5.84.0"
  constraints = "~> 5.75"
  hashes = [
    "h1:xxxxxx",
    "h1:yyyyyy",
    ...
  ]
}
```

---

### **Q3: Terraform `lock file`의 역할은?**

✅ Terraform에서 사용된 **프로바이더 및 모듈의 정확한 버전이 기록**됨.  
✅ `terraform init` 실행 시 `.terraform.lock.hcl`을 참조하여 **일관된 버전**을 유지.  
✅ 개발자 간 환경 차이를 방지하여, 팀 내에서 **동일한 Terraform 실행 결과를 보장**.  
✅ 보안 및 안정성을 위해 최신 프로바이더 자동 업데이트를 방지.

---

### **Q4: Terraform에서 `terraform init` 시 lock 파일은 어떻게 작동하는가?**

✅ Terraform은 `.terraform.lock.hcl` 파일이 존재하면 **해당 파일에 기록된 버전의 프로바이더를 사용**.  
✅ **만약 특정 프로바이더가 `.terraform.lock.hcl`에 기록되어 있지 않으면, Terraform은 최신 버전을 다운로드**.  
✅ **`-upgrade` 플래그를 사용하면 최신 버전으로 업데이트 가능**.

```bash
# 기존 `.terraform.lock.hcl`을 유지하면서 프로바이더 설치
terraform init

# 기존 `.terraform.lock.hcl`을 무시하고 최신 버전으로 업데이트
terraform init -upgrade
```

✅ Terraform은 `.terraform.lock.hcl`에 기록된 버전이 존재하는 한 **해당 버전을 강제로 사용**.  
✅ `-upgrade` 옵션을 사용하지 않으면 `.terraform.lock.hcl`의 버전이 유지됨.

---

### **Q5: `.terraform` 디렉터리는 무엇인가?**

✅ Terraform 프로젝트에서 사용되는 **프로바이더 및 모듈을 저장하는 로컬 캐시 디렉터리**.  
✅ `.terraform/` 디렉터리 내부에는 다음과 같은 항목이 포함될 수 있음.

```
.
├── .terraform
│   ├── modules/  # 외부 모듈 저장
│   ├── providers/  # 설치된 프로바이더 저장
│   ├── terraform.tfstate  # 상태 파일 (로컬 실행 시)
│   ├── terraform.tfstate.backup  # 백업 상태 파일
│   └── .terraform.lock.hcl  # Lock 파일
```

✅ `.terraform/` 디렉터리는 **Terraform이 실행되는 환경에서 필요한 종속성을 저장하는 공간**.  
✅ `.terraform.lock.hcl` 파일과 함께 사용되어 **모든 실행 환경에서 동일한 프로바이더 및 모듈을 사용하도록 보장**.

---
# **핵심 요약**
- **`.terraform.lock.hcl`은 Terraform의 버전 관리용 Lock 파일로, 실행 환경의 일관성을 유지하는 역할**.
- **`terraform init` 실행 시 자동 생성되며, 해당 프로젝트에서 사용되는 프로바이더 및 모듈 버전을 기록**.
- **팀 내에서 동일한 Terraform 실행 결과를 보장하기 위해 Git에 포함하여 공유하는 것이 권장됨**.
- **최신 프로바이더 버전으로 업데이트하려면 `terraform init -upgrade`를 사용해야 함**.

---

# **핵심 정리**

## **1. Terraform `lock file` 개요**

✅ `.terraform.lock.hcl` 파일은 Terraform에서 **프로바이더 및 모듈 버전을 고정하는 파일**.  
✅ Terraform 프로젝트 내에서 일관된 환경을 보장하며, 예기치 않은 버전 업데이트를 방지함.  
✅ Git에 포함하여 팀원 간 버전 불일치를 방지하는 것이 중요.

---

## **2. Terraform `lock file` 생성 및 사용 방법**

✅ **Terraform `lock file` 생성 (자동 생성됨)**

```bash
terraform init
```

✅ **Terraform `lock file` 확인**

```bash
cat .terraform.lock.hcl
```

✅ **최신 프로바이더 버전으로 업데이트 (`-upgrade` 옵션 사용)**

```bash
terraform init -upgrade
```

✅ **기존 `lock file`을 삭제하고 다시 생성 (`-reconfigure` 옵션 사용)**

```bash
terraform init -reconfigure
```

---

## **3. `.terraform.lock.hcl`의 주요 필드 설명**

|**필드**|**설명**|
|---|---|
|`provider`|사용된 Terraform 프로바이더|
|`version`|사용된 프로바이더의 정확한 버전|
|`constraints`|사용 가능한 버전의 제약 조건|
|`hashes`|프로바이더의 무결성을 보장하는 해시값|

✅ **예제 (`.terraform.lock.hcl` 내용)**

```hcl
# Terraform에서 자동 생성된 lock 파일

provider "registry.terraform.io/hashicorp/aws" {
  version     = "5.84.0"
  constraints = "~> 5.75"
  hashes = [
    "h1:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "h1:yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy"
  ]
}
```

---

## **4. Terraform `lock file` 관련 주요 명령어**

|**명령어**|**설명**|
|---|---|
|`terraform init`|`.terraform.lock.hcl`을 생성하고 기존 버전 유지|
|`terraform init -upgrade`|`.terraform.lock.hcl`을 무시하고 최신 버전으로 업데이트|
|`terraform init -reconfigure`|기존 `lock file`을 삭제하고 다시 생성|
|`terraform providers lock`|`.terraform.lock.hcl`을 강제로 생성|

✅ **특정 프로바이더만 업데이트하는 예제**

```bash
terraform providers lock -platform=linux_amd64
```

---

## **5. `.terraform.lock.hcl` 파일을 Git에서 관리해야 하는 이유**

✅ `.terraform.lock.hcl` 파일을 Git에 커밋하면 **팀원 간 동일한 환경을 유지 가능**.  
✅ 프로바이더 버전이 예기치 않게 변경되는 것을 방지하여 **Terraform 실행 결과의 일관성을 보장**.

✅ **Git에 커밋하는 방법**

```bash
git add .terraform.lock.hcl
git commit -m "Lock Terraform provider versions"
git push origin main
```

✅ **`.gitignore`에 `.terraform.lock.hcl`을 추가하면 안 되는 이유**

- `.terraform/` 디렉터리는 `.gitignore`에 추가하는 것이 일반적이지만, **`.terraform.lock.hcl`은 반드시 관리해야 함**.
- Git에서 관리하지 않으면, 팀원마다 다른 버전의 프로바이더를 사용할 수 있어 예기치 않은 동작이 발생할 수 있음.

```bash
# 올바른 .gitignore 설정 예제
.terraform/  
!terraform.lock.hcl  # Lock 파일은 예외적으로 Git에 추가
```

---

# **Terraform `lock file` 주요 비교**

|**옵션**|**설명**|**사용 예시**|
|---|---|---|
|**`terraform init`**|`.terraform.lock.hcl` 생성 및 기존 버전 유지|`terraform init`|
|**`terraform init -upgrade`**|`.terraform.lock.hcl`을 무시하고 최신 버전 사용|`terraform init -upgrade`|
|**`terraform init -reconfigure`**|기존 lock 파일 삭제 후 다시 생성|`terraform init -reconfigure`|
|**`terraform providers lock`**|특정 플랫폼용 lock 파일 생성|`terraform providers lock -platform=linux_amd64`|

---

# **참고 자료**
- [Terraform 공식 문서 - Lock File](https://developer.hashicorp.com/terraform/language/files/dependency-lock)