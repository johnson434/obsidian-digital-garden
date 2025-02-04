---
{"dg-publish":true,"permalink":"/06-full-notes/section-11-amazon-s3-introduction/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ultimate AWS Certified SysOps Administrator Associate 2024\|Ultimate AWS Certified SysOps Administrator Associate 2024]]

---
# 단서 질문
- S3는 무엇인가요?
	- AWS에서 제공하는 무한대로 확장가능한 저장 공간
- S3는 스코프 범위가 어떻게 되나요?
	- S3의 Region에 설정합니다. 그러나, Region에 한정되지 않고 전세계에서 접근 가능합니다.
- S3의 사용 예시 3가지만
    1. archive: 기록 백업용
    2. Media Hosting: 미디어 파일을 호스팅해준다.
    3. Hybrid Cloud Storage: 온프로미스와 같이 사용하는 저장 공간. Storage Gateway를 통해서
- 버켓 네이밍룰은 어떻게 되나요?
	- 영어 소문자나 숫자로 시작하며 대문자는 사용이 불가능하다. 또한 하이픈(’-’)을 사용할 수 있다.
	- 또한, AWS 전체에서 이름이 유일(unique)해야 한다.
- S3 Versioning이랑?
	- 오브젝트에 덮어씌울 때마다 버전을 사용하여 이전 오브젝트를 복구할 수 있게 설정한다.
- Bucket Policy로 할 수 있는 일
    - 특정 Organization만 접근 허가
    - S3 업로드 시에 암호화 강제하기
    - S3 작업 시에 MFA 인증 받기
- AWS S3로 호스팅한 웹사이트로 요청을 했는데 403 Forbidden 에러가 나오면 원인이 뭔가?
	- S3에 퍼블릭 리드 권한을 안줘서
- 버저닝을 Enable하면 오브젝트의 버전이 어떻게 되는가?
	- null로 시작한다.
- 버저닝을 중단하면 오브젝트의 이전 버전들은 어떻게 되는가?
	- 유지된다.
- Storage Class 크게 3가지로 분류
	- Standard
	- IA
	- Glacier
- S3의 내구성(Durability)
	- 11 9's = 99.999999999%
- S3는 기본적으로 높은 가용성을 위해서 AZ 3개를 쓴다. (One-zone IA 제외)
---
# 핵심 요약
- S3는 무한대로 확장 가능한 AWS의 객체 스토리지 서비스입니다.
- 버킷은 S3의 기본 컨테이너로, 글로벌 고유 이름을 가져야 합니다.
- S3는 버킷 정책, IAM 정책, ACL 등 다양한 보안 옵션을 제공합니다.
- S3 버저닝을 통해 객체의 여러 버전을 유지 관리할 수 있습니다.
- S3 복제를 사용하여 버킷 간에 객체를 자동으로 복사할 수 있습니다.
- S3는 Standard, Infrequent Access, Glacier 등 다양한 스토리지 클래스를 제공합니다.
- S3 Intelligent-Tiering은 액세스 패턴에 따라 객체를 자동으로 이동시켜 비용을 최적화합니다.
- S3는 또한 데이터 수명 주기 관리 기능을 제공하여 객체를 자동으로 다른 스토리지 클래스로 이동하거나 삭제할 수 있습니다.
- 이를 통해 장기 보관 데이터의 스토리지 비용을 최적화할 수 있습니다. 또한, S3는 강력한 데이터 암호화 옵션을 제공하여 저장 및 전송 중인 데이터의 보안을 보장합니다.
- S3는 또한 다양한 데이터 분석 도구와 통합되어 있어, 저장된 데이터에 대한 인사이트를 얻는 데 도움을 줍니다.
- 예를 들어, Amazon Athena를 사용하면 S3에 저장된 데이터에 대해 SQL 쿼리를 실행할 수 있습니다.
- S3 Select 기능을 사용하면 전체 객체를 다운로드하지 않고도 필요한 데이터만 효율적으로 검색할 수 있습니다.
---
# 핵심 필기
# S3 Overview
### Amazon S3 Use cases
- 백업과 스토리지 저장소
- DR
- 아카이브
- Hybrid Cloud Storage: 온프로미스와 클라우드를 병행해서 사용
- 어플리케이션 호스팅
- 미디어 호스팅
- Data lakes & 빅데이터 분석
- 소프트웨어 배포(delivery)
- 정적 웹사이트
### Amazon S3 Buckets
- S3는 Object라는 단위로 파일을 Bucket에 저장한다.
- Bucket의 이름은 모든 AWS에서 유니크하다. (모든 리전, 모든 계정을 포함해서 유니크하다.)
- Bucket은 Region Level에서 정의된다.
- S3는 글로벌 서비스처럼 보이지만, S3는 특정 리전에 만들어진다.
- 다른 리전에서 접속은 가능하지만 Latency가 크다. (Multi Region Access Point로 해결 가능)
### Amazon S3 - Objects
- Object는 key를 가진다.
- key는 전체 경로다.
	- s3://bucket-name/file_name.txt
	- s3://bucket-name/second_directory/image_folder/image.jpeg
- key는 prfix + object name으로 이뤄진다. 
	- 전체 경로: s3://bucket-name/second_directory/image_folder/image.jpeg
	- 키: second_directory/image_folder/
	- 오브젝트명: image.jpeg
- S3는 UI가 디렉터리처럼 보이지만 디렉터리란 컨셉이 존재하지 않는다.
### Amazon S3 - Objects (cont.)
- 오브젝트의 값들은 body의 content입니다.
	- 오브젝트 최대 크기는 5테라바이트
	- [오브젝트 크기가 100MB보다 커지면 멀티파트 업로드를 쓰는 게 좋고](https://docs.aws.amazon.com/AmazonS3/latest/userguide/mpuoverview.html), 5GB보다 커지면 무조건 멀티파트 업로드를 사용한다. [단일 Object로 업로드 가능한 크기 제한이 5GB다.](https://aws.amazon.com/ko/blogs/korea/amazon-s3-multi-part-dowload/)
- 메타데이터 (키와 값의 쌍으로 시스템 정보나 유저 정보가 들어간다)
- Tags (유니코드 키 / 값 페어 - 최대 10) - 보안과 라이프사이클에 유용하다.
- Version ID (Versioning이 활성화되었다면)
---
## S3 Security
### Amazon S3 - Security
- S3 버킷에 접근 권한으론 유저 기반 정책과, 리소스 기반 정책이 있다.
	1. 유저 기반
		- IAM Policies - IAM의 특정 유저에게 API 호출을 허락하는 방법
	2. 리소스 기반
		- Bucket Policies - 버켓 내부에서 접근 권한을 설정하는 방법 - **다른 계정도 허락 가능**하며 JSON 형식으로 작성하여 Bucket ACL보다 디테일하게 설정 가능
		- Object Access Control List (ACL) - 오브젝트(파일) 별로 접근 권한을 허횽
		- Bucket Access Control List - Bucket Policies와 동일하게 버켓과 해당하는 버켓의 Object에 접근 권한을 설정
- Note: IAM Principal이 S3 오브젝트에 접근하려면
	- User가 IAM 권한이 있거나 혹은 리소스 기반 정책으로 허용해야 한다.(버켓에서 허용)
	- 그리고 명시적인 DENY가 없다.
- **암호화**: S3 Encryption Key로 암호화가 가능하다.
### Bucket settings for Block Public Access
![Pasted image 20250118210026.png](/img/user/image/Pasted%20image%2020250118210026.png)
- 퍼블릭 액세스를 허용하지 않기 위해 만든 규칙
- 계정 레벨에서 설정 가능하다.
---
## S3 Security: Bucket Policy Advanced
### S3 Bucket Policies
- [AWS에서 제공하는 Bucket Policy 예제](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies.html)
- S3 버킷 정책은 다음 상황에서 사용하세요.
	- Bucket에 public access를 주기 위해서
	- 오브젝트 업로드 시에 암호화를 강제하기 위해서
	- 다른 계정에 권한을 주기 위해서(CrossAccount)
- 선택적 조건으로 다음과 같은 제한을 걸 수 있다.
	- Public IP or Elastic IP (not on Private IP)
	- Source VPC or Source VPC Endpoint - 특정 VPC나 VPC Endpoint로만 접근 가능하게
	- CloudFront Origin Identity
	- MFA 인증 여부
## S3 Website Overview
### Amazon S3 - Static Website Hosting
- S3는 인터넷을 통해 접속 가능한 정적 웹사이트를 호스팅 가능하다. 
- URL은 Region에 따라 설정됨
	- ex: http://bucket-name.s3-website-aws-region.amazonaws.com
- `403 Forbidden` 에러를 던지면 Bucket Policy에 Public read 권한을 줬는지 확인.
- **주의 사항**: aws s3api put-object 시에 `content-type`으로 `text/html` 명시하지 않으면 html로 판단하지 않아서 html 파싱이 일어나지 않고 다운로드가 진행됨.
### S3 Versioning
- S3에서 Object를 버저닝 가능
- bucket 마다 설정 가능하다.
- 버전은 1,2,3 순서로 증가한다.
- 버저닝의 이점은 다음과 같다.
	- 의도치 않은 삭제 시에 복원 가능
	- 쉬운 이전 버전 롤백
- **Notes:**
	- **버저닝 이전에 업로드된 object**들의 버전은 "null"이다.
	- 버저닝을 중단해도 이전 버전들은 삭제되지 않는다.
### S3 Replication
- 필수 조건: `source`와 `destination` 버켓 Versioning 활성화
- Replication 종류는 두 가지가 있다.
	- CRR (Cross Region Replication)
	- SRR (Sample Region Replication)
- 서로 다른 계정의 버킷 간에도 가능하다.
- 복제는 비동기로 이뤄진다. (source에서 destination의 복제가 완료 되기를 기다리지 않음.)
- S3의 적절한 IAM 권한이 필요하다.
- Use cases:
	- CRR - compliance(규정 때문에 서로 다른 Region의 백업이 필요한 경우), lower latency access, replication across accounts
	- SRR - log aggregation(로그 집약), live replication between production and test accounts
### S3 Replication Notes
- Replication를 설정한 후부터 만들어진 새로운 object부터 복제된다.
- 옵션으로 `S3 Batch Replication`를 통해 복제도 가능하다.
	- Object를 복제하고 복제 실패한 Object도 복제한다.
- DELETE 작업
	- (선택적 옵션) source의 delete 마커를 target에 복제할 수 있다.
	- 특정 버전의 삭제는 복제되지 않는다. (malicious 삭제를 피하기 위해서)
		- `source` 버킷에서 침입자가 버전들을 삭제해도 `destination` 버켓에서 버전은 남아있다. 따라서, 복구가 가능하다.
---
## S3 Storage Classes Overview
### S3 Storage Class
- https://aws.amazon.com/s3/storage-classes/
- 스토리지 클래스는 변경 가능하다. (수동으로 or S3 Lifecycle 설정을 통해서)
### S3 Durability and Availability
- Durability (내구성)
	- 높은 지속성을 가진다. (99.999999999% 11 9's)
	- 10,000,000를 매 년 저장한다고 가정했을 때, 10,000년에 Object 1개가 손실될 수 있다.
	- 모든 스토리지 클래스가 위와 같은 지속성을 가진다.
- Availability (가용성)
	- 내구성과 달리 스토리지 클래스에 따라 달라진다. (3AZ가 기본인데 One-zone IA 이런 애들은 비용 아끼려고 AZ를 하나만 씀. 따라서, 가용성이 떨어질수밖에)
---
## S3 Standard - General Purpose
### S3 Standard - General Purpose
- 99.99% 가용성
- 자주 접근 되는 데이터
- 낮은 지연성과 높은 처리량
- AZ를 3개 사용 => 시설이 2개 동시에 망가져도 동작(Failover)
- Use Cases:
	- 빅데이터 분석
	- 모바일 게이밍 어플리케이션
	- Content Distribution
---
## S3 Storage Classes - Infrequent Access
### S3 Storage Classes - Infrequent Access (IA)
- GP보다 덜 접근되는 데이터들. 그러나, 필요할 땐 빠른 접근이 필요할 때
- S3 Standard보다 저렴함. 그러나, retrieval 비용이 듬.
- **Amazon S3 Standard-Infrequent Access (S3 Standard-IA)**
	- 99.9% Availability
	- 사용 예: 재난 복구, 백업
- **Amazon S3 One Zone-Infrequent Access (S3 One Zone-IA)**
	- 단일 AZ에서 높은 지속성(99.99999999%)을 가진다. AZ가 파괴되면 데이터 유실
	- 99.5% Availability
	- 사용 예: 두 번째 백업 카피
---
## Amazon S3 Glacier Storage Classes
### Amazon S3 Glacier Storage
- 저비용 아카이빙/백업용 오브젝트 저장소
- 비용: 저장소 사용 공간 가격 + 오브젝트 검색(retrieving) 비용

- **Amazon S3 Glacier Instant Retrieval**
	- 복구에 milisecond, 분기 별로 접근되는 데이터에 사용하면 좋다.
	- Inteligent Tiering에선 90일 이상 접근하지 않으면 이 Class로 옮긴다.
- **Amazon S3 Glacier Flexible Retrieval** (이전엔 S3 Glacier라고 불렀다)
	- Expedited (1~5분), Standard (3~5시간), Bulk (5 to 12 hours) - Free
	- Inteligent Tiering에선 90일 이상 접근하지 않으면 이 Class로 옮긴다.
- **Amazon S3 Glacier Deep Archive - 장기간 보관 시에**
	- Standard (12시간), Bulk (48시간)
	- Inteligent Tiering에선 180일 이상 접근하지 않으면 이 Class로 옮긴다.
- AWS S3 공격으로 오래된 Object만 접근해서 비용 많이 나오게 할 수도 있지 않나.
---
## S3 Intelligent-Tiering
### S3 Intelligent-Tiering
- 한 달에 모니터링이랑 auto-tiering 요금을 부과한다.
- Object 사용 빈도에 따라 자동으로 Access Tier를 변경
- Intelligent Tiering엔 조회 비용(retrieval cahrges)이 없다.

- Frequent Access tier (automatic): default tier
- Infrequent Acces tier (automatic): 30일동안 접근 안되면
- Archive Instant Access tier (automatic): 90일동안 접근 안되면
- Archive Access tier (optional): 90일에서 700일 이상
- Deep Archive Access tier (optional): 180일에서 700일 이상
## Storage Class 총 정리
- 크게 3가지로
	- Standard
		- 속도 빠름
		- 비용 제일 비쌈
		- retrieve 비용x
	- IA(Infrequent Access)
		- GP보다 저렴
		- 속도면에서 GP랑 비슷함.
		- 대신, retrieve 비용이 듬
	- Glacier
		- 즉시 조회랑 분 ~ 시간 조회까지 있음.
		- 1년에 몇 번 조회 안되는 데이터를 사용
### S3 Storage Class Comparison Table

| **Storage Class**                  | **Use Cases**                                                                 | **First Byte Latency** | **Durability** (11 nines) | **Designed Availability** | **Availability SLA** | **Availability Zones** | **Minimum Storage Duration Charge** | **Retrieval Charge** | **Lifecycle Transitions** |
| ---------------------------------- | ----------------------------------------------------------------------------- | ---------------------- | ------------------------- | ------------------------- | -------------------- | ---------------------- | ----------------------------------- | -------------------- | ------------------------- |
| **S3 Standard**                    | General purpose storage for frequently accessed data                          | Milliseconds           | Yes                       | 99.99%                    | 99.9%                | ≥3                     | N/A                                 | N/A                  | Yes                       |
| **S3 Intelligent-Tiering***        | Automatic cost savings for data with unknown or changing access patterns      | Milliseconds           | Yes                       | 99.9%                     | 99%                  | ≥3                     | N/A                                 | N/A                  | Yes                       |
| **S3 Express One Zone***           | High-performance storage for your most frequently accessed data               | Single-digit ms        | Yes                       | 99.95%                    | 99.9%                | 1                      | 1 hour                              | N/A                  | No                        |
| **S3 Standard-IA**                 | Infrequently accessed data that needs millisecond access                      | Milliseconds           | Yes                       | 99.9%                     | 99%                  | ≥3                     | 30 days                             | Per GB retrieved     | Yes                       |
| **S3 One Zone-IA***                | Re-creatable infrequently accessed data                                       | Milliseconds           | Yes                       | 99.5%                     | 99%                  | 1                      | 30 days                             | Per GB retrieved     | Yes                       |
| **S3 Glacier Instant Retrieval**   | Long-lived data that is accessed a few times per year with instant retrievals | Milliseconds           | Yes                       | 99.9%                     | 99%                  | ≥3                     | 90 days                             | Per GB retrieved     | Yes                       |
| **S3 Glacier Flexible Retrieval*** | Backup and archive data that is rarely accessed and low cost                  | Minutes or hours       | Yes                       | 99.99%                    | 99.9%                | ≥3                     | 90 days                             | Per GB retrieved     | Yes                       |
| **S3 Glacier Deep Archive***       | Archive data that is very rarely accessed and very low cost                   | Hours                  | Yes                       | 99.99%                    | 99.9%                | ≥3                     | 180 days                            | Per GB retrieved     | Yes                       |

| **Attribute**                       | **S3 Standard**                                      | **S3 Intelligent-Tiering***                                              | **S3 Express One Zone***                              | **S3 Standard-IA**                                 | **S3 One Zone-IA***                     | **S3 Glacier Instant Retrieval**                                      | **S3 Glacier Flexible Retrieval***                 | **S3 Glacier Deep Archive***                             |
| ----------------------------------- | ---------------------------------------------------- | ------------------------------------------------------------------------ | ----------------------------------------------------- | -------------------------------------------------- | --------------------------------------- | --------------------------------------------------------------------- | -------------------------------------------------- | -------------------------------------------------------- |
| **Use Cases**                       | General purpose storage for frequently accessed data | Automatic cost savings for data with unknown or changing access patterns | High-performance storage for frequently accessed data | Infrequently accessed data with millisecond access | Re-creatable infrequently accessed data | Long-lived data accessed a few times per year with instant retrievals | Backup and archive data, rarely accessed, low cost | Archive data that is very rarely accessed, very low cost |
| **First Byte Latency**              | Milliseconds                                         | Milliseconds                                                             | Single-digit milliseconds                             | Milliseconds                                       | Milliseconds                            | Milliseconds                                                          | Minutes or hours                                   | Hours                                                    |
| **Durability**                      | 99.999999999% (11 nines)                             | 99.999999999% (11 nines)                                                 | 99.999999999% (11 nines)                              | 99.999999999% (11 nines)                           | 99.999999999% (11 nines)                | 99.999999999% (11 nines)                                              | 99.999999999% (11 nines)                           | 99.999999999% (11 nines)                                 |
| **Designed Availability**           | 99.99%                                               | 99.9%                                                                    | 99.95%                                                | 99.9%                                              | 99.5%                                   | 99.9%                                                                 | 99.99%                                             | 99.99%                                                   |
| **Availability SLA**                | 99.9%                                                | 99%                                                                      | 99.9%                                                 | 99%                                                | 99%                                     | 99%                                                                   | 99.9%                                              | 99.9%                                                    |
| **Availability Zones**              | ≥3                                                   | ≥3                                                                       | 1                                                     | ≥3                                                 | 1                                       | ≥3                                                                    | ≥3                                                 | ≥3                                                       |
| **Minimum Storage Duration Charge** | N/A                                                  | N/A                                                                      | 1 hour                                                | 30 days                                            | 30 days                                 | 90 days                                                               | 90 days                                            | 180 days                                                 |
| **Retrieval Charge**                | N/A                                                  | N/A                                                                      | N/A                                                   | Per GB retrieved                                   | Per GB retrieved                        | Per GB retrieved                                                      | Per GB retrieved                                   | Per GB retrieved                                         |
| **Lifecycle Transitions**           | Yes                                                  | Yes                                                                      | No                                                    | Yes                                                | Yes                                     | Yes                                                                   | Yes                                                | Yes                                                      |

---
# 참고 자료

https://aws.amazon.com/ko/blogs/korea/amazon-s3-multi-part-dowload/
https://docs.aws.amazon.com/AmazonS3/latest/userguide/mpuoverview.html