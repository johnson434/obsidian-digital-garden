---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-backend/","noteIcon":""}
---

# **Tags**
- [[03 - Tags/Terraform\|Terraform]]

---

# **단서 질문**
### **1. Terraform의 `backend` 블록이 하는 역할은 무엇인가?**

✅ `backend` 블록은 **Terraform 상태 파일(`.tfstate`)의 저장 위치를 지정**하는 설정 블록입니다.  
✅ 기본적으로 로컬(`local`)에 저장되지만, **AWS S3, GCS, Azure Storage 등 원격 백엔드를 사용할 수도 있습니다**.  
✅ 원격 백엔드를 사용하면 여러 사용자가 동일한 상태 파일을 공유하여 협업할 수 있습니다.

---

### **2. Terraform 상태 파일(`.tfstate`)이 중요한 이유는?**

✅ Terraform은 `.tfstate` 파일을 사용하여 **현재 인프라의 상태를 추적**하고, 코드와의 차이를 비교하여 변경 사항을 적용합니다.  
✅ `.tfstate` 파일이 손실되거나 변경되면 **Terraform이 리소스를 잘못 판단하여 삭제하거나 중복 생성할 위험이 있습니다**.  
✅ 따라서, 상태 파일을 안전하게 저장하고 버전 관리를 하는 것이 중요합니다.

---

### **3. S3를 Terraform backend로 사용할 때의 장점과 주의할 점은?**

✅ **장점:**

- **여러 사용자가 동일한 `.tfstate` 파일을 공유할 수 있음** → 협업 가능.
- **S3 버전 관리(Versioning) 및 암호화 지원** → 보안 강화 가능.
- **백업 및 복구가 용이함**.

✅ **주의할 점:**

- S3 단독으로는 **상태 잠금(State Locking)이 불가능** → **DynamoDB와 함께 사용해야 Lock 기능을 지원**.
- `.tfstate` 파일에 민감한 정보(예: 비밀번호, 접근 키)가 포함될 수 있으므로 **IAM 정책으로 접근 제어 필요**.

---

# **핵심 요약**

- **Terraform `backend` 블록**은 **Terraform 상태 파일(`.tfstate`)의 저장 위치를 지정**하는 설정.
- 기본값은 `local`, 그러나 **여러 사용자가 공동 작업할 경우 원격 백엔드(S3, Azure, GCS 등)를 사용해야 함**.
- **S3를 `backend`로 사용할 경우**: 중앙 집중식 상태 관리 가능하지만, **DynamoDB를 함께 사용해야 Lock 기능 제공 가능**.
- **`.terraform` 디렉터리 내부에 백엔드 설정이 저장되며, 인증 방식 등이 포함됨**.
- **Terraform backend 종류**: S3, GCS, Azure Storage, PostgreSQL, Consul, etcd 등 다양한 옵션 존재.

---

# **핵심 필기**

## **Terraform `backend` 블록 개요**

✅ **Terraform의 상태 파일(`.tfstate`) 역할**

- Terraform은 **실제 인프라 상태를 `.tfstate` 파일에 저장**하여 변경 사항을 추적.
- 이 파일을 기반으로 현재 인프라와 Terraform 코드 간의 차이를 분석하여 **추가/수정/삭제할 리소스를 결정**.

✅ **`backend` 블록의 역할**

- `.tfstate` 파일을 **어디에 저장할지 결정** (기본값: `local`).
- 여러 사용자가 동일한 인프라를 관리할 경우 **공유 저장소(S3, GCS, Azure 등)를 사용하여 협업 가능**.
- `.terraform` 디렉터리에 backend 설정을 저장하여 **보안 및 인증 관리 가능**.

✅ **Terraform `backend` 블록 예제 (S3 사용 시)**

```hcl
terraform {
  backend "s3" {
    bucket = "my-terraform-state-bucket"
    key    = "terraform.tfstate"
    region = "us-west-2"
  }
}
```

- `bucket`: S3 버킷 이름
- `key`: `.tfstate` 파일 저장 경로
- `region`: S3가 위치한 AWS 리전

✅ **백엔드 설정 후 `terraform init` 실행 필요**

```shell
terraform init
```

- `backend` 설정 변경 시 `terraform init`을 다시 실행해야 적용됨.

---

## **Terraform `backend` 유형**

Terraform은 다양한 백엔드 저장소를 지원하며, 대표적인 옵션은 다음과 같음.

|**백엔드 타입**|**설명**|**주요 특징**|
|---|---|---|
|**local**|기본 백엔드 (로컬 저장)|단일 사용자용, 협업 불가능|
|**S3**|AWS S3에 저장|버전 관리 가능, Lock 기능 없음|
|**DynamoDB (S3와 함께 사용)**|상태 파일 Lock 지원|다중 사용자 충돌 방지 가능|
|**GCS**|Google Cloud Storage|GCP 환경에서 사용 가능|
|**Azure Storage**|Azure Blob Storage에 저장|Azure 기반 프로젝트에서 사용|
|**PostgreSQL**|PostgreSQL 데이터베이스에 저장|상태를 DB에 저장, SQL 쿼리 가능|

---

## **Terraform S3 Backend 사용 시 주의할 점**

✅ **1. 상태 파일의 동시 수정 문제 (State Lock)**

- **S3는 기본적으로 state lock을 제공하지 않음**.
- 여러 사용자가 동시에 Terraform을 실행할 경우, **경합(Race Condition) 발생 가능**.
- **DynamoDB를 추가로 설정하면 Lock 기능 활성화 가능**.

✅ **2. DynamoDB와 함께 S3를 사용하여 Lock 기능 추가하기**

```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state-bucket"
    key            = "terraform.tfstate"
    region         = "us-west-2"
    dynamodb_table = "terraform-state-lock"
  }
}
```

- `dynamodb_table`: DynamoDB 테이블을 사용하여 상태 잠금(Lock) 지원.
- **Terraform 작업 중 충돌 방지 (예: 두 사용자가 동시에 `terraform apply` 실행 시 오류 방지)**.

✅ **3. S3 상태 파일의 보안 관리**

- **버킷 암호화 활성화** (`AES-256` 또는 `KMS` 사용)
- **버킷 버전 관리(Versioning) 활성화**하여 변경 이력 저장
- **S3 액세스 정책을 제한**하여 불필요한 권한 차단

✅ **4. Terraform Remote State 데이터 읽기**

- 다른 Terraform 모듈에서 `.tfstate` 정보를 참조하려면 `terraform_remote_state` 사용 가능.

```hcl
data "terraform_remote_state" "vpc" {
  backend = "s3"
  config = {
    bucket = "my-terraform-state-bucket"
    key    = "vpc/terraform.tfstate"
    region = "us-west-2"
  }
}

output "vpc_id" {
  value = data.terraform_remote_state.vpc.outputs.vpc_id
}
```

- 위 코드에서는 `terraform_remote_state`를 사용해 다른 `.tfstate` 파일에서 **VPC ID를 가져옴**.

---

## **Terraform `backend` 블록 사용 예제**

✅ **1. 기본 Local backend 사용 (default 값)**

```hcl
terraform {
  backend "local" {
    path = "terraform.tfstate"
  }
}
```

- `.tfstate` 파일이 로컬 디렉터리에 저장됨.

✅ **2. AWS S3 backend 사용 (DynamoDB 포함)**

```hcl
terraform {
  backend "s3" {
    bucket         = "terraform-state-bucket"
    key            = "global/s3/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-state-lock"
    encrypt        = true
  }
}
```

- `.tfstate`를 **AWS S3 버킷에 저장**.
- **DynamoDB 테이블을 사용하여 State Lock 활성화**.
- **파일 암호화(`encrypt = true`) 적용**.

✅ **3. Google Cloud Storage(GCS) backend 사용**

```hcl
terraform {
  backend "gcs" {
    bucket = "terraform-state-bucket"
    prefix = "terraform/state"
  }
}
```

- GCS를 백엔드로 사용하여 **GCP 기반 인프라 상태 관리 가능**.

✅ **4. Azure Storage backend 사용**

```hcl
terraform {
  backend "azurerm" {
    storage_account_name = "terraformstate"
    container_name       = "tfstate"
    key                 = "terraform.tfstate"
  }
}
```

- Azure의 Blob Storage를 사용하여 `.tfstate` 파일 저장.

---

# **Terraform `backend` 블록 주요 비교**

|**Backend 타입**|**저장 위치**|**장점**|**단점**|
|---|---|---|---|
|**local**|로컬 파일|설정 간단, 빠름|협업 불가능, 상태 공유 어려움|
|**S3**|AWS S3|중앙 집중 관리 가능|기본적으로 Lock 기능 없음|
|**S3 + DynamoDB**|AWS S3 + DynamoDB|상태 Lock 가능|추가적인 설정 필요|
|**GCS**|Google Cloud Storage|GCP 인프라와 연동|IAM 관리 필요|
|**Azure Storage**|Azure Blob Storage|Azure 환경에 적합|Terraform 설정 필요|
|**PostgreSQL**|PostgreSQL DB|SQL 기반 관리 가능|성능 이슈 발생 가능|

---

# **참고 자료**

- [Terraform Official Docs - Backend](https://developer.hashicorp.com/terraform/language/settings/backends)
- [AWS S3 & DynamoDB State Locking](https://developer.hashicorp.com/terraform/language/state/locking)
- [Terraform Remote State Data](https://developer.hashicorp.com/terraform/language/state/remote-state)

---

## 🚀 **결론**

- **Terraform `backend` 블록은 `.tfstate` 파일의 저장 위치를 정의하며, 협업 시 S3 같은 원격 스토리지를 사용해야 함**.
- **S3를 사용할 경우, DynamoDB를 함께 설정하여 Lock 기능을 활성화하는 것이 필수적**.
- **Terraform Remote State를 활용하면 다른 프로젝트에서 기존 인프라 상태를 쉽게 참조 가능**.
- **적절한 백엔드 타입을 선택하여 인프라 상태를 안전하게 관리하는 것이 중요**. 🚀