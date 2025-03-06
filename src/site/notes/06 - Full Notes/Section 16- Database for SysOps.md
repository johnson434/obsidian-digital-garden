---
{"dg-publish":true,"permalink":"/06-full-notes/section-16-database-for-sys-ops/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ultimate AWS Certified SysOps Administrator Associate 2024\|Ultimate AWS Certified SysOps Administrator Associate 2024]]
---
# 단서 질문
- RDS를 사용했을 때 이점은?
	- Managed Service이므로 버전 업데이트가 가능하다.
	- MultiAZ 등 고가용성을 위한 옵션이 있다.
	- 수직적, 수평적 스케일링을 통해 확장성도 제공한다.
- RDS Autoscaling이 불리는 조건은?
	- 남은 용량이 10% 이하
	- Low Storage 상태가 5분 이상 지속
	- DB 설정을 변경한지 6시간 이상 경과
- 서비스의 트래픽 양이 유동적인 서비스가 있다. 캐시 서버를 사용할 때, 유의해야할 점은?
	- 캐시서버를 cluster mode로 설정하지 않으면 수동으로 오토스케일링이 불가능하다.
- ElastiCache의 클러스터는?
	- 난 읽기 복제본도 노드이고 Master Slave 형식의 여러 개에 노드가 있는 것도 클러스터라고 생각하는데 여기선 느낌이 다르다.
	- 샤딩을 통해 마스터의 개수가 여러 개여야 클러스터 모드다.
---
# 핵심 요약
#### **1️⃣ Amazon RDS 개요**

- AWS에서 제공하는 **Managed Database 서비스**
- 지원하는 데이터베이스 엔진: **MariaDB, MySQL, Aurora**
- **RDS vs EC2에서 직접 DB 운영 비교**
    - 자동 백업 및 **Point in Time Restore** 지원
    - MultiAZ 옵션으로 **고가용성 및 장애 복구** 지원
    - 수직 및 수평 스케일링 가능
    - **Read Replica** 기능을 통해 읽기 부하 분산 가능
    - **EBS 볼륨 사용**

#### **2️⃣ RDS 스토리지 자동 확장(Storage Autoscaling)**

- 모든 RDS 엔진이 **자동 스토리지 확장** 지원
- Autoscaling 트리거 조건:
    - 남은 공간이 **10% 이하**
    - **5분 이상 Low Storage 상태 유지**
    - DB 수정 후 **6시간 이상 경과**
- 확장 크기 결정 방식은 3가지 옵션 중에 최대값을 사용한다:
    - **10GB 증가**
    - 현재 DB 크기의 **10%**
    - 향후 **7시간 동안 예상되는 DB 사용량**

#### **3️⃣ RDS MultiAZ vs Read Replicas**

- **Read Replica**: 비동기 복제, **최대 15개 생성 가능**, **읽기 부하 분산**
- **MultiAZ**: 동기 복제, **고가용성 보장**, 단일 엔드포인트 사용
- **Read Replica는 승격 가능 (Master로 전환 가능)**
- **MultiAZ Failover 조건**: 네트워크 장애, OS 패치, DB 인스턴스 과부하 등

#### **4️⃣ RDS Proxy**

- DB **커넥션 풀 관리**로 성능 향상 및 스케일링 지원
- **IAM 인증 지원** (IAM Role을 통한 DB 접근 가능)
- **Lambda와 VPC 연동 시 유용**

#### **5️⃣ RDS 백업 및 스냅샷**

- **자동 백업**: 최대 **35일 보관**, PITR(Point In Time Recovery) 가능
- **스냅샷(Snapshot)**:
    - **수동 생성 가능, 보존 기간 무제한**
    - **MultiAZ 환경에서 Standby 인스턴스에서 스냅샷 생성 (성능 영향 없음)**
    - 암호화된 스냅샷은 **KMS 키 공유 필요**

#### **6️⃣ RDS Performance Insights & CloudWatch**

- **RDS 주요 모니터링 지표**
    - Database Connections, Swap Usage, Read/Write IOPS, Read/Write Latency
    - Disk Queue Depth, Free Storage Space 등
- **Performance Insights**:
    - **DB 부하 시각화 및 병목 탐색 가능**
    - **SQL Statement 별 성능 분석**
    - **대량의 DB 사용 주체(User, Host) 분석 가능**

#### **7️⃣ Amazon Aurora (시험 문제 多)**

- AWS에서 개발한 **고성능 DB 서비스**
- **MySQL 및 PostgreSQL과 호환 가능**
- **스토리지 자동 확장 지원 (최대 128TB)**
- **MultiAZ 지원** (3개 AZ, 6개 복제본 사용)
- **Failover 시 자동 복구 및 HA 제공**
- Aurora HA: 최소 4개 복제본 이상 살아있어야 **쓰기 가능**, 3개 이상 있어야 **읽기 가능**

#### **8️⃣ Amazon ElastiCache 개요**

- 인메모리 캐시 서비스 (**Redis / Memcached 지원**)
- **조회 성능 향상, 지연 시간 감소**
- **ElastiCache Redis vs Memcached**
    - Redis: **데이터 복제, 지속성, 트랜잭션, Lua 스크립트 지원**
    - Memcached: **단순 Key-Value 캐시, 높은 확장성 지원**

#### **9️⃣ Redis Cluster 모드 및 확장**

- **Cluster Mode Disabled**
    - **단일 샤드 (Primary + 최대 5개 Replica)**
    - **MultiAZ 활성화 기본 지원**
- **Cluster Mode Enabled**
    - **데이터 샤딩 가능 (쓰기 확장성 향상)**
    - **노드당 최대 5개 Replica**
    - **최대 500개 노드 지원**

#### **🔟 Redis 오토 스케일링**

- **Cluster Mode Enabled** 환경에서만 가능
- **자동 샤드/Replica 개수 조정 가능**
- **Target Tracking & Scheduled 정책 지원**
---
# 핵심 필기
## RDS Overview
### Amazon RDS Overview
- RDS는 AWS에서 제공하는 Managed 서비스다.[^1]
- 여러 가지 엔진을 제공한다.
	- MariaDB, MySQL, Aurora[^2]
## Advantages over using RDS versus Deploying DB on EC2 Instances
- 자동화된 백업과  Point in Time Restore[^3]를 지원
- 프로비저닝과 OS 업그레이드 자동화
- MultiAZ 옵션을 통해서 고가용성 및 DR 제공
	- DB에 장애가 발생하더라도 다른 AZ의 DB가 Active로 전환되어 서비스를 제공
- 수직적 스케일링, 수평적 스케일링이 가능하다.
- Read Replica 옵션을 통해서 읽기 전용 DB 생성 가능
- EBS를 볼륨으로 사용한다.
### [RDS - Storage Autoscaling](https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/USER_PIOPS.Autoscaling.html)
- 모든 RDS의 데이터베이스 엔진들은 자동으로 수직적 확장을 지원한다.
- 확장 가능한 임계점을 설정할 필요가 있다.
- 남은 스토리지 공간이 10% 이하면 알림을 보낸다.
- Autoscaling 트리거 조건
	- 남은 공간이 10% 이하
	- Low Storage 상태가 5분 이상 지속
	- 가장 최근에 DB를 수정한지 6시간 이상 경과
- 수직적 확장 용량은 다음 중에서 가장 큰 값을 사용합니다.
	- 10GB
	- 현재 DB 크기의 10%
	- Free Storage Space 모니터링을 통해서 향후 7시간동안 필요할 DB의 양[^4]
## RDS Multi AZ vs Read Replicas
### RDS read replicas for read scalability
- 최대 15개 읽기 복제본 생성 가능
- 비동기로 데이터 동기화가 일어난다. 따라서, 데이터가 일치하지 않는 상황이 발생한다.
- 동일한 AZ, Cross AZ, Cross Region 등 배치 전략 설정 가능
- Read Replica는 Master DB가 죽으면 Master DB로 승격이 가능하다.[^5]
- Application단에서 엔드포인트 수정이 필요하다.
	- 단, Aurora는 Cluster Endpoint랑 Reader Endpoint를 제공하여 해당 엔드포인트로 요청을 보내면 알아서 로드밸런싱해줌.
### RDS read replicas - Use Cases
- AWS에선 사용자가 10,000명 이상이면 고려하란 식으로 말함.
- Production에서 사용하는 DB가 있는데 통계와 같이 따로 작업이 필요하지만 Master DB에 영향을 주고 싶지 않을 때 => Replica가 작업을 처리하므로 Master DB는 복제본에 데이터를 동기화하는 일 이외에는 부하가 없다.
### RDS read replicas - Network Cost
- 일반적으로 AWS에서 네트워크 전송 비용은 동일 AZ에선 무료, 서로 다른 AZ에서 유료, 서로 다른 리전이면 더 비싸다.
- RDS 읽기 복제본은 동일 리전이면 네트워크 전송 비용이 무료다.[^6]
### RDS MultiAZ
- Read Replica랑 다르다.
- MultiAZ 옵션은 서로 다른 AZ에 백업 DB를 구성하는 것.
- 동기 방식으로 데이터를 복제하므로 Master DB와 Slave DB의 데이터 일관성이 높다.
- 단일 엔드포인트를 사용하여 Application의 String 수정이 필요 없다.
### RDS one zone to MultiAZ
- DB 재부팅이나 설정을 위한 다운타임이 없다.
- MultiAZ 옵션을 활성화하면 Master DB의 스냅샷을 통해서 MultiAZ를 설정한다.
### RDS MultiAZ - Failover condition
- Master DB 인스턴스
	- Failed
	- OS가 소프트웨어 패치로 인해서 다운
	- 네트워크 연결이 원할하지 않음
	- DB 인스턴스 타입 변경
	- DB에 많은 IO로 인해 바쁨
	- 용량 부족
	- AZ 다운
- `Reboot with failover`로 리부트했을 때
## RDS Proxy
### Lambda in default
![Pasted image 20250226112734.png](/img/user/image/Pasted%20image%2020250226112734.png)
- Lambda는 기본적으로 AWS Managed VPC에 생성된다.
- 따라서, 사용자 VPC 내부에 리소스들과 상호 작용이 불가능.[^7]
- Lambda가 VPC 내부 리소스들과 통신하기 위해선 subnet과 보안그룹 설정과 `AWSLambdaVPCAccessExecutionRole` 역할이 필요하다.
### Lambda in VPC
![Pasted image 20250226112946.png](/img/user/image/Pasted%20image%2020250226112946.png)
- Lambda에 subnet과 sg를 설정하면 해당 서브넷에 ENI를 생성한다.
- 해당 ENI를 통해서 Lambda와 VPC 내부 리소스들 간에 통신이 가능해진다.
### RDS Proxy
- 커넥션 풀을 관리해주는 프록시 서버로 스케일링이 가능하다.
- IAM 기반 인증을 가능하게 만든다.
## RDS Parameter Group
![Pasted image 20250226114752.png](/img/user/image/Pasted%20image%2020250226114752.png)
- RDS 생성 시에 특정 설정 값을 템플릿처럼 만들 수 있다.
- Dynamic은 리소스가 만들어지기 전에 값을 알 수 있다.
- Static은 리소스가 만들어진 후에 값을 알 수 있다.
- 반드시 알아야 하는 Param값
	- PostgreSQL / SQL Server: rds.force_ssl=1 => force SSL connections
	- MySQL / MariaDB: require_secure_transport=1 => force SSL connections
## RDS Backups and Snapshots
### RDS Backup vs snapshot
**Backup**
- 연속적인 작업으로 Maintenance windows 중에 동작한다.
- Point in time recovery가 가능하다.
- retain 기간을 설정 가능하다. (0일 ~ 35일)
**Snapshot**
- 입출력 작업을 통해서 snapshot을 찍는 인스턴스를 멈출 수 있다. (수초 수분)
- MultiAZ에선 StandBy에서 snapshot을 찍으므로 Master에 영향이 없다.
- 첫번째 스냅샷은 DB의 전체 상태를 저장하지만 이후엔 변경된 부분만 수정이 들어간다.[^8]
- 스냅샷은 복사나 공유가 가능하다.
- 메뉴얼 스냅샷은 만료 기간이 없다.
### RDS snapshot share
![Pasted image 20250226120900.png](/img/user/image/Pasted%20image%2020250226120900.png)
- Manual 스냅샷은 AWS 계정 간에 공유가 가능하지만 Automated 스냅샷은 공유가 불가능하다.
- Automated 스냅샷을 공유하기 위해선 해당 스냅샷을 먼저 복사해서 Manual 스냅샷으로 만든다.
- 공유가 가능한 스냅샷은 암호화가 되지 않았거나 custom managed key로 암호화한 스냅샷만 가능하다.
- 암호화된 스냅샷에 접근하기 위해선 해당 kms키에 접근 권한이 필요하다.
## RDS Events and Logs
### RDS Events & Event Subscriptions
![Pasted image 20250226122236.png](/img/user/image/Pasted%20image%2020250226122236.png)
- RDS는 다음 리소스와 관련된 레코드를 기록합니다.
	- DB Instances
	- Snapshots
	- Parameter groups, security groups ...
- 예: DB 상태가 `Pending`에서 `Running`으로 변경
- RDS 이벤트 구독
	- RDS 이벤트를 구독하여 SNS로 알림
	- 이벤트 소스(instance, SGs 등)와 이벤트 종류(creation, failover 등)를 설정
- RDS Events는 EventBridge로 이벤트를 전송한다.
## RDS & CloudWatch
### RDS Database Log Files
![Pasted image 20250226122422.png](/img/user/image/Pasted%20image%2020250226122422.png)
- 시험 문제는 매트릭스를 기반으로 퍼포먼스 예측이나 트러블 슈팅이 나옴
### RDS with CloudWatch
- **CloudWatch 매트릭도 RDS와 함께 동작한다. (하이퍼바이저에서 수집된다.[^9])**
	- Database Connections: Database의 커넥션의 개수를 의미한다. 너무 많은 경우엔 커넥션 풀링을 통해 적절하게 개수를 조절할 필요가 있다.
	- Swap Usage: 메모리가 부족하여 Swap이 발생하는 횟수를 의미한다. 값이 너무 높다면 메모리양을 늘리는 것을 고려해보든가 쿼링을 개선
	- ReadIOPS / WriteIOPS: 초당 처리한 입출력 횟수다. 디스크 퍼포먼스를 체크 가능하다. 너무 높다면 IOPS 연산이 많으므로 좋다고 볼 수도 있지만, ReadLatency가 높다면 연산이 많고 해당 작업을 처리하지 못하여 연기된 작업들이 많다는 것이다.
	- ReadLatency / WriteLatency: 조회 시에 발생하는 Latency를 의미한다. 느리다면 쿼링을 조사할 필요가 있다.
	- ReadThroughPut / WriteThroughPut: 초당 전송되는 데이터 양으로 여러 매트릭과 같이 판단이 필요하다. ReadLatency나 ReadIOPS가 높다면 연산 횟수는 많고, 처리 속도도 느리다. 이런 상태에서 Throughput이 낮다면 한 번의 쿼리로 발생하는 데이터의 양이 너무 적을 가능성도 있다. 예를 들어, 한번에 호출로 충분한데 불필요하게 쿼리를 나눠서 호출한다거나
	- DiskQueueDepth: 대기 중인 작업이 얼마나 있는지. 
	- FreeStorageSpace: 남아있는 공간
- Enhanced Monitoring (agent로부터 수집된다.)
	- 프로세스와 스레드 별 CPU 사용량을 알고 싶을 때
	- 50개 이상의 매트릭에 접근 가능하다. (CPU, memory, file system )
## RDS Performance Insights
![Pasted image 20250226130604.png](/img/user/image/Pasted%20image%2020250226130604.png)
- 데이터베이스 퍼포먼스를 시각화하고 이슈를 분석 가능하다.
- Performance Insights로 데이터베이스 부하를 시각화하고 특정 부하만 필터링 가능하다.
	- By Waits => 보틀넥 리소스를 찾는다. (CPU, IO, lock, etc...)
	- By SQL Statements => 문제인 SQL 쿼리를 찾는다.
	- By Hosts => DB를 가장 많이 쓰는 서버 찾기
	- By Users => DB를 가장 많이 쓰는 유저 찾기
- DBLoad = DB Engine에서 활성화된 세션 개수

## **Amazon Aurora (시험문제 많이 나오는 중)**
- AWS에서 개발한 거라 오픈 소스가 아니다.
- Postgres랑 MySQL 둘 다 지원한다.
- 일반 RDS보다 성능이 더 좋다. (이건 어느정도 걸러들을 필요가 있다. 대부분의 DBMS 벤치마크는 다 자기들이 최고로 빠르다고 주장한다.)
- 스토리지 아웃스케일링을 주장하며 최대 128TB까지 증가 가능하다.
- 최대 15개 레플리카 생성이 가능하며 MySQL보다 복제 프로세스가 빠르다.
- Failover가 동시에 일어난다. 자체적으로 HA 지원
- RDS보다 20% 비싸다.
### Aurora HA and Read Scaling
- 3AZ에 6개 복제본이 있다.
	- writes 작업을 하기 위해선 최소 4개 이상의 copies들이 살아있어야 한다.
	- reads 작업을 하기 위해선 최소 3개 이상의 copies들이 살아있어야 한다.[^10]
	- `peer-to-peer`로 셀프힐링이 가능하다.
	- 100개 이상의 볼륨에 스트라이프 형태로 저장된다.
- Master 인스턴스와 최대 15개 Read Replicas가 조회를 지원한다.
- 크로스 리전 Replication도 지원한다.

- 이해 안가서 내가 정리한 내용
	- Aurora는 높은 가용성을 위해 data 복제본을 3개의 AZ에 총 6개 복제본을 생성한다.
	- 해당 복제본 중에 4개 이상이 살아있어야 쓰기 작업이 가능하다. 
	- 해당 복제본 중에 3개 이상이 살아있어야 쓰기 작업이 가능하다.
	- 각 복제본에 문제가 생겨도 다른 복제본으로부터 복구가 가능하다.(peer-to-peer)
	- RAID처럼 일자로 100개 이상의 볼륨에 데이터를 저장한다. (데이터 손실을 최소화하려고 그런 듯)
	- 애초에 read replica는 매 요청을 데이터의 origin으로 전달하지 않고 replica 자체가 가진 데이터를 응답함. 따라서, read replica에 요청이 많이 와도 Primary(Master) DB에 영향을 끼치지 않는 것임.
### Aurora DB Cluster
![Pasted image 20250226141134.png](/img/user/image/Pasted%20image%2020250226141134.png)
- **Reader Endpooint (시험 가능성 높음)**: Reader Endpoint로 요청하면 Read Replica로 로드밸런싱이 일어난다. Statement 레벨이 아니라 Connection 레벨에서 일어난다.[^11]
### Features of Aurora
- 자동화된 failover
- 백업과 리커버리
- 고립성과 보안
- 산업 표준
- Scaling을 직접 트리거 가능
- 자동화된 패치와 제로 다운타임
- 개선된 모니터링
- 보수 루틴(Maintenance Routine)
- 백트랙: 어떤 지점으로도 백업이 가능하다.
## Amazon Aurora - Backups
### Backups, Backtracking & Restores in Aurora
- 백업 자동화
	- 데이터 보유 기간 1-35일 (비활성화 불가능)
	- PITR[^12]으로 DB를 복구가 가능하다. 시간은 5분 전부터 가능하다.[^13]
	- 새로운 DB 클러스터에서 데이터 복구를 진행한다.
- Aurora Backtracking
	- DB를 과거 또는 현재로 최대 72시간까지 되돌릴 수 있다.
	- 새로운 DB 클러스터를 만들지 않고 제자리에서 복구가 가능하다. (제자리 복구)
	- Aurora MySQL만 지원한다.
- Aurora, Database Cloning
	- 원본 클러스터와 동일한 볼륨을 사용하는 새로운 DB 클러스터를 만든다.
	- copy-on-write 프로토콜을 사용한다. (원본/복사본 데이터를 사용하다가 데이터가 변경됐을 때만 수정된 부분에 데이터를 저장한다.)
	- 사용 예: production 데이터로 테스트 환경 만들기
## **Amazon Aurora for SysOps**
- 각 Read Replica에 우선도 순위[^14]를 0에서 15로 설정할 수 있다. 다음에 설정된다.
	- Failover 우선도를 조정한다.
	- 가장 우선도가 높은[^15] Read Replica를 승격(Promote)한다.
	- 우선도가 같다면, 가장 사이즈가 큰 replica를 승격한다.
	- 사이즈도 같다면 임의의 데이터를 승격한다.
- RDS MySQL 스냅샷을 Aurora MySQL Cluster로 이전할 수 있다.
## Aurora: CloudWatch metrics
- AuroraReplicaLag: Primary 인스턴스로부터 replica 인스턴스에 데이터를 업데이트하는데 걸리는 시간
- AuroraReplicaLagMaximum: 최대값. 클러스터 내의 모든 Replica 중에서 가장 늦게 복제된 Replica의 Lag
- AuroraReplicaLagMinimum: 최소값. 클러스터 내의 모든 Replica 중에서 가장 빨리 복제된 Replica의 Lag
- DatabaseConnections: 현재 연결된 커넥션 개수
- InsertLatency: Insert 작업 평균 지속 시간
## RDS & Aurora Security
- At-rest 암호화:
	- KMS를 통해서 암호화 (무조건 DB launch time에 정의되어야한다.)
	- master가 암호화되지 않으면 replica도 암호화가 불가능하다.
	- 암호화되지 않은 DB를 암호화하기 위해선 snapshot을 만들고 암호화 설정해서 복구하는 방법밖에 없다.
- In-flight 암호화: 
	- TLS는 디폴트 옵션. AWS TLS 루트 인증서를 클라이언트 쪽에서 사용하세요.
- IAM Authentication:
	- DB에 연결하기 위한 IAM role(username/password 대신에 사용 가능)
- Security Groups: 보안그룹
- No SSH Available: RDS 커스텀 설정에선 ssh 설정도 가능
- Audit Log 활성화 가능. 해당 로그는 CloudWatch로 전송됨.
## ElasticCache Overview
### Amazon ElasticCache Overview
- Redis나 Memcached나 발키리 같은 애들을 사용
- Caches는 인메모리 DB로 높은 성능과 낮은 지연성을 가진다.
- 조회 관련 부하를 줄여준다.
- AWS에서 OS 유지 보수를 진행한다.
## ElastiCache - Redis vs Memcached
![Pasted image 20250306103848.png](/img/user/Pasted%20image%2020250306103848.png)
## ElasticCache Redis Cluster Modes
### ElastiCache Replication: Cluster Mode Disabled
![Pasted image 20250306104352.png](/img/user/Pasted%20image%2020250306104352.png)
- primary node 1개에 최대 5개 replicas
- 비동기 복제
- primary node는 read/write에 사용
- replicas는 read-only
- 하나의 샤드를 사용하므로 모든 노드가 동일한 데이터를 가진다.[^16]
- 노드가 실패했을 때 데이터 유실을 막는다.
- MultiAZ가 기본적으로 활성화된 상태다.
- 확장과 조회 성능에 도움이 된다.
### Redis Scaling - Cluster Mode Disabled
![Pasted image 20250306104527.png](/img/user/Pasted%20image%2020250306104527.png)
- 수평적
	- read replicas 개수 조절
- 수직적
	- 노드 타입 변경
	- 제자리에서 업데이트하지 않고, 해당 노드 그룹의 복제본을 만든 후에 DNS 업데이트를 진행한다.
### ElastiCache Replication: Cluster Mode Enabled
![Pasted image 20250306104606.png](/img/user/Pasted%20image%2020250306104606.png)
- 샤드를 통해 데이터가 파티셔닝(쓰기 확장성이 좋다.)
- 각 샤드는 primary 노드와 최대 5개에 replicas를 가진다.
- Multi-AZ 가능

- 클러스터 당 최대 500개 노드 사용 가능하다.
	- 500개 샤드 1primary
	- 250개 샤드 1primary 1replica
	- 100개 샤드 1primary 4replica
	- ...
### ElastiCache for Redis - Auto Scaling
- 샤드나 replicas 조절
- Target Tracking이나 Scheduled 정책 둘 다 지원
- Cluster Mode가 활성화된 Redis만 가능하다.
![Pasted image 20250306105357.png](/img/user/Pasted%20image%2020250306105357.png)
## ElastiCache - Redis Connection Endpoints
![Pasted image 20250306111828.png](/img/user/Pasted%20image%2020250306111828.png)
- Standalone Node
	- 단 하나의 엔드포인트에 read/write 모두 작업
- Cluster Mode Disabled Cluster
	- Primary Endpoint - 모든 쓰기 작업
	- Reader Endpoint - 읽기 작업 로드밸런싱
	- Node Endpoint - 읽기 작업
- Cluster Mode Enabled Cluster
	- Configuration Endpoint - Cluster Mode 활성화 커맨드를 지원하는 모든 읽기/쓰기 작업
	- Node Endpoint - 읽기 작업
### Redis Scaling - Cluster Mode Disabled
- Cluster 모드가 활성화된 AutoScaling을 지원하지만, 단순 스케일링은 Cluster 모드가 지원되지 않아도 괜찮다.
- 수평적:
	- read replicas 증감
- 수직적:
	- 노드 타입 변경
	- 오토스케일링 방식과 동일하게 복제본을 만들고 DNS를 업데이트한다.
![Pasted image 20250306112138.png](/img/user/Pasted%20image%2020250306112138.png)
### Redis Scaling - Cluster Mode Enalbed
- 클러스터 모드가 활성화된 상태에서 레디스 클러스터를 스케일링하는 방법은 두 가지가 있다.
	- Online Scaling: 스케일링 과정에도 요청을 처리한다. (다운타임이 없다, 대신 스케일링 과정에서 성능이 준다)
	- Offline Scaling: 스케일링 과정에 요청을 처리하지 못한다. (backup and restore, 노드 타입 변경, 엔진 버전 업데이트 등의 작업)[^17]
- 수평적: (리샤딩, 샤딩 리밸런싱)
	- Resharding: 샤딩을 추가/삭제하면서 확장과 축소한다.
	- Shard Rebalancing: 키공간을 동일하게 샤드에 나눈다.[^18]
	- 온라인/오프라인 스케일링 모두 지원한다.
- 수직적: (read/write capacity 변경)
	- 노드 타입 벼경
	- Online Scaling을 지원한다.
### Redis Metrics to Monitor
- Evictions: expired되지 않았지만 새로운 쓰기 작업을 위해 방출된 item의 개수(캐시 메모리가 가득 찼을 때, 쓰기 작업이 들어오거나 Expire 정책을 사용하면 방출된다.)[^19]
	- 해결 방법:
		- 방출 정책을 지정한다.
		- 더 큰 노드 타입으로 변경한다.
- CPUUtilization: 전체 호스트의 CPU Utilization
	- 해결 방법: 스케일링
- SwapUsage: 50MB를 넘으면 안된다.
	- 해결 방법: 충분한 예약 메모리가 있는지 확인한다.
- CurrConnections: 동시 활성화된 커넥션 개수
	- 해결 방법: application측에서 확인할 필요가 있다.
- DatabaseMemoryUsagePercentage: 메모리 사용량 퍼센트
- NetworkBytesIn/Out & NetworkPacketsIn/Out
- ReplicationBytes: 복제된 데이터의 볼륨
- ReplicationLag: replica가 primary node로부터 얼마나 뒤에 있는지
## Memcached Scaling
- Memcached 클러스터는 1-40개 노드까지 사용 가능하다.(제한이 비교적 널널하다)
- 수평적:
	- 클러스터에서 노드를 추가하거나 삭제
	- Auto-discovery로 application이 node들을 찾을 수 있다.
- 수직적:
	- 노드 타입 변경
	- 스케일업 절차:
		- 새로운 클러스터를 만든다.
		- 어플리케이션에서 새로운 엔드포인트를 설정해야함(Redis가 DNS 업데이트로 동일한 엔드포인트에 요청해도 문제가 없는 거랑 다르다.)
		- 기존 클러스터를 삭제한다.
	- Memcached 클러스터와 노드는 데이터가 비어있는 상태로 만들어진다.
### Memcached Auto Discovery
- 원래는 각 서버의 DNS의 엔드포인트에 연결해야한다.
- Auto Discovery는 자동으로 각 memcached 노드들의 주소를 찾을 수 있다.
- 모든 Cache 노드들은 cluster의 메타데이터를 가지고 있습니다. 여기엔 다른 노드들의 정보도 있습니다.
- 따라서, 클라이언트 입장에선 seamless합니다.[^20]
---
# 주석

[^1]: RDBMS(Relational Database Management System)은 관계형 DB라는 개념을 말하고 RDS는 AWS의 서비스를 의미한다.

[^2]: AWS에서 만든 엔진. 읽기 전용 복제본 생성으로 오토스케일링이 가능하다.

[^3]: 특정 시간대로 데이터를 복원한다.

[^4]: 이건 내 생각인데, 6시간이 지나야 수직적 확장이 가능하므로 7시간동안 버틸만큼의 여분의 DB 스토리지를 예측해서 설정하는 느낌이다.
	만약에 6시간이 지나지 않았는데 DB가 꽉찬다면 Autoscaling이 동작하지 않으니까 이를 예방하기 위한 방법같다.

[^5]: Active-Active 전략으로 현재 실행 중인 DB를 Master로 승격하는 전략. MultiAZ 옵션은 Active-StandBy으로 대기 중인 DB를 승격하는 방법과 달리 현재 실행 중인 DB를 승격하므로 Active-StandBy보다 빠르다. Active-Active 전략이 Active-StandBy보다 빠르다곤 했지만 실제로 큰 차이는 없음.

[^6]: EC2와 비교하자면 EC2는 동일한 AZ 내에서만 네트워크 비용이 무료다.

[^7]: 단, VPC 내부에 리소스들이 IGW를 통해서 외부 인터넷과 연결이 가능하다면 Lambda 또한, 인터넷을 통해서 접근 가능하다.

[^8]: Snapshots are incremental after the first snapshot (which is full)

[^9]: 하이퍼바이저는 bare metal인 type1과 Hosted인 type2로 나뉘는데 type2는 vmware 등과 같은 소프트웨어고 RDS는 type1이다. 아무래도 운영체제 없이 바로 하드웨어에 하이퍼바이저를 올리는 게 더 가볍기 때문에 그렇게 선택한듯

[^10]: 헷갈리는 부분인데 처음부터 설명하자면 Aurora는 기본적으로 6개의 copy(읽기 전용 replication과는 다르다.)를 3개의 AZ에 가지고 있는다.
	Master DB

[^11]: 즉, 연결할 때 로드밸런싱이 일어나고 연결이 이뤄진 이후엔 로드밸런싱이 일어나지 않는다. 예를 들어, 서버 A가 커넥션을 이룬 상태에서 SQL문을 5개 실행시켜도 동일한 DB로 요청이 간다. 왜냐? 연결 과정에서 로드밸런싱을 통해 해당 DB에 연결이 됐고 이후엔 연결을 유지하며 해당 DB로 statement를 실행할 뿐이니까

[^12]: Point In Time Recovery: 특정 시간대로 백업

[^13]: 현재 시각이 12시라면 11시 55분 시점까지 복구 가능

[^14]: priority tier

[^15]: tier값이 낮을수록 우선도가 높다. 0이 최고 우선도다.

[^16]: 아까 말한 Memcached는 샤딩을 해싱키로 이용하여 서로 나눠서 저장한다. 안정해시 알고리즘 등 해싱 전략도 필요하다.

[^17]: cluster mode가 disabled된 standalone 형식은 무조건 offline scaling밖에 쓰지 못한다.

[^18]: 이전에 공부한 안정해시 알고리즘에 이어지는 개념이다. 샤딩은 안정해시 링에서 시계방향으로 돌면서 가장 인접한 노드에 저장한다. 이로 인해 데이터의 균등한 분배가 가능하다.

[^19]: 이 값이 너무 크면 올바른 캐시 정책을 사용하지 않거나, 효율적으로 캐싱을 사용하지 않는 것이므로 해결할 필요가 있지만.
	어떤 정책을 사용하더라도 Evictions를 0으로 만드는 건 거의 불가능하다. 만약, Evictions가 항상 0이라면 과도하게 메모리 크기를 크게 잡았을 확률이 높다고 나는 생각한다.

[^20]: Client는 엔드포인트로 요청하면 내부에서 알아서 Auto Discovery하니까. seamless하다.
