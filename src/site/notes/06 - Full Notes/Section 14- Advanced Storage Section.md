---
{"dg-publish":true,"permalink":"/06-full-notes/section-14-advanced-storage-section/","dgPassFrontmatter":true,"noteIcon":""}
---

# Tags
[[03 - Tags/Ultimate AWS Certified SysOps Administrator Associate 2024\|Ultimate AWS Certified SysOps Administrator Associate 2024]]

---
# 단서 질문
- IOPS와 Throughput의 차이
    - IOPS는 I/O per second로 입력과 출력. 즉, 쓰기와 읽기에 초당 연산 횟수를 의미한다.
    - 만약에, IOPS의 값이 1600(64kb)이라면 이 기억장치는 100MB/s로 입출력이 가능하다는 뜻이다.
    - 처리량(Throughput)은 실제 단위 시간동안 데이터 처리량을 의미한다.
    - 예를 들어 보면 아래와 같다.
    
    1. 클라이언트가 100MB 데이터 요청
    2. 서버로 요청 도착
    3. 서버에선 IOPS 스펙에 맞춰 조회를 실시. 1초동안 데이터 조회(IOPS를 1600 64kb로 가정했을 때)
    4. 데이터를 클라이언트에게 전달. 대역폭이 50mb/s라면 2초동안 클라이언트에게 데이터가 전달됨.
    5. 클라이언트는 자신의 PC에 데이터를 저장(클라이언트의 IOPS를 기반으로 데이터가 저장됨)
- FSx란?
    - File System X의 줄임말로 다양한 파일 시스템을 제공하는 서비스다. S3의 Bucket을 마운트할 수도 있다. Window File System부터 Lustre 파일시스템까지 제공한다.
- Window FS vs Lustre
    - Lustre가 더 HPC에 적합하다.(병렬 액세스를 제공하므로) 또한, Lustre는 S3에 직접 접근이 가능하다.    
- EFS vs FSx
    - **EFS**는 **리눅스 기반** 애플리케이션을 위한 **NFS** 파일 시스템을 제공하며, **자동 확장** 및 **다중 AZ** 지원으로 **대규모 병렬 액세스**에 유용합니다.
    - **AWS FSx**는 **특정 파일 시스템 유형**(예: Windows용 NTFS, 고성능 컴퓨팅용 Lustre)을 지원하며, **특정 워크로드에 최적화된 파일 시스템**을 제공합니다.

| 특성            | **Block Storage**                        | **File Storage**                               | **Object Storage**                    |
| ------------- | ---------------------------------------- | ---------------------------------------------- | ------------------------------------- |
| **데이터 저장 방식** | 데이터를 **블록 단위**로 분할하여 저장                  | 데이터를 **파일과 디렉토리** 구조로 저장                       | 데이터를 **객체 단위**로 저장 (키-값 쌍)            |
| **액세스 방식**    | **서버 기반**으로 블록을 로우 레벨에서 관리               | **NFS**, **SMB** 등의 파일 시스템 프로토콜 사용             | **HTTP(S) API**를 통해 객체에 액세스           |
| **주요 사용 사례**  | **데이터베이스**, **가상 머신**, 고성능 요구 작업         | **웹 서버**, **공유 네트워크 스토리지**, **홈 디렉토리**         | **정적 콘텐츠 저장**, **백업**, **아카이브**       |
| **확장성**       | 제한적, 스토리지 크기 및 확장 필요 시 수동 관리 필요          | 중간 정도, 파일 시스템 구조 확장성                           | 매우 높음, 무제한 확장 가능                      |
| **데이터 관리**    | 애플리케이션에서 **디스크**처럼 관리                    | **파일과 디렉토리**로 계층적 관리                           | 각 객체는 **메타데이터**와 **키**로 관리            |
| **데이터 수정 방식** | **개별 블록** 단위로 수정 가능                      | **개별 파일** 단위로 수정 가능                            | **전체 객체**를 수정 또는 다시 업로드해야 함           |
| **내구성 및 가용성** | 특정 디스크나 볼륨에 따라 다름                        | 여러 서버와 연결되어 가용성 증가                             | **높은 내구성**(예: S3는 11 9s)과 가용성 (다중 AZ) |
| **성능**        | **고성능 I/O**가 필요한 애플리케이션에 적합              | 일반적인 파일 액세스 및 공유에 적합                           | **고성능 처리량**보다는 내구성과 대규모 확장이 중요        |
| **비용**        | 보통 **높음**, 고성능 제공                        | 보통 수준                                          | 대규모 데이터 저장 시 **비용 효율적**               |
| **사용 예시**     | **EC2 EBS**, **EC2 Instance Store, SAN** | **EFS**, **FSx**, **NAS**, Windows File Server | **S3**, **Glacier**, 비디오 및 이미지 저장, 백업 |

---
# 핵심 요약
- 10TB가 넘는 데이터를 최소 비용으로 이전하고 싶다면 어떻게 해야 할까?
	- Snow Family 하드웨어를 직접 리소스에 연결해서 데이터를 이전한다.
- A 회사는 온프로미스와 클라우드를 동시에 운영하는 하이브리드 클라우드 환경이다. 온프로미스에 데이터를 저장하고 S3에 데이터를 백업하기 위한 방법으론 뭐가 있을까?
	1. 주기적으로 온프로미스에서 S3에 동기화
	2. Storage Gateway를 통해서 온프로미스 데이터센터와 AWS S3 저장소를 연결
- Storage Gateway는 언제 사용하나?
	- DR
	- 온프로미스 데이터센터의 데이터를 클라우드에 백업
---
# 핵심 필기
## AWS Snow Family Overview
- 오프라인 데이터 전송 장치로 데이터를 적재하고 AWS에 배송해서 Snowcone을 직접 리소스에 연결해서 데이터를 이전한다.
- AWS Snow Family는 포터블 디바이스로 edge에서 데이터를 수집 및 처리하고 해당 데이터를 AWS에 전달할 수 있다.
### Data Migrations with AWS Snow Family
- 데이터 Migration에서 생기는 문제점들
	- 제한된 연결성
	- 제한된 대역폭
	- 높은 네트워크 비용
	- 공유되는 대역폭 (최대 대역폭 사용 불가)
	- 연결의 안정성
- AWS Snow Family는 **오프라인 디바이스**로 데이터 이전을 지원합니다.
- 만약 데이터 이전이 1주 이상 걸리면 Snowball Device를 사용하세요
- Snowball에 데이터를 적재(Load)하고 이를 AWS로 전달(Ship)하고 이를 S3 Bucket에 Import하거나 Export한다.
### Snow Family - Usage Process
- 데이터를 이전할 서버에 Snowball client를 설치한다.
- 서버에서 Snowball에 연결 후에 데이터를 적재한다.
- Snowball은 적재가 완료되면 AWS의 S3 Bucket에 데이터를 전송한다.
- 작업이 완료되면 Snowball의 데이터는 초기화된다.
### What is Edge Computing
- 데이터를 만들어진 곳(Edge Location)에서 바로 처리하는 것을 Edge Computing이라 한다.
- Edge Location은 인터넷이 제한되었거나 컴퓨터 사용이 불가능할 수 있다.
- 이를 위해 Snowball Edge / Snowcone을 사용한다. (물리적인 실제 디바이스임)
- 사용 예시
	- 데이터 전처리
	- 머신러닝
	- 미디어 부호화(transcoding media)
## Amazon FSx
### Amazon Fsx - Overview
- Amazon FSx (Amazon File System x)
- AWS에서 서드파티 파일시스템을 실행한다.
	- 예시: FSx for Lustre, FSx for NetApp ONTAP, FSx for Windows File Server, FSx for OpenZFS
- Fully Managed Service
> [!important]  
> 러스터(Lustre)는 분산 파일시스템의 한 유형인 병렬 파일시스템으로 주로 HPC의 대용량 파일시스템으로 사용되고 있습니다. 러스터는 GNU GPL 정책의 일환으로 개방되어 있으며 소규모 클러스터 시스템부터 대규모 클러스터까지 사용되는 고성능 파일시스템입니다. 이름의 유래는 Linux + Cluster이다.  
	
### Amazon Fsx for Windows(File Server)
- SMB & NTFS 프로토콜을 지원한다.
- 디렉터리 통합, ACL(Access Control List), user quotas(유저가 사용할 수 있는 자원 제한)을 지원한다.
- **Linux EC2 인스턴스에 마운트 가능하다.**
- MS의 DFS 네임스페이스를 지원한다. (여러 파일시스템을 통합해서 사용)
- 최대 10s of GB/s, millions of IOPS, 100s PB of data까지 스케일 업이 가능하다.
- 온프로미스에서 접근 가능 (VPN을 통한 접근이나 Direct Connect)
- 여러 AZ에 설정 가능하다. (높은 가용성)
- 매일 S3에 백업된다.
### Amazon FSx for Lustre
- 러스터는 병렬 분산 파일시스템으로 큰 규모의 컴퓨팅에 적합하다.
- S3와 통합 가능
	- S3를 파일 시스템으로 읽을 수 있다.
	- S3에 작성을 할 수 있다.
- 온프로미스에서 사용 가능하다.
> [!important]  
> IOPS Intensive(입출력 집중) vs Throughput-Intensive(처리량 집중)I/O 집중 워크로드란 작고 빈번한 데이터 입출력 작업을 많이 수행하는 작업을 의미합니다. 예를 들어, 데이터베이스 쿼리, 파일 메타데이터 작업, 랜덤 읽기/쓰기 작업 등은 I/O 작업의 빈도가 매우 높고, 작은 크기의 데이터 조각이 자주 접근됩니다.SSD는 랜덤 액세스가 빠르며 낮은 Latency를 가지므로 I/O 집중 워크로드에 적합합니다.처리량 집중 워크로드는 대량의 데이터를 연속적이고 일관된 속도로 전송하는 것이 중요한 작업입니다. 예를 들어, 대규모 데이터 분석, 백업 작업, 대용량 파일 전송 작업이 이에 해당합니다.HDD는 연속적인 데이터 처리에 효율적입니다.단, HDD는 여전히 SSD보다 데이터 처리 속도가 느립니다. 그럼에도 처리량 집중 워크로드는 커다란 데이터를 일관된 속도로 전송해야 하므로 비용 측면에서 HDD가 효율적입니다.  

> [! SSD는 왜 HDD보다 랜덤 Access가 빠를까?]
SSD의 경우, 헤드를 돌려서 지정한 블록을 찾는 시간이 없다. 즉, 탐색(seek) 시간이 없다. 따라서, 랜덤 액세스가 HDD에 비해 빠르다.

### FSx Lustre - File System Deployment Options
![Screenshot from 2025-01-31 18-19-59.png](/img/user/image/Screenshot%20from%202025-01-31%2018-19-59.png)
- Lustre 파일시스템 배포 옵션
	1. Scratch File System
		- 임시 데이터
		- 데이터는 복제되지 않음
		- 높은 버스트 (처리량을 못따라가면 기존 처리량보다 최대 6배 빨라진다.)
		- 사용 예: 짧은 기간 처리할 작업. 비용 절감
	2. Persistent File System
		- 장기간 스토리지
		- 같은 AZ에 똑같은 데이터가 복제된다.
		- 사용 예: 장기간 작업, 민감한 데이터

### Amazon FSx for NetApp ONTAP
- NetApp ONTAP은 **NetApp의 파일 시스템 기술로, 데이터 액세스와 관리 기능을 제공하는 스토리지 솔루션**입니다. ONTAP은 엔터프라이즈 애플리케이션에 필요한 안정성과 지속적인 데이터 액세스를 제공하며, 관리가 간편하고 빠르며 안전합니다.
- NFS, SMB, iSCSI 프로토콜과 호환 가능
- ONTAP이나 NAS에서 작업 중인 워크로드를 AWS로 옮길 수 있다.
- 저장소는 자동으로 줄거나 커질 수 있다.
- 스냅샷, Replication, low-cost, compression, 데이터 de-duplication
- 특정 시점에 동시에 클론이 가능하다.
### Amazon FSx for OpenZFS
- NFS와 같이 사용 가능 (File System Compatible with NFS v3, v4, v4.1, v4.2)
- ZFS의 워크로드를 AWS로 옮길 수 있다.
- 스냅샷, 압축, 저비용
- 특정 시점의 동시에 복제 가능(Point-in-time instantaneous cloning)
---
## FSx for SysOps
- FSx for Windows - Single-AZ
	- 같은 AZ 내에서 데이터 복제를 해놓는다.
	- Signle-AZ 1 (SSD)와 Single-AZ 2 (SSD & HDD)가 존재한다.
- FSx for Windows - Multi-AZ
	- 서로 다른 AZ에 데이터를 복제해놓는다.(동기적 복제)
	- Standby(대기 상태) 파일 서버를 서로 다른 AZ에 만들어 놓는다.
---
## Storage Gateway Overview
### Hybrid Cloud for Storage
- 온프로미스 + 클라우드를 하이브리드 클라우드라 한다.
- 하이브리드 클라우드는 장기간 클라우드 이전이나 보안, 규정, IT 전략을 위해 사용된다.
- S3는 ==**Proprietary storage technology**==로 온프로미스와 연결하기 위해선 EFS(NFS 프로토콜을 사용하므로 NFS 프로토콜을 사용해서 연결하면 됨)와 달리 AWS Storage Gateway를 사용해야 한다.
> [!important]  
> Proprietary storage technology is a system, tool, or application that is exclusively owned by a business or individual.  

| 특성            | **Amazon S3**                   | **Amazon EFS**              |
| ------------- | ------------------------------- | --------------------------- |
| **스토리지 유형**   | **객체 스토리지**                     | **파일 시스템 스토리지 (NFS)**       |
| **데이터 저장 방식** | 객체 단위로 데이터 저장 (키-값 구조)          | 파일 단위로 저장 (디렉토리 구조)         |
| **설계 철학**     | AWS의 **독자적인 스토리지 기술**           | **POSIX 호환 네트워크 파일 시스템**    |
| **주요 사용 사례**  | 정적 콘텐츠 저장, 백업, 대용량 데이터 아카이빙     | 여러 인스턴스 간 파일 공유, 웹 서버 파일 관리 |
| **데이터 수정 방식** | 전체 객체를 재업로드해야 함                 | 개별 파일 수정 가능                 |
| **내구성 및 가용성** | 높은 내구성(11 9s), 데이터가 여러 AZ에 복제됨  | 다중 AZ에서 파일 시스템 복제, 자동 확장    |
| **사용 사례**     | 이미지, 동영상, 백업 파일, 대용량 데이터 아카이빙 등 | 리눅스 서버 애플리케이션, 공유 파일 시스템    |
### AWS Storage Cloud Native Options
### AWS Storage Gateway
- 은프로미스 데이터와 클라우드 데이터의 다리 역할
- Use cases:
	- DR
	- 백업 & 복구
	- 티어 저장소(tiered storage: 중요도에 따라 데이터를 카테고라이징화하는 것)
	- 온프로미스 캐시 & low latency 파일 접근
- Types of Storage Gateway
	- S3 File Gateway
	- FSx File Gateway
	- Volume Gateway
	- Tape Gateway
### Amazon S3 File Gateway
![Screenshot from 2025-01-31 18-46-25.png](/img/user/image/Screenshot%20from%202025-01-31%2018-46-25.png)
- S3 Gateway에 NFS나 SMB 프로토콜을 통해 요청해주면 Gateway에서 S3에 HTTPS로 요청해서 응답을 받는다.
- 최근에 받은 데이터는 게이트웨이에 캐싱된다.
- 각 파일게이트 별로 IAM role을 통해서 S3 버켓에 접근한다.
- SMB Protocol has integration with Active Directory (AD) for user authentication
### Amazon FSx File Gateway
![Screenshot from 2025-01-31 18-51-48.png](/img/user/image/Screenshot%20from%202025-01-31%2018-51-48.png)
- Amazon FSx에 네이티브 액세스가 가능하다.
- 자주 접근하는 데이터를 로컬에서 캐싱한다.
- 게이트웨이를 사용하지 않고도 AWS FSx에 접근이 가능하다.
- 그럼에도 사용하는 이유는 자주 사용되는 데이터를 게이트웨이에서 캐싱하기 때문
### Volume Gateway
![Screenshot from 2025-01-31 18-54-54.png](/img/user/image/Screenshot%20from%202025-01-31%2018-54-54.png)
- iSCSI 프로토콜을 사용하며 backed by S3
- EBS 스냅샷을 기반으로 온프로미스 볼륨을 복구할 수 있다.
- 볼륨 게이트웨이는 두 가지 모드가 있다.
	- Cached Volumes: 최근 데이터에 low latency access
	- Stored Volumes: 모든 데이터가 온프로미스에 있으며 스케쥴링을 통해서 S3에 백업
### Tape Gateway
![Screenshot from 2025-01-31 18-57-08.png](/img/user/image/Screenshot%20from%202025-01-31%2018-57-08.png)
- 몇몇 회사는 실제 물질 테이프(기억장치)를 백업으로 사용한다.
- Tape Gateway는 이와 동일한 절차를 사용하지만 이를 클라우드에서 진행한다.
- VTL은 S3와 Glacier 기반으로 만들어진다.
- Tape Gateway는 Client 측에 설치되어야 한다.

### Storage Gateway - Hardware appliance
- 스토리지 게이트웨이를 사용하기 위해서는 온프로미스 가상화가 필요하다.
- 혹은 AWS에서 Hardware Appliance를 사용하면 된다.
- amazon.com에서 구매 가능한 하드웨어다.
### AWS Storage Gateway
![Screenshot from 2025-01-31 19-19-46.png](/img/user/image/Screenshot%20from%202025-01-31%2019-19-46.png)
---
## Storage Gateway for SysOps
### Storage Gateway - SysOps
- File Gateway는 POSIX 규약을 사용한다. (리눅스 파일 시스템과 동일)
	- 따라서 파일에 ownership, 권한, 타임스탬프와 같은 메타데이터가 오브젝트에 포함되어 S3에 저장된다.
- Storage Gateway 리부팅하는 법
	- File Gateway: Storage Gateway VM을 리부트하면 된다.
	- Volume and Tape Gateway
		1. 스토리지 게이트웨이 서비스를 중단
		2. Storage Gateway VM을 리부트
		3. 스토리지 게이트웨이 서비스를 실행
### Storage Gateway - Activations
- 스토리지 게이트웨이 활성화 키를 얻는 방법
	1. Gateway VM CLI를 통해서
	2. Gateway VM에 HTTP Request
- 트러블 슈팅하는 법
	- Gateway VM의 80번 포트가 열렸는지 확인
- Gateway와 NTP 서버의 타임존이 동기화 되었는지 확인
- ![Screenshot from 2025-01-31 19-25-29.png](/img/user/image/Screenshot%20from%202025-01-31%2019-25-29.png)
### Storage Gateway - Volume Gateway Cache

---
# 참고 자료
