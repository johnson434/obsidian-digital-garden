---
{"dg-publish":true,"permalink":"/06-full-notes/section-18-aws-account-management/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ultimate AWS Certified SysOps Administrator Associate 2024\|Ultimate AWS Certified SysOps Administrator Associate 2024]]
---
# 단서 질문 및 답변

---
# 핵심 요약
## 🔍 **1. AWS 계정 상태 및 모니터링**
### 🩺 **AWS Health 대시보드**
- **전체 서비스 상태(Service History)**: 리전/서비스별 상태 확인 및 과거 이력 제공
- **계정 상태 대시보드(Account Health Dashboard)**: 내 계정 리소스에 영향을 주는 이벤트 제공 (개인화된 PHD)
- **글로벌 서비스 대시보드**: 장애 발생 시 영향 분석, 복구 가이드 제공
- **EventBridge 연동** 가능: 이벤트 감지 후 SNS/Lambda/SQS 등을 통한 자동 대응
- 📌 예: IAM 키 유출 시 자동 삭제, EC2 퇴역 시 자동 재시작
---
## 🏢 **2. AWS Organizations**
### 🧩 조직 구조 및 계정 관리
- 관리 계정 + 멤버 계정으로 구성
- 하나의 조직에만 속할 수 있음
- **OU(Organizational Units)**로 계정 그룹화 가능  
    (예: Dev, Prod, Finance 등 계층화)
### 💳 통합 결제(Consolidated Billing)
- 모든 계정의 비용을 하나로 청구
- **RI, Savings Plans 할인 공유 가능**
- 할인 공유 제한도 가능 (계정별 적용 비활성화)
### 🔐 보안 정책 관리
- **서비스 제어 정책(SCP)**으로 IAM 권한보다 상위 제어 가능  
    (예: 특정 OU는 EC2만 허용, S3 차단 등)
- `aws:PrincipalOrgID`로 IAM 정책에서 조직 제한 가능
### 🏷 태그 정책(Tag Policies)
- 태그 키/값의 일관성을 정책으로 관리
- 비용 관리, 액세스 제어(ABAC), 감사에 활용
---
## 🔧 **3. 계정 환경 자동화 및 표준화 도구**
### 🏗 AWS Control Tower
- **조직/계정/OU/SCP를 자동으로 설정**해주는 환경 자동화 도구
- 정책 위반 탐지 및 자동 복구
- 규정 준수 상태 대시보드 제공
### 🛍 AWS Service Catalog
- 승인된 인프라 템플릿(포트폴리오)을 제공하고 배포 가능
- 조직/계정 단위로 공유 및 제어 가능
---
## 💰 **4. 비용 및 최적화 도구**
### 📈 **Billing & Cost Tools**
- **Billing Console**: 비용 확인, 결제 정보 관리
- **Cost Explorer**: 사용량 및 비용 시각화, 예측 기능
- **AWS Budgets**: 예산 초과 시 알림 설정 (SNS 연동 가능)
- **Billing Alarm**: CloudWatch 기반 전체 계정 비용 알림
### 🧠 **AWS Compute Optimizer**
- ML 기반 리소스 최적화 추천 (EC2, EBS, Lambda 등)
- 25% 비용 절감 가능성
- 추천 데이터를 S3로 내보내기 가능
---
## 💡 실전 포인트 요약

| 도구                                 | 실전 활용                       |
| ---------------------------------- | --------------------------- |
| **Health Dashboard + EventBridge** | 장애/보안 이벤트에 자동 대응            |
| **Organizations + SCP**            | 계정 그룹별 강력한 보안 정책 관리         |
| **Control Tower**                  | 조직 초기 셋업을 빠르게 자동화           |
| **Tag Policy**                     | 태깅 일관성 확보 → 비용 추적 및 정책 연계   |
| **Budgets / Explorer / Optimizer** | 비용 초과 감지, 예측, 리소스 최적화 자동 추천 |

---
# 핵심 필기
## AWS 계정 관리
- **AWS Health 대시보드**: 서비스 및 계정 상태를 모니터링하는 도구
- **AWS Organizations**: 여러 AWS 계정을 효율적으로 관리하는 서비스
- **청구 콘솔(Billing Console)**: AWS 서비스 비용을 모니터링하고 최적화

---
## AWS Health 대시보드
### 서비스 기록 (Service History)
- 모든 리전 및 서비스의 상태 확인
- 각 날짜별로 과거 기록 제공
- RSS 피드 구독 가능
- 이전 명칭: AWS 서비스 상태 대시보드(AWS Service Health Dashboard)
### 계정 상태 대시보드(Account Health Dashboard)
- 이전 명칭: AWS 개인 상태 대시보드(Personal Health Dashboard, PHD)
- AWS 서비스 이벤트가 계정에 미치는 영향을 분석 및 경고 제공
- 서비스 상태 대시보드는 AWS 전체의 상태를 제공하지만, 계정 상태 대시보드는 개인화된 정보를 제공
- 계정 내 리소스와 연관된 정보 제공 및 사전 알림 기능
- 전체 AWS Organizations에서 데이터 집계 가능
### 글로벌 서비스 대시보드
- AWS 장애가 계정 및 리소스에 미치는 영향 분석
- 이벤트 알림, 복구 가이드, 사전 계획된 유지보수 활동 제공

---
## Health 이벤트 알림
![Pasted image 20250313143509.png](/img/user/image/Pasted%20image%2020250313143509.png)
- **EventBridge를 사용하여 AWS Health 이벤트를 감지하고 반응**
- 예: EC2 인스턴스 업데이트 예정 알림 이메일 수신
- 계정 이벤트(내 리소스 관련 이벤트) 및 공용 이벤트(리전 내 서비스 상태) 구분
- 이벤트 기반 자동 대응 가능 (Lambda, SNS, SQS, Kinesis 데이터 스트림 활용 가능)

### Health 대시보드 예제

- **IAM 키 유출 감지 시 자동 삭제**
![Pasted image 20250313143559.png](/img/user/image/Pasted%20image%2020250313143559.png)
- **EC2 인스턴스 퇴역 예정 시 자동 재시작**
![Pasted image 20250313143616.png](/img/user/image/Pasted%20image%2020250313143616.png)
---

## AWS Organizations
- 여러 AWS 계정을 관리하는 글로벌 서비스
- 관리 계정(Management Account)과 멤버 계정(Member Account)으로 구성
- 멤버 계정은 하나의 조직에만 속할 수 있음
- 전체 계정에 대한 통합 결제(Consolidated Billing) 지원
- EC2, S3 등의 서비스에서 볼륨 기반 할인 적용 가능
- 예약 인스턴스(Reserved Instances) 및 세이빙 플랜(Savings Plans) 공유
- API를 통해 계정 생성 자동화 가능

### 조직 계층 구조 예제
![Pasted image 20250313150240.png](/img/user/image/Pasted%20image%2020250313150240.png)
- **Root OU (최상위 조직 단위)**
    - 관리 계정
    - 개발(Dev) OU
    - 운영(Prod) OU
    - HR 및 재무(Finance) OU
![Pasted image 20250313150509.png](/img/user/image/Pasted%20image%2020250313150509.png)
---

## 조직 단위 (Organizational Units, OU) 예제
### 비즈니스 단위별 계층 구조
- Sales OU, Retail OU, Finance OU 등으로 계정을 분리하여 관리
- 예: Sales OU 하위에 개별 판매 팀 계정 배치
### 환경 수명 주기 관리
- 운영(Prod), 개발(Dev), 테스트(Test) 환경으로 계정 분리
### 프로젝트 기반 계층 구조
- 프로젝트별로 별도 OU 구성하여 관리
---
## 조직의 보안 정책 (Security Policies)
- **서비스 제어 정책(SCP)**: OU 및 계정 단위로 IAM 정책을 적용하여 사용자 및 역할 제한 가능
- **SCP 계층 구조 예제**:
    - 특정 OU 내 모든 계정에서 S3 접근 제한
    - 특정 계정에서 EC2 서비스만 허용
    - 관리 계정은 모든 작업 수행 가능 (SCP 미적용)
---
## 예약 인스턴스(Reserved Instances) 및 세이빙 플랜(Savings Plans) 정책
- AWS Organizations 내 모든 계정이 예약 인스턴스(RI) 및 세이빙 플랜 할인을 공유 가능
- 관리 계정에서 특정 계정의 할인 공유 기능 비활성화 가능
---
## IAM 정책 적용
- **aws:PrincipalOrgID 조건 키 활용**하여 AWS Organizations 내 사용자 액세스 제어 가능
- 예제: 특정 S3 버킷에 대한 액세스를 AWS Organizations 내 특정 계정으로 제한
![Pasted image 20250313150823.png](/img/user/image/Pasted%20image%2020250313150823.png)
---

## 태그 정책 (Tag Policies)
![image.UMK922.png](/img/user/image/image.UMK922.png)
- 리소스 태그 일관성을 유지하도록 정책 설정 가능
- 허용된 태그 키 및 값 정의 가능
- 비용 할당 태그(AWS Cost Allocation Tags)와 속성 기반 액세스 제어(ABAC) 활용 가능
- 클라우드 환경 모니터링 및 감사 기능 제공

---

## AWS Control Tower
![image.Z21T32.png](/img/user/image/image.Z21T32.png)
- 다중 계정 환경을 안전하고 규정을 준수하는 방식으로 설정하는 서비스
- **주요 기능**
    - 간단한 클릭만으로 환경 설정 자동화
    - 지속적인 정책 관리 자동화
    - 정책 위반 감지 및 자동 복구
    - 대시보드를 통한 실시간 규정 준수 모니터링
- AWS Organizations를 통해서 구현된다. 자동으로 AWS Organization과 계정을 설정하고 SCP를 구현한다.
---

## AWS 서비스 카탈로그 (AWS Service Catalog)
![image.0IWH32.png](/img/user/image/image.0IWH32.png)
- 사전 승인된 제품(가상 머신, 데이터베이스, 스토리지 옵션 등) 목록을 관리 및 배포
- 조직 내 사용자가 간편하게 인프라를 배포할 수 있도록 지원
- **공유 옵션**
    - 포트폴리오를 AWS 계정 또는 AWS Organizations에 공유 가능
    - 포트폴리오 참조 공유(원본과 동기화 유지) 또는 복사 배포(수동 업데이트 필요)

---

## AWS 비용 및 예산 관리
### AWS 청구 알람 (Billing Alarms)
- CloudWatch에서 비용 모니터링 및 알람 설정 가능
- 글로벌 AWS 비용에 대한 데이터 수집
- 특정 프로젝트별 비용 모니터링에는 적합하지 않음
### Cost Explorer
- AWS 비용 및 사용량을 분석하고 최적화하는 도구
- 사용자 정의 보고서 생성 가능
- 서비스별, 시간대별, 리소스 단위 비용 분석 지원
- 최적의 세이빙 플랜 추천 및 12개월 사용량 예측 기능 제공
### AWS 예산 (AWS Budgets)
- 비용 초과 시 알람 설정 가능
- 예산 유형: 사용량, 비용, 예약 인스턴스(RI), 세이빙 플랜
- 최대 5개의 SNS 알림 지원
- EC2, ElastiCache, RDS, Redshift 지원

---

## AWS Compute Optimizer

- 머신 러닝을 이용하여 최적의 AWS 리소스를 추천
- **지원 리소스**
    - EC2 인스턴스
    - EC2 오토 스케일링 그룹
    - EBS 볼륨
    - Lambda 함수
- 최대 25% 비용 절감 가능
- 추천 사항을 S3로 내보낼 수 있음
---
# 참고 자료
- 
# 주석
