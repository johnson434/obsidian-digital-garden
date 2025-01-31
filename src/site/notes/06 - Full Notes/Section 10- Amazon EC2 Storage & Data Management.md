---
{"dg-publish":true,"permalink":"/06-full-notes/section-10-amazon-ec-2-storage-and-data-management/","noteIcon":""}
---

# Tags
[[Ultimate AWS Certified SysOps Administrator Associate 2024 \|Ultimate AWS Certified SysOps Administrator Associate 2024 ]]

---
# 단서 질문
- 인스턴스 부팅 볼륨으로 사용 가능한 볼륨은 뭐가 있을까?
    - gp2/gp3
    - io1/io2
- EBS 볼륨 사이즈 조절은 가능한가?
    - 크기를 늘릴 수만 있다.
- EBS Volume의 IOPS를 조절할 수 있는가?
	- io1은 가능하다.
- EBS Volume gp2와 gp3의 차이점은?
    - gp3는 IOPS와 throughput 스펙을 별개로 설정할 수 있다.
- EBS S3에 저장된 snapshot을 빨리 사용하기 위해선?
	- S3 블록에 처음 접근은 지연이 발생한다. 따라서, dd 명령어나 fio 명령어로 인위적으로 block을 활성화하거나 FSR 옵션을 활성화한다. 단, FSR은 비쌈(분당 요금)
- EFS vs EBS vs Instance Store
    - EFS와 EBS 모두 네트워크 드라이브 반면에, Instance Store은 실제로 물리적 하드디스크를 대여
    - EFS는 여러 AZ에서 동시에 사용 가능
---
# 핵심 요약
- Amazon EC2 Storage와 Data Management에 대한 주요 내용:
- EBS (Elastic Block Store)
	- EC2 인스턴스용 네트워크 드라이브로, 한 번에 하나의 인스턴스에 연결 가능 (io1/io2 제외)
	- 특정 AZ에 종속되며, 스냅샷을 통해 백업 가능
	- gp2/gp3, io1/io2, st1, sc1 등 다양한 볼륨 유형 제공
- EFS (Elastic File System)
	- 여러 EC2 인스턴스에 동시 마운트 가능한 관리형 NFS
	- 멀티 AZ 지원, 높은 가용성과 확장성 제공
	- 다양한 성능 모드와 처리량 모드 지원
	- 스토리지 클래스를 통해 비용 최적화 가능
- EBS와 EFS의 주요 차이점
	- EBS는 단일 인스턴스, EFS는 여러 인스턴스에 동시 연결 가능
	- EBS는 AZ 종속적, EFS는 리전 범위로 사용 가능
	- EFS는 Linux 인스턴스만 지원 (POSIX 파일 시스템)
- 핵심 개념:
	- EBS: EC2 인스턴스용 네트워크 드라이브, 주로 단일 인스턴스에 연결
	- EFS: 여러 EC2 인스턴스에 동시 마운트 가능한 관리형 NFS
	- 성능과 확장성: EFS는 높은 확장성과 다양한 성능 모드 제공
	- 비용 최적화: EFS가 더 비용이 높지만 스토리지 클래스와 라이프사이클 관리를 통해 비용 절감 가능
	- 가용성: EBS는 AZ 종속적, EFS는 멀티 AZ 지원으로 높은 가용성 제공
	- 사용 사례: EBS는 부팅 볼륨에 적합, EFS는 대규모 파일 공유와 데이터 처리에 적합
---
# 핵심 필기

## EBS Overview
### What’s an EBS Volume
### What's an EBS Volume?
- EBS(Elastic Block Strore)는 인스턴스에 붙일 수 있는 네트워크 드라이브다.
- 인스턴스가 제거되더라도 EBS 내용은 유지된다.
- (CCP 레벨에선)한 번에 하나의 인스턴스에만 `Attached`된다.
- 특정 AZ에 종속된다.
### EBS Volume
- 네트워크 드라이브다. (물리적 드라이브가 아니다)
	- 인스턴스와 네트워킹을 통해 연결된다. → 약간의 Latency가 있다. (인스턴스와 EBS가 동일한 AZ에 있으므로 Latency는 아주 작다.)
- 특정 AZ 존에 고정되어 있다.
	- 볼륨을 옮기려면 스냅샷을 찍고 새로 만들어야 한다.
- 프로비저닝된 Capacity와 IOPS가 있다.
	- 프로비전된 Capacity만큼 요금이 부과된다.
	- 드라이버의 Capacity는 증가 시킬 수 있따.
### EC2 Instance Store(임시 저장소)
- EBS Volume은 네트워크 드라이브로 괜찮은 성능이지만 한계가 있다. 
- EC2 Instance Store를 사용하여 높은 성능의 **하드웨어 디스크**를 사용 가능하다.
- I/O 성능이 더 좋다.
- EC2 Instance Store가 멈추면 데이터를 잃는다. (임시데이터)
- 버퍼, 캐시, 데이터 스크래치, 임시 컨텐츠에 좋다.
- 하드웨어가 실패하면 데이터를 잃을 수 있다.
- 백업이랑 Replication은 당신의 책임이다.
- EC2 m5d type 설정 화면![Pasted image 20250118163535.png](/img/user/image/Pasted%20image%2020250118163535.png)
- 지금까지 EC2 실행하면 자동으로 EBS가 붙고, EC2를 삭제하면 EBS가 사라지길래 이게 EC2 Instance Store인줄 알았다. 그런데 아니다. 
- EC2 삭제 시에 EBS 볼륨이 사라지는 이유는 EC2 생성 시에 Default 옵션으로 `Delete on Termination` 옵션이 걸려서다.
- EC2 Instance Store는 지원하는 Instance Type이 있어서(m5d 등) 해당하는 인스턴스만 사용 가능하다.
---
## EBS Volume Types Deep Dive
### EBS Volume Types
- 부팅 볼륨으로는 gp2/gp3와 io1/io2만 사용 가능하다.
### EBS Volume Types Use cases General Purpose SSD
- 낮은 Latency, 비용 효율적 스토리지
- 가상 데스크탑, 개발 & 테스트 환경용
- 1GiB - 16TiB
- gp3:
	- IOPS: 3,000 - 16,000
	- Throughput: 125MiB/s - 1,00MiB/s
	- IOPS와 별개로 대역폭을 설정 가능하다.
- gp2:
	- IOPS와 Throughput 별개로 설정 불가능함.
	- 확인해보니 gp3는 IOPS랑 throughput에 추가 비용이 붙지만 3,000IOPS랑 125MiB/s까진 무료임. 
	- 공통으로 붙는 요금은 Capacity 비용인데 gp3가 gp2보다 저렴하다.
	- **따라서, gp3 대신에 gp2 쓸 이유가 없다.**
### EBS Volume Types Use cases Provisioned IOPS(PIOPS) SSD
- iops 퍼포먼스가 중요한 앱에서 사용한다.
- 16,000 IOPS 이상을 요구하는 앱에서 사용한다.
- 데이터베이스 작업에 좋다. (차라리 RDS 쓰는 게 낫다고 생각한다.)
- io1 (4GiB - 16 TiB):
	- Max PIOPS: 64,000 for Nitro EC2 Instances & 32,000 for other
	- PIOPS랑 Storage 크기는 독립적으로 설정 가능
- io2 Block Express (4 GiB - 64 TiB):
	- 1ms보다 Latency가 적다. (Sub-millisecond latency)
	- Max PIOPS: 256,000 with an IOPS:GiB ratio of 1,000:1
- 멀티 attach 가능
### EBS Volume Types Use cases Hard Disk Drives HDD (e.g. st1, sc1)
- 부트 볼륨으로 사용 불가능
- 처리량 최적화 HDD (st1)
	- 빅데이터, 데이터웨어하우스, 로그 작업
	- 최대 처리량은 500 MiB/s - max IOPS 500 => 입출력이 500개고 개당 크기가 1MB면 최대 처리량에 도달한다.
- Cold HDD (sc1)
	- 드물게 접근되는 데이터들을 저장
	- 비용이 제일 적은 시나리오에서 사용
	- 최대 처리량은 250 MiB/s - max IOPS 250
### EBS - Volume Types Summary
- https://docs.aws.amazon.com/ko_kr/ebs/latest/userguide/ebs-volume-types.html

|    [  <br>Amazon EBS 범용 SSD 볼륨](https://docs.aws.amazon.com/ko_kr/ebs/latest/userguide/general-purpose.html)    |                                                                                                                                                  | [Amazon EBS 프로비저닝 볼륨 IOPS SSD](https://docs.aws.amazon.com/ko_kr/ebs/latest/userguide/provisioned-iops.html) |                                                                                                               |                                                                              |
| :-------------------------------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------------------------------: | :-----------------------------------------------------------------------------------------------------------: | ---------------------------------------------------------------------------- |
|                                                    **볼륨 유형**                                                    |                                                                      `gp3`                                                                       |                                                    `gp2`                                                     |                                             `io2` Block Express 3                                             | `io1`                                                                        |
|                                                     **내구성**                                                     |                                                        99.8%~99.9% 내구성(연간 장애율 0.1%~0.2%)                                                         |                                                                                                              |                                          99.999% 내구성(연간 장애율 0.001%)                                           | 99.8%~99.9% 내구성(연간 장애율 0.1%~0.2%)                                            |
|                                                    **사용 사례**                                                    | - 트랜잭션 워크로드<br>    <br>- 가상 데스크톱<br>    <br>- 중간 규모의 단일 인스턴스 데이터베이스<br>    <br>- 짧은 대기 시간 대화형 애플리케이션<br>    <br>- 부트 볼륨<br>    <br>- 개발 및 테스트 환경 |                                                                                                              | 다음이 필요한 워크로드:<br><br>- 밀리초 미만의 지연 시간<br>    <br>- 지속적인 IOPS 성능<br>    <br>- 64,000 IOPS 또는 1,000MiB/s 이상의 처리량 | - 지속적인 IOPS 성능이 필요하거나 16,000개 이상의 워크로드 IOPS<br>    <br>- I/O 집약적 데이터베이스 워크로드 |
|                                                    **볼륨 크기**                                                    |                                                                   1GiB - 16TiB                                                                   |                                                                                                              |                                                 4GiB~64TiB 4                                                  | 4GiB - 16TiB                                                                 |
|                                                   **최대 IOPS**                                                   |                                                               16,000(64KiB I/O 6)                                                                |                                             16,000(16KiB I/O 6)                                              |                                            256,000 5(16KiB I/O 6)                                             | 64,000(16KiB I/O 6)                                                          |
|                                                   **최대 처리량**                                                    |                                                                    1,000MiB/s                                                                    |                                                  250MiB/s 1                                                  |                                                  4,000MiB/s                                                   | 1,000MiB/s 2                                                                 |
|                                              **Amazon EBS 다중 연결**                                               |                                                                     지원되지 않음                                                                      |                                                                                                              |                                                      지원                                                       |                                                                              |
|                                                   **NVMe 예약**                                                   |                                                                     지원되지 않음                                                                      |                                                                                                              |                                                      지원                                                       | 지원되지 않음                                                                      |
|                                                    **부트 볼륨**                                                    |                                                                        지원                                                                        |                                                                                                              |                                                                                                               |                                                                              |
| [  <br>처리량 최적화 HDD 볼륨](https://docs.aws.amazon.com/ko_kr/ebs/latest/userguide/hdd-vols.html#EBSVolumeTypes_st1) |                       [콜드 HDD 볼륨](https://docs.aws.amazon.com/ko_kr/ebs/latest/userguide/hdd-vols.html#EBSVolumeTypes_sc1)                       |                                                                                                              |                                                                                                               |                                                                              |

|         **볼륨 유형**          |                       `st1`                       |                                  `sc1`                                   |     |     |
| :------------------------: | :-----------------------------------------------: | :----------------------------------------------------------------------: | :-: | --- |
|          **내구성**           |         99.8%~99.9% 내구성(연간 장애율 0.1%~0.2%)         |                                                                          |     |     |
|         **사용 사례**          | - 빅 데이터<br>    <br>- 데이터 웨어하우스<br>    <br>- 로그 처리 | - 자주 액세스하지 않는 데이터를 위한 처리량 중심의 스토리지<br>    <br>- 스토리지 비용이 최대한 낮아야 하는 시나리오 |     |     |
|         **볼륨 크기**          |                  125GiB ~ 16TiB                   |                                                                          |     |     |
| **볼륨IOPS당 최대** 수(1MiB I/O) |                        500                        |                                   250                                    |     |     |
|       **볼륨당 최대 처리량**       |                     500MiB/s                      |                                 250MiB/s                                 |     |     |
|    **Amazon EBS 다중 연결**    |                      지원되지 않음                      |                                                                          |     |     |
|         **부트 볼륨**          |                      지원되지 않음                      |                                                                          |     |     |

### EBS Multi Attach
- 동일한 EBS Volume을 여러 인스턴스에 부착(Attatch)하는 것.
- 사용 예시:
	- 고가용성 클러스터 리눅스 어플리케이션
	- 동시에 쓰기가 필요한 어플리케이션
- 최대 16개 인스턴스에 붙일 수 있다.
- cluster-aware 파일시스템을 써야한다. (XFS, EXT4, etc... 등은 안된다.)
### EBS Operation: Volume Resizing
- 모든 EBS Volume은 Size를 생성 후에 증가시킬 수 있다.
- 단, IOPS는 IO1만 가능하다.
- 사이즈를 늘린 후에는 리파티션이 필요하다.
- 리사이즈 후에는 Volume은 “optimisation” 상태에서 시간이 걸릴 수 있다. 단, 볼륨은 "optimisation" 상태에도 사용이 가능하다.
- 볼륨 크기를 줄이기 위해서는 더 작은 볼륨을 만들고 데이터를 이전해야 한다.

---
## EBS Operation: Snapshots
### EBS Snapshots
- Volume을 Detach 하지 않고도 snapshot을 찍을 수 있다.
- 단, `detach`하고 스냅샷 찍는 것을 **권장**한다.

---
## Amazon Data Lifecycle Manager
### Amazon Data Lifecycle Manager
- EBS snapshot과 EBS-backed AMI 제어를 라이프사이클에 따라 자동화한다.
- 백업, 다른 계정으로부터 스냅샷 복제, 기한 지난 백업을 삭제 등을 **스케쥴링**한다.
- DLM(Data Lifecycle Manager) 외부에서 만들어진 snapshot과 AMI는 관리하지 못한다.
- EC2 Instance-Store backed AMI는 관리하지 못한다.

---
## Fast Snapshot Restore (FSR)
### Fast Snapshot Restore (Fast Snapshot Restore)
- S3에 저장된 스냅샷들은 입출력 작업을 처음 할 때(각 블록에 처음 접근할 때) latency가 발생합니다. (Block을 S3로부터 Pull해야 하므로)
- 해결책: 
	1. dd나 fio 명령어로 init하거나 FSR 옵션을 킨다.
		- dd와 fio는 볼륨 성능을 측정하는 커맨드로 실행하면 전체 볼륨에 한번씩 접근이 일어난다.
	2. **FSR 옵션을 활성화**한다.
- FSR은 생성할 때 초기 생성된 스냅샷으로부터 볼륨을 만들 때 도와줍니다. (no I/O latency)
- 특정 AZ에 있는 snapshot에서 사용 가능합니다. (분당 요금을 청구하며 굉장히 비쌉니다.)
	- snapshot 자체는 특정 Region에 저장되며, FSR 활성화도 Region에서 진행하지만 FSR 활성화는 특정 AZ에서 진행됩니다.
	- 왜냐하면 실제로 사용할 저장장치를 초기화 해놓아야 하는데 이는 실제 기기를 사용하므로 특정 AZ에 종속 되는 것입니다. (만약, Region이 범위가 되려면 해당 Region 전체에 초기화된 볼륨이 있어야 하므로 비용이 말도 안되게 비쌀 거다.)
- DLM을 통해 만들어진 snapshot에서 FSR을 활성화할 수 있습니다.
### EBS Snapshots Features
- **EBS Snapshot Archive**
	- Archive Tier는 일반 Snapshot보다 75% 저렴하다.
	- 아카이브 된 스냅샷을 되찾는데(restoring)는 24~72시간이 걸린다.
- **Recycle Bin for EBS Snapshots**
	- 규칙을 설정하여 삭제해도 일정 기간동안 스냅샷이 Recycle Bin에 남는다.
	- 만료 기간을 설정 가능하다. (1일~1년)
---
## EBS Operation: Volume Migration
### EBS Volume Migration
- EBS Volume은 특정 AZ에 한정된다.
- 다른 AZ(혹은 다른 Region)으로 옮기기 위해선 다음과 같은 절차를 밟아야한다.
	1. Volume의 스냅샷을 찍는다.
	2. (선택 사항) Volume을 다른 리전으로 복사한다. (Volume은 기본적으로 리전에 종속되므로 다른 리전에서 사용하기 위해선 복사할 필요가 있다.)
	3. 원하는 AZ에서 스냅샷을 통해서 Volume을 만든다.
---
## EBS Operation: Volume Encryption
### EBS Encryption
- 암호화된 EBS Volume을 만들면 다음과 같은 내용이 따라온다.
	- Volume 내에 데이터는 암호화된다.
	- 인스턴스와 볼륨이 데이터를 주고 받을 때 데이터들은 암호화된다.
	- 모든 스냅샷은 암호화된다.
	- 모든 볼륨은 스냅샷을 통해 만들어진다.
- 암호화와 복호화는 자동으로 핸들링된다. (사용자가 설정할 필요x)
- 암호화는 지연성의 최소한의 영향만 끼친다.
- EBS Encryption은 KMS 키를 통해 진행된다.
- 암호화되지 않은 스냅샷을 암호화할 수 있다.
- 암호화된 볼륨의 스냅샷도 암호화된다.
### Encryption: encrypt an unencrypted EBS Volume
- 스냅샷을 만든다.
- 스냅샷의 복사본을 만든다. (암호화 옵션을 키고)
- 스냅샷으로부터 볼륨을 만든다.
- 볼륨을 인스턴스에 붙인다.
- 쨘! 암호화된 EBS Volume을 사용하는 인스턴스가 된다.
- 쇼트컷으로는 snapshot에서 create volume from snapshot으로 volume을 만들 때 암호화 옵션만 걸면 된다.

---
## Amazon EFS
### Amazon EFS (Elastic File System)
- **관리형 NFS (Network File System)**로 여러 EC2 인스턴스에 동시에 마운트 가능하다.
- 서로 다른 AZ의 EC2에서 사용 가능하다.
- 높은 가용성, 확장성, 비용(General Purpose SSD의 3배), 사용한 만큼 비용을 낸다.
- EFS 사용 예
	- content Management
	- web serving
	- data sharing
	- Wordpress
- NFSv4.1 프로토콜을 사용한다.
- EFS 접근 제어엔 Security Group을 사용한다.
- KMS를 통해 암호화한다.
- 스탠다드 파일 API를 가지는 POSIX file System (~Linux)을 사용한다.
- 파일시스템은 자동으로 확장하고, 공간 설정이 없다. 또한, 사용한 만큼 비용을 낸다.
### EFS - Performance & Storage Classes
- EFS Scale
	- 동시에 1000개 NFS Client를 가지며 10GB+/s 처리량(throughput)을 가진다.
	- 자동으로 페타바이트 크기까지 늘어날 수 있다.
- 퍼포먼스 모드 (EFS 만들 때 설정한다.)
	- General Purpose (default): 
		- 지연성에 민감한 경우의 사용한다. 
		- 예: web server, CMS, etc...
	- Max I/O: 
		- 더 높은 지연성, 대역폭, 높은 병렬성 
		- 병렬 처리에 중점을 두기 위해서 I/O를 높인다. 따라서, 지연성이 더 클 수도 있다.
		- 예: 빅데이터, 미디어 처리
- 처리량 모드
	- Bursting: 스토리지 1TB 기준 = 50MB/s + 최대 100MiB/s
	- Provisioned: 스토리지 사이즈랑 상관 없이 처리량을 설정할 수 있따.
	- Elastic: 워크로드에 따라 처리량이 자동으로 설정된다.
### EFS - Storage Classes
- Storage Tiers (Lifecycle Management feature - move file after N days)
	- 라이프사이클 관리 기능으로 특정 기간(N days) 이후에 스토리지 클래스를 옮겨서 비용을 절약한다.
	- 스토리지 클래스 목록
		- **Standard**: 자주 접근되는 파일용
		- **Infrequent access (EFS-IA)**: 파일 복구에 돈이 든다. 대신 저장 비용이 더 저렴하다.
		- **Archive**: 아주 가끔 접근되는 데이터들 (년에 몇 번), 50% 저렴하다.
	- 라이프사이클 정책을 통해서 파일들의 Storage Tier를 변경 가능하다.
- Availability and Durability(가용성과 지속성)
	- Standard: 멀티AZ, prod 환경에 좋다.
	- One Zone: One AZ에 저장한다. dev 환경에 사용하며 백업은 자동으로 된다. IA랑 같이 사용 가능하다. (EFS One Zone-IA)
- Storage Class를 통해 90%까지 비용을 아낄 수 있다.
---
## EFS vs EBS
### EBS vs EFS - Elastic Block Storage(1)
- EBS volumes
	- 인스턴스 1개 (단, io1/io2는 멀티 attach 가능)
	- 특정 AZ에 한정된다.
	- gp2: 디스크 사이즈가 증가하면 IO도 증가한다.
	- gp3 & io1: io를 독립적으로 증가시킬 수 있다.
- 인스턴스의 Root EBS Volumes은 기본적으로 EC2 인스턴스가 제거 되면 제거된다. (인스턴스 생성할 때, 옵션으로 retain할 수도 있음.)
- 여러 AZ에 존재하는 인스턴스에 동시에 마운트가 가능하다.
- EFS의 장점
	- 여러 AZ에 최대 100개 인스턴스까지 붙일 수 있다.
	- Linux 인스턴스만 사용 가능하다.(**POSIX 파일시스템을 사용하므로**)
	- EFS는 EBS보다 비싸지만 비용 절감이 가능하다.(스토리지 클래스를 통해서)
---
## EFS Access Points
### EFS - Access Points
- NFS 환경에 접근하는 어플리케이션들을 관리할 수 있다.
- Access Points를 통해 접근하는 어플리케이션에 POSIX 유저와 그룹을 강제할 수 있다.
- 접근 가능한 디렉터리를 제한하고 루트 디렉터리를 변경 가능하다.
- NFS Client에서 IAM Policies를 통해 접근 제어가 가능하다.
### EFS - Operations
![Pasted image 20250118183058.png](/img/user/image/Pasted%20image%2020250118183058.png)
- 제자리에서 가능한 작업들(Operations)
	- Lifecycle Policy (enable IA or change IA settings)
	- 처리량 모드와 Provisioned 처리량 개수 조절 가능
	- EFA Access Points
- Migration을 해야 가능한 작업들 (Data Sync로 이전 가능)
	- 암호화된 EFS로 이전
	- 퍼포먼스 모드로 변경(e.g. Max IO)
---
## EFS - CloudWatch Metrics
- PercentIOLimit
	- I/O Limit에 얼마나 근접한지
	- 만약, 100%면 Max I/O 옵션으로 이전하세요.
- BurstCreditBalance
	- Burst를 위해 사용 가능한 Credit의 양
- Storage Bytes
	- 파일 시스템의 사이즈
---
# 참고 자료