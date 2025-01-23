---
{"dg-publish":true,"permalink":"/06-full-notes/section-12-advanced-amazon-s3-and-athena/","dgPassFrontmatter":true}
---

# Tags
[[03 - Tags/Ultimate AWS Certified SysOps Administrator Associate 2024\|Ultimate AWS Certified SysOps Administrator Associate 2024]]

---
# 단서 질문
- S3의 전송 속도를 높이는 방법 두 가지는?
	1. Multipart Upload 설정
		- 100MB 이상에서 사용을 권장한다.
		- 5GB 이상이면 무조건 사용해야만 올릴 수 있다.
	2. Accelerator를 사용
- S3는 자동으로 스케일링이 발생하는가?
	- S3는 초당 요청 비율(Update 유사 작업은 초당 3,500 조회 류 작업은 초당 5,500)에 따라서 스케일링이 발생한다.
- S3를 스케일링 없이 효율적으로 사용하기 위해선?
	- 스케일링은 Object의 Key를 기반으로 이뤄지기 때문에 적절하게 Key를 나눈다.
- MultiPart 업로드 중에 작업이 중단됐다. 업로드를 재개했을 때 어떻게 되는가?
	- 업로드되지 않은 파트만 업로드한다.
- MultiPart를 중단하고 이후에 작업을 재개하지 않고 일부만 업로드 된 경우가 있다. 이러한 Object들이 용량을 차지하는 예방하기 위해 해야할 것은?
	- Lifecycle Configuration을 통해서 중단된 MultiPart 업로드 Object들을 특정 기간 이후에 삭제한다.
- 특정 Application에선 Object의 헤더를 조회할 필요가 있다. 이를 효율적으로 처리하기 위해선?
	- byte-range를 통해서 특정 byte 범위만 조회가 가능하다. 이를 통해 네트워크 전송 비용을 아낄 수 있다.
---
# 핵심 요약
- S3 Lifecycle Rules란?
	- 오브젝트의 기간을 기준으로 삭제나 Storage Class 이동이 가능하다.
- S3 Event Notifications
	- S3의 특정 이벤트(PutObject, PutACL, GetObject 등)가 발생했을 때, 알림이 발생함.
	- 여러 가지 리소스(Lambda, EventBridge 등)와 같이 사용이 가능하다.
	- EventBridge는 이벤트 재전송 등 Event를 더 세밀하게 다룰 때 사용
- S3 Inventory란?
	- 주기적으로 S3의 보고서를 생성한다.
- S3 Athena란?
	- 서버리스 쿼리 서비스
	- 컬럼형 데이터베이스다. => 로우 조회보다 집계에 유리
---
# 핵심 필기
## S3 Lifecycle Rules (with S3 Analytics)
### S3 - Moving Between Storage Classes
- Object를 Storage Classes 별로 전송 가능하다.
- 빈번하게 접근되는 오브젝트는 Standard
- 빈번하게 접근되지 않는 오브젝트는 Standard IA
- 아카이빙 오브젝트는 Glacier나 Glacier Deep Archive
- Lifecycle Rules를 통해 오브젝트를 자동으로 옮길 수 있다.
![Pasted image 20250121110231.png](/img/user/image/Pasted%20image%2020250121110231.png)
---
## Lifecycle Rules
### AWS S3 - Lifecycle Rules
- 전송 액션 (Transition Actions) - 오브젝트를 다른 Storage Class로 변경 가능
	- 예: object를 60일 후에 Standard IA class로 이동하라.
	- 6개월 후에 아카이빙을 위해 Glacier로 이동해라.
- 만료 액션 (Expiration Actions) - 기간이 지나면 Object 삭제
	- 예: Object의 이전 버전을 삭제
	- 완료되지 않은 멀티파트 업로드를 삭제 가능. (멀티파트 업로드 중간에 작업을 취소하거나 서버에 오류가 발생하여 일부 파일만 업로드 된 경우에 멀티파트 파일들이 남아있을 수 있다. 해당 파일들을 삭제한다.)
- Lifecycle Rule은 특정 접두어를 위해 만들 수 있다. (ex: s3://mybucket/mp3/*)
- Lifecycle Rule은 특정 태그를 위해 만들 수 있다. (ex: env:dev)
### Lifecycle Rules (Scenario 1)
- 상황: Your application on EC2 creates images thumbnails after profile photos are uploaded to Amazon S3. These thumbnails can be easily recreated, and only need to be kept for 60 days. The source images should be able to be immediately retrieved for these 60 days, and afterwards, the user can wait up to 6 hours. How would you design this?
- S3 source 이미지는 Standard이고, Lifecycle 설정으로 60일 이후에는 Glacier로 전송
- S3 썸네일 이미지는 One-Zone IA, Lifecycle 설정으로 60일 이후에는 삭제
### Lifecycle Rules (Scenario 2)
- A rule in your company states that you should be able to recover your deleted S3 objects immediately for 30 days, although this may happen rarely. After this time, and for up to 365 days, deleted objects should be recoverable within 48 hours.
- 문제 조건:
	1. 30일 이내에 Object는 삭제해도 즉시 복구 가능
	2. 30일부터 365일 사이인 object는 48시간 내에 복구 가능
- 해결 방법
	- Versioning 활성화
		- 버저닝을 활성화하면 삭제 시에 deleted marker가 남는다. 이를 통해 복구 가능
	- 30일 ~ 365일은 48시간 내에 복구
		- Glacier Storage Class 중에서 Deep Archive는 retrieving 시간이 최대 48시간이므로 해당 클래스를 사용해서 비용을 아낀다.
### Amazon S3 Analytics - Storage Class Analysis
- 언제 적절한 storage class로 옮길지 도와준다.
- Standard랑 Standard IA 클래스만 추천한다.
	- One-Zone IA랑 Glacier에는 동작 안함.
- 리포트는 매일 업데이트 된다.
- 데이터 분석을 보려면 24시간에서 48시간 걸린다.
- Lifecycle 적용하는데 도움이 되는 자료다.
![Pasted image 20250121114345.png](/img/user/image/Pasted%20image%2020250121114345.png)
---
## S3 Event Notifications
### S3 Event Notifications
- S3 관련 이벤트(Object 생성, 조회, 복구 등)가 있다.
- 오브젝트 이름으로 이벤트 필터링이 가능하다. (ex: *.jpg)
- S3 이벤트를 수신하면 이벤트 브릿지를 통해서 다른 리소스로 트리거 가능 (예: S3 PutObject로 이미지를 업로드하고 PutObject를 감시하다가 Lambda를 트리거하여 썸네일 생성)
- S3 이벤트를 원하는 만큼 만들 수 있다.
- 이벤트 전송에 평균적으로 몇 초정도 걸리지만 가끔은 분 단위보다 길어질 수도 있다.
### S3 Event Notifications - IAM Permissions
- S3의 이벤트를 수신하기 위해서는 S3가 아닌 수신하는 리소스들(SNS, SQS, Lambda 등)에 리소스 기반 IAM 정책이 필요하다.
![Pasted image 20250121134830.png](/img/user/image/Pasted%20image%2020250121134830.png)

### S3 Event Notifications with Amazon EventBridge
![Pasted image 20250121160912.png](/img/user/image/Pasted%20image%2020250121160912.png)
- JSON rule을 통해서 Advanced filtering을 제공한다. (metadata, 오브젝트 크기, 이름 등 다양한 조건으로 필터링이 가능하다.)
- 여러 도착지(Multiple Destinations): 여러 리소스에 이벤트를 전달할 수 있다. (ex. Step Functions, Kinesis Streams / Firehost ...)
- EventBridge가 할 수 있는 일: 아카이브, 이벤트 재실행, 안정적인 이벤트 전달
---
## S3 Performance
### S3 - Baseline Performance
- S3는 높은 request rate(요청 빈도)와 Latency가 100ms-200ms가 되면 자동으로 스케일링한다.
- 버켓의 **특정 프리픽스를 기준으로 초당 3,500 PUT/COPY/POST/DELETE나 5,500 GET/HEAD 리퀘스트**를 보낼 수 있다.
	- 예: (object path => prefix)
		- `bucket/folder1/sub1/file` => `/folder1/sub1/`
		- `bucket/1/file` => `/1/`
- prefix 개수 제한은 없다.
- prefix에 균등하게 요청을 나눌 수 있으면 스케일링 없이 효율적으로 요청을 주고 받을 수 있다.
	- 예: `/image`라는 하나의 path로 모든 요청을 받으면 감당 가능한 GET/HEAD 요청 개수는 5,500개다. 그러나, 이를 `/image`, `/thumbnail`이라는 두 가지 path로 나누면 스케일링 없이 11,000개의 GET/HEAD 요청을 처리할 수 있다.
### S3 Performance
- 파일을 빠르게 전송하기 위한 2가지 방법이 있다.
1. Multi-Part upload 
	- 파일의 크기가 100MB 이상이면 권장한다.
	- 파일의 크기가 5GB 이상이면 필수다.
	- 파일을 여러 파트(Multi-Part)로 나누어 병렬적(Parellel)으로 동시에 업로드한다.
2. S3 Transfer Acceleration
	- 파일을 수신자의 region 근처에 있는 AWS Edge Location에 캐싱하여 파일 전송을 빠르게 한다.
	- 멀티파트 업로드와 같이 사용 가능하다.
### S3 Performance - S3 Byte-Range Fetches
- **특정 byte 범위(specific byte ranges)**를 요청함으로써 GET 요청을 병렬적으로 처리 가능하다.
- 실패해도 회복력이 좋다.
- Use cases:
	- 다운로드 속도 증가
	- 데이터의 부분만 검색 가능 (파일의 헤더만 다운)
``` bash
aws s3api get-object --bucket text-content --key dir/my_data --range bytes=8888-9999 my_data_range
```
### S3 Batch Operations
- 대용량 작업을 한 번의 요청으로 처리한다.
	- 예시
		- 오브젝트 메타데이터, properties 수정
		- S3 버켓 간에 오브젝트 복사
		- **암호화되지 않은 오브젝트 암호화**
		- ACL, Tags 수정
		- S3 Glacier에서 Object Restore
		- 람다 함수 호출
- 작업은 다음 3가지로 이뤄진다.
	1. 작업을 수행할 Object 목록
	2. 수행할 작업
	3. (선택 사항) 추가 파라미터
- S3 Batch Operations는 재시도, 진행도(Progress) 추적, 완료 알림 전송, 리포트 생성 등을 관리합니다.
- **S3 Inventory를 통해서 Object 리스트를 조회하고 S3 Select를 사용해서 이를 필터링할 수있습니다.**
### S3 Inventory
- 주기적으로 S3의 보고서를 생성하는 방식
- 버킷과 시간을 설정하여 해당 시간에 맞춰서 CSV, ORC 등의 형태로 보고서를 출력한다.
- 주기는 매일이나 1주
- 오브젝트와 함께 메타데이터 리스트를 나열한다. (S3 List API 작업의 대체 방법)
- 다음과 같은 서비스를 통해 쿼리가 가능하다.
	- Amazon Athena, Redshift Presto, Hive, Spark... 
- S3 Select를 통해서 생성된 리포트를 필터링 가능하다.
- 사용 예:
	- 비즈니스
	- 규약
	- 주기적으로 필요할 때
---
## S3 Glacier Overview
### Amazon S3 Glacier
![image.J1ZF02.png](/img/user/image/image.J1ZF02.png)
- 저비용 아카이빙 백업용 저장소
- 수십년 보관이 필요한 데이터들을 위한 Storage Class
- 온프로미스 마그네틱 하드드라이브의 대체품
- 한달 0.004/GB - Standard | 0.00099/GB Deep Archive
- Glacier의 저장소를 Vault라 하고 Vault에 저장되는 객체를 Archive라고 한다. Archive는 최대 40GB다.
- 기본 설정으로 모든 데이터는 AES-256을 통해 암호화된다. => Key는 AWS에 의해서 관리된다.
- 만약, XXX일이 지난 후에 아카이빙이 필요하면 Glacier를 써라.
### Amazon S3 Glacier Operations
- 볼트 작업(Operation)은 3가지가 있다.
	- Create & Delete: 삭제는 볼트 내부가 비었을 때(아카이브가 없을 때)만 가능
	- Retrieving Metadata: 만들어진 날, 아카이브 개수, 전체 사이즈 등 메타데이터를 조회
	- Download Inventory: 볼트 내에 아카이브 목록 (archive ID, creation date, size, ...)
- Glacier 작업(Operation)은 3가지가 있다.
	- Upload - 단일 업로드 혹은 크기가 큰 아카이브를 위한 멀티파트 업로드
	- Download - retrieve 후에 download가 가능하다. (retrieve 대기 시간 존재)
	- Delete - 삭제 가능
- Restore 링크는 만료 기한이 있다.
- **Retrieval Options:**
	- Expedited (1 to 5분) - $0.03 per GB and $10 per 1000 requests
	- Standard (3 to 5시간) - $0.01 per GB and $0.03 per 1000 requests
	- Bulk (5 to 12 hours) - $0.0025 per GB and $0.025 per 1000 requests
### Valut Policies & Vault Lock
- 볼트별로 access 정책과 lock 정책이 있다.
- Vault Policy는 Bucket Policy와 비슷하다.(접근 유저 제한, 계정 권한)
- Vault Lock Policy는 변경 불가능하다.
---
## Glacier Vault Lock
### Glacier - Notifications for Restore Operations
- Vault Notification 설정을 통해서 Retrieve가 끝나거나 시작됐을 때 알림을 받을 수 있다.
- 이전에 PutObject 등의 Event Notification처럼 동일하게 알림을 사용 가능하다.
### S3 Multi-Part Upload Deep Dive
- 오브젝트를 파트로 나눠서 업로드 (순서 상관 x)
- 업로드 병렬적으로 가능
- 최대 파트 개수는 10,000
- 실패: 실패한 파트만 재전송
- Lifecycle 정책을 사용하여 old 파트 삭제 자동화 가능 (오랜 기간 multipart 업로드가 되지 않으면 실패한 작업으로 보고 자동 삭제)
- AWS CLI or SDK를 사용하여 업로드
---
## Athena
### Amazon Athena
- S3에 저장된 데이터들 분석을 위한 서버리스 쿼리서비스
- 내부적으로 SQL을 사용하는 Presto Engine을 사용한다.
- CSV, JSON, ORC, Avro, Parquest 지원
- 요금은 5$ per TB
- 일반적으로 Amazon Quicksight와 같이 사용하고 Athena로 수집한 데이터를 리포팅하고 대시보드화 시킬 수 있다.
- S3 데이터 분석을 위한 서비스이지만 Federated Query 등을 사용해서 S3가 아닌 다른 리소스(AWS or 온프로미스)에서도 SQL을 사용해 검색이 가능하다.
- **시험팁: 서버리스로 S3 데이터 분석을 하고 싶을 땐, 아테나를 사용해라**
### Athena - Performance Improvement(P336)
- columnar data(열 기반 데이터)를 사용한다.
	- Apache Parquet이나 ORC 형식을 사용한다.
	- 엄청 퍼포먼스가 많이 상승한다.
	- Parquet이나 ORC로 전환하는데 Glue를 사용해라
	- 데이터를 압축해라
	- 데이터셋을 이지쿼리로 파티션해라.
	- 더 큰 파일을 사용해라.
### Amazon Athena - Federated Query
- Federated(통합 연합?) Query
- SQL을 관계형, 비관계형, 오브젝트, 커스템 데이터 소스 등에 사용할 수 있다.(AWS or on-premises)
- Athena에서 AWS 람다를 호출하고 람다에서 Federated Queries를 사용한다.
- 결과는 S3에 저장한다.
---
# 참고 자료
