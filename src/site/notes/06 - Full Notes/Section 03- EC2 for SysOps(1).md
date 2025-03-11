---
{"dg-publish":true,"permalink":"/06-full-notes/section-03-ec-2-for-sys-ops-1/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ultimate AWS Certified SysOps Administrator Associate 2024\|Ultimate AWS Certified SysOps Administrator Associate 2024]]
# 핵심 요약
### 🚀 **EC2 핵심 요약**  
---
## **1. EC2 인스턴스 타입 변경**  
- **조건**: EC2가 EBS 볼륨을 사용해야 변경 가능  
- **방법**:  
  1. 인스턴스 중지  
  2. 인스턴스 타입 변경  
  3. 인스턴스 시작  
---
## **2. EC2 네트워킹 (Enhanced Networking)**  
### **(1) EC2 Enhanced Networking (SR-IOV)**  
- 높은 대역폭, 낮은 지연 시간  
- **옵션:**  
  - **ENA (Elastic Network Adapter)** → 최대 100Gbps  
  - **Intel 82599 VF** → 최대 10Gbps (레거시)  
- **적용 가능**: 최신 EC2 인스턴스에서 사용 가능 (T3 이상)  
### **(2) Elastic Fabric Adapter (EFA)**  
- HPC(고성능 컴퓨팅) 및 MPI(Message Passing Interface) 최적화  
- 리눅스 전용, 커널 우회하여 속도 증가  
---
## **3. EC2 Placement Groups (인스턴스 배치 전략)**  
- **Cluster**: 단일 AZ, 저지연, 높은 처리량 (빅데이터 처리)  
- **Spread**: 여러 AZ 분산, 높은 가용성 (AZ별 최대 7개 인스턴스)  
- **Partitioning**: 독립적인 랙 배치, 대규모 분산 처리 시스템 (Kafka, HDFS, Cassandra)  
---
## **4. EC2 종료(Shutdown) 및 보호(Termination Protection)**  
- **Shutdown Behavior**:  
  - 기본값 `stop` (종료)  
  - 설정 변경 시 `terminate` (완전 삭제) 가능  
- **Termination Protection**:  
  - AWS Console/CLI에서 실수로 삭제 방지  
  - OS에서 `shutdown` 실행 시 보호 불가능  
---
## **5. EC2 문제 해결 (Troubleshooting)**  
### **(1) EC2 인스턴스 실행 문제**  
✅ **InstanceLimitExceeded**: vCPU 한도 초과 → **vCPU 제한 증가 요청**  
✅ **InsufficientInstanceCapacity**: AZ 내 자원 부족 → **다른 AZ 시도 / 인스턴스 타입 변경**  
✅ **Instance Terminates Immediately**:  
- EBS 볼륨 제한 초과  
- AMI 손상  
- KMS 키 접근 문제  
### **(2) EC2 SSH 연결 문제**  
✅ **접근 불가 원인:**  
- SSH 키 파일 권한 (`chmod 400`)  
- 사용자명 (`ubuntu`, `ec2-user` 확인)  
- 보안 그룹 / NACL 설정 확인  
- 공인 IP 부재  
✅ **SSH vs EC2 Instance Connect**  
- SSH: 인바운드 포트 필요  
- EC2 Instance Connect: AWS 콘솔에서 웹 기반 SSH 지원 (일회용 공개 키)  
---
## **6. EC2 구매 옵션**  
### **(1) On-Demand**  
- 사용한 만큼 결제 (가장 비쌈, 장기 약정 없음)  
- **짧은 작업, 지속적 실행 작업**에 적합  

### **(2) Reserved Instances (RI)**  
- 1년 또는 3년 약정 → **최대 72% 할인**  
- **Convertible RI**: 인스턴스 타입 변경 가능 (최대 66% 할인)  

### **(3) Saving Plans**  
- 일정 사용량 약정 후 다양한 EC2 서비스 사용 가능  
- 유연한 사용 가능 (`m5.xlarge → m5.2xlarge`, OS 변경 가능)  

### **(4) Spot Instances**  
- **최대 90% 할인**, 가격 변동에 따라 종료 가능  
- **배치 작업, 데이터 분석, 분산 처리 작업**에 적합  

### **(5) Dedicated Hosts vs. Dedicated Instances**  
| 옵션 | 설명 | 주요 특징 |
|------|------|----------|
| **Dedicated Host** | 물리적 서버 전용 | 라이선스 요구 사항 충족, 가장 비쌈 |
| **Dedicated Instance** | 특정 물리적 서버에서만 실행 | 배치 제어 불가, 상대적으로 저렴 |

---
## **7. EC2 인스턴스 용량 예약 (Capacity Reservations)**  
- 특정 AZ에 **On-Demand 인스턴스** 사전 예약  
- 사용 여부와 관계없이 비용 청구됨  
- Reserved Instances & Saving Plans과 결합 가능  
---
### ✅ **핵심 요약**  
1. **EC2 인스턴스 타입 변경** → EBS 필요, 중지 후 변경 가능  
2. **Enhanced Networking** → ENA 사용 시 성능 증가  
3. **Placement Groups** → Cluster (빠른 네트워크), Spread (고가용성), Partitioning (대규모 분산)  
4. **Shutdown/Terminate Protection** → AWS CLI/SDK 보호, OS 내 shutdown 보호 불가  
5. **Troubleshooting** → SSH 연결 문제(키/SG 확인), 인스턴스 실행 문제(vCPU, AZ 자원 부족)  
6. **구매 옵션** → On-Demand (유연), RI/Saving Plans (할인), Spot (저가, 비중요 작업), Dedicated (전용 서버)  
7. **Capacity Reservations** → 특정 AZ에 온디맨드 인스턴스 예약, 비용은 계속 청구  

🚀 **AWS EC2를 효율적으로 운영하려면 인스턴스 배치 전략, 네트워크 성능, 구매 옵션을 고려해야 합니다!**

# 단서 질문
### **AWS EC2 모의고사 (10문제)**

---

#### **1. EC2 인스턴스 타입을 변경하려면 어떤 조건이 필요할까요?**

**A.** 인스턴스가 반드시 퍼블릭 IP를 가져야 한다.  
**B.** 인스턴스가 EBS 볼륨을 사용해야 한다.  
**C.** 인스턴스가 반드시 동일한 AZ 내에 있어야 한다.  
**D.** 인스턴스에 Enhanced Networking이 활성화되어 있어야 한다.

✅ **정답:** **B. 인스턴스가 EBS 볼륨을 사용해야 한다.**  
📌 **해설:** EC2 인스턴스 타입을 변경하려면 EBS 볼륨을 사용해야 하며, 인스턴스를 **중지(Stop)** 한 후 변경할 수 있습니다. 인스턴스 스토어(Instance Store)를 사용하는 경우 타입 변경이 불가능합니다.

---

#### **2. EC2 Enhanced Networking을 활성화하면 어떤 이점이 있을까요?**

**A.** 인스턴스의 RAM 용량이 증가한다.  
**B.** 네트워크 대역폭이 증가하고 지연 시간이 감소한다.  
**C.** 모든 EC2 인스턴스에서 자동으로 활성화된다.  
**D.** 네트워크 패킷이 암호화되어 전송된다.

✅ **정답:** **B. 네트워크 대역폭이 증가하고 지연 시간이 감소한다.**  
📌 **해설:** Enhanced Networking은 SR-IOV 기술을 활용하여 높은 PPS(Packet Per Second), 낮은 지연 시간(Latency), 높은 대역폭을 제공합니다. **ENA(Elastic Network Adapter)** 를 사용하면 최대 100Gbps까지 지원됩니다.

---

#### **3. EC2 Placement Groups 중에서 높은 가용성이 필요한 경우 가장 적절한 것은?**

**A.** Cluster Placement Group  
**B.** Spread Placement Group  
**C.** Partition Placement Group  
**D.** Reserved Placement Group

✅ **정답:** **B. Spread Placement Group**  
📌 **해설:** Spread Placement Group은 여러 **AZ** 에 걸쳐 인스턴스를 분산 배치하여 **하드웨어 장애로 인해 여러 인스턴스가 동시에 실패하는 위험을 줄입니다.** 높은 가용성이 필요한 경우 적절한 배치 전략입니다.

---

#### **4. EC2 인스턴스를 종료할 때 발생하는 동작 중, Shutdown Behavior 설정이 'Terminate'인 경우 어떤 일이 발생하나요?**

**A.** 인스턴스가 단순히 중지(Stop)되며, 나중에 다시 시작할 수 있다.  
**B.** 인스턴스가 영구적으로 삭제되며, 복구할 수 없다.  
**C.** 인스턴스의 EBS 볼륨이 항상 자동으로 삭제된다.  
**D.** AWS 콘솔에서는 'Terminate' 설정이 적용되지 않는다.

✅ **정답:** **B. 인스턴스가 영구적으로 삭제되며, 복구할 수 없다.**  
📌 **해설:** **Shutdown Behavior** 가 `Terminate` 으로 설정되어 있다면, EC2 인스턴스가 종료 명령을 받을 때 **즉시 삭제** 됩니다. 다만, **Termination Protection** 을 활성화하면 AWS Console이나 CLI를 통해 실수로 삭제되는 것을 방지할 수 있습니다.

---

#### **5. EC2 SSH 연결이 되지 않을 때 발생할 수 있는 원인이 아닌 것은?**

**A.** 보안 그룹(Security Group)에서 포트 22가 열려있지 않다.  
**B.** EC2 인스턴스에 공인(Public) IP가 할당되지 않았다.  
**C.** 인스턴스에 할당된 IAM Role이 부족하다.  
**D.** Private Key 파일의 권한이 잘못 설정되었다.

✅ **정답:** **C. 인스턴스에 할당된 IAM Role이 부족하다.**  
📌 **해설:** IAM Role은 SSH 접속과 관련이 없습니다. SSH 연결이 되지 않는 주요 원인은 **보안 그룹 설정, 공인 IP 부재, 잘못된 Key 파일 권한(400 필요)** 등이 있습니다.

---

#### **6. AWS EC2 구매 옵션 중에서 가장 저렴한 방식은 무엇인가요?**

**A.** On-Demand  
**B.** Reserved Instances  
**C.** Spot Instances  
**D.** Dedicated Hosts

✅ **정답:** **C. Spot Instances**  
📌 **해설:** Spot Instances는 온디맨드 가격 대비 **최대 90% 저렴**하지만, AWS에서 인스턴스 용량이 부족하면 **즉시 회수** 될 수 있기 때문에 가용성이 보장되지 않습니다. **배치 작업, 데이터 분석, 분산 처리 워크로드** 에 적합합니다.

---

#### **7. AWS에서 특정 물리적 서버를 완전히 점유하려면 어떤 구매 옵션을 사용해야 하나요?**

**A.** Dedicated Hosts  
**B.** Reserved Instances  
**C.** Spot Instances  
**D.** On-Demand

✅ **정답:** **A. Dedicated Hosts**  
📌 **해설:** Dedicated Hosts는 특정 물리적 서버 전체를 특정 계정에 할당하여 사용할 수 있습니다. **라이선스 요구 사항을 만족해야 하거나, 규제가 많은 환경에서 유용**합니다.

---

#### **8. EC2가 특정 AZ의 용량 부족으로 인해 실행되지 않는 경우, 해결 방법이 아닌 것은?**

**A.** 다른 AZ에서 EC2 인스턴스를 실행한다.  
**B.** 다른 인스턴스 타입으로 변경하여 실행한다.  
**C.** vCPU 제한을 늘린다.  
**D.** 해당 AZ에서 Spot Instance를 요청한다.

✅ **정답:** **C. vCPU 제한을 늘린다.**  
📌 **해설:** AZ 내 용량 부족은 **vCPU 제한과 무관**합니다. 해결책으로는 **다른 AZ 시도, 다른 인스턴스 타입 사용, Spot Instance 요청** 등이 있습니다.

---

#### **9. EC2 Instance Connect를 사용할 경우 어떤 특징이 있나요?**

**A.** AWS 콘솔에서 SSH Key 없이 EC2에 연결할 수 있다.  
**B.** EC2의 보안 그룹 설정과 관계없이 접속할 수 있다.  
**C.** Windows 인스턴스에서도 사용할 수 있다.  
**D.** 영구적인 SSH 접근이 가능하다.

✅ **정답:** **A. AWS 콘솔에서 SSH Key 없이 EC2에 연결할 수 있다.**  
📌 **해설:** EC2 Instance Connect는 **AWS 웹 콘솔을 통해 60초 동안 유효한 임시 공개 키를 사용하여 SSH 접속을 지원**합니다. 그러나 보안 그룹 설정이 올바르지 않으면 접속할 수 없습니다.

---

#### **10. AWS Saving Plans의 특징으로 올바르지 않은 것은?**

**A.** 사용량을 약정하면 EC2, Lambda, Fargate에서 할인 받을 수 있다.  
**B.** 특정 인스턴스 패밀리 및 AWS Region에 적용된다.  
**C.** 약정 금액을 초과하면 추가 사용량은 On-Demand 요금이 적용된다.  
**D.** 특정 EC2 인스턴스에만 고정적으로 적용된다.

✅ **정답:** **D. 특정 EC2 인스턴스에만 고정적으로 적용된다.**  
📌 **해설:** Saving Plans는 특정 EC2 인스턴스가 아니라, **인스턴스 패밀리(예: M5) 및 AWS Region** 에 유연하게 적용됩니다. 사용량을 약정하면 EC2 뿐만 아니라 **Lambda, Fargate** 도 할인됩니다.

---

### **결론 🎯**

이 모의고사를 통해 **EC2 인스턴스 운영, 네트워킹, 배치 전략, 문제 해결, 구매 옵션** 등 중요한 개념을 학습할 수 있습니다. AWS 시험이나 실무에서 유용하게 활용하세요! 🚀
# 핵심 필기
## EC2 인스턴스 타입 바꾸기
![Pasted image 20241219121052.png](/img/user/image/Pasted%20image%2020241219121052.png)
- **조건:** EC2가 EBS 볼륨을 써야 한다.
- **방법:**
	1. 인스턴스 멈추기
	2. 인스턴스 설정에서 인스턴스 타입 바꾸기
	3. 인스턴스 시작

> [!important]  
> EBS는 Elastic Block Storage로 EC2가 사용 가능한 영구지속저장장치를 의미한다.

---
## Enhanced Networking

### 1. EC2 Enhanced Networking(SR-IOV)
- 높은 대역폭, 높은 PPS(Packet Per Second), 낮은 지연속도(latency)
- **Option1**: **Elastic Network Adapter(ENA)** 100Gbps
- **Option2**: Intel 82599 VF 10Gbps - Legacy
- Works for newer generation EC2 Instances(옛날 인스턴스는 지원 안함)
```Shell
ec2-user@host: ~$ modinfo ena

# 아마존 구버전(amazon 2023이전)은 eth0임.
ethotool -i enX0 

# 현재 사용하는 EC2인 t2 구타입이므로 ENA가 아닌 vif 사용
driver: vif 

# t3부터는 ENA 사용함
version: 6.1.102-111.182.amzn2023.x86_64
firmware-version: 
expansion-rom-version: 
bus-info: vif-0
supports-statistics: yes
supports-test: no
supports-eeprom-access: no
supports-register-dump: no
supports-priv-flags: no
```

> [!important]  
> SR-IOV(Single Route I/O Virtualization): 단일 루트 IO 가상화란? 여러 파티션이 동시에 실행되어 PCIe 장치를 공유할 수 있도록 하는 방법  
### 2. Elastic Fabric Adapter(EFA)
- HPC(High Performance Computer)를 위한 ENA 향상판으로 리눅스에서만 동작한다.
- Great for inter-node communication, tightly coupled workloads
- Message Passing Interface(MPI)에 영향을 끼친다.
- 리눅스의 커널을 우회해서 어플리케이션끼리 직접 통신하므로 더 빠르다.(랜카드를 통해서 통신 하지 않는다.)
---

## [SAA] EC2 Placement Groups
EC2 인스턴스의 배치 전략을 정한다.
### Cluster
 ![image 40.png](/img/user/image/image%2040.png)
-  단일 AZ에 인스턴스를 구축 → low latency
- 장점: 네트워크에 좋다. (Enhanced Networking이 설정된 인스턴스 간에 10Gbps 대역폭을 가진다.)
- 단점: AZ이 다운되면 전체 다운
- Use case: 
	- 빠른 처리가 필요한 빅데이터 작업
	- 낮은 지연성과 높은 처리량이 필요한 어플리케이션(Low latency High throughput)
### Spread
![image 1 13.png](/img/user/image/image%201%2013.png)
- **장점**
	- 여러 AZ에 걸쳐 존재할 수 있다.
	- 하드웨어 자체의 문제가 생겨서 인스턴스들이 동시에 실패하는 리스크를 줄인다.
	- EC2 인스턴스들은 서로 다른 물리적 하드웨어에 속한다.
- **단점**
	- AZ의 그룹별로 인스턴스는 최대 7개까지만 가능하다.
- UseCase
	- 높은 가용성(High availability)이 필요한 App
	- 다른 인스턴스가 실패하더라도 영향을 받지 않아야 하는 Application
### Partitioning
![image 2 11.png](/img/user/image/image%202%2011.png)
- 파티션이라는 그룹에 EC2를 넣는 것
- 파티션끼리는 서로 독립적으로 서로 다른 랙을 사용한다.
- AZ 하나에 7개의 파티션을 배치 가능하다.
- 여러 AZ에 걸쳐 존재할 수 있다. (Can span across multiple AZs in the
same region)
- 서로 다른 파티션에 위치한 EC2 인스턴스는 서로 다른 랙에 위치한다.(AZ가 같더라도)
- 파티션 하나에 EC2 수백개까지 배치 가능
- 파티션 하나가 실패하면 많은 EC2 인스턴스에 영향을 끼칠 순 있지만(EC2는 최대한 서로 다른 랙에 배치되어 생산되니까) 다른 파티션엔 영향을 끼치지 않는다.(서로 다른 파티션이므로 서로 다른 랙이므로)
- **UseCase**
	- 카프카와 같이 분산 처리 가능한 APP
	- HDFS
	- HBase
	- 카산드라
---
## EC2 Shutdown Behavior
### Shutdown Behavior
- EC2의 인스턴스에서 CLI를 통해 `shutdown`을 입력하면 두 가지 액션이 일어날 수 있습니다.
	- shutdown(default): 인스턴스가 종료
	- terminate: 인스턴스가 제거됨.
- AWS Console에선 적용이 안됩니다.
### Termination Protection
- AWS Console이나 CLI에서 제거되는 것을 방지합니다.

- **시험 팁**
	1. shutdown 설정을 terminate로 설정 후에 terminate Protection을 킨다
	2. OS에서 shutdown하면 무슨 일이 일어날까? 
	⇒ 인스턴스가 제거된다. 왜일까? terminate Protection은 AWS CLI나 AWS SDK를 통해 종료하는 것을 방지할뿐이지 OS내의 shoutdown을 통한 terminate를 방지하지 못한다.

---
## **Troubleshooting EC2 Launch Issues**
**시험에 백퍼 나오는 파트**
==**InstanceLimitExceeded**==
- **원인**: vCPU 사용량 한계에 도달함을 의미한다.
- **발생 예시**: (A,C,D,H,I,M,R,T,Z) 인스턴스 타입들을 돌려서 총합 vCPU가 64를 넘을 때(64는 default 값) vCPUs 제한은 region별로 있다.
- **해결 방법**
	- 방법1. 인스턴스를 다른 리전에서 실행
	- 방법2. vCPUs 제한 증가 시키기 -> Service Quotas 들어가서 설정 가능
- 중요 포인트: vCPU 기반 제한은 On-Demand랑 Spot 인스턴스에만 적용된다.
==**InsufficientInstanceCapacity**==
- **원인**: EC2 인스턴스를 실행하려는 AZ의 AWS의 가용 자원이 모자람. 
- **해결 방법**
	- 방법1. 기다렸다가 재요청
	- 방법2. 리퀘스트 취소 후에 요청하기. 여러 개 요청 시에 한 번에 리퀘스트로 요청해라
	- 방법3. 너무 급하면 **다른 인스턴스 타입 요청**하기. 나중에 리사이즈하면 됨
	- 방법4. 다른 AZ에서 EC2 실행
==**Instance Terminates Immediately**==
- 가능한 원인들:
	- EBS volume 제한 초과
	- EBS 스냅샷이 오염됨
	- root EBS volume이 암호화 되어 있는데 복호화를 위해 사용할 키가 저장되어 있는 KMS(Key Management System)에 접근 권한이 없어서
	- 인스턴스 실행을 위한 AMI에 필수 파일이 없어서
- 정확한 원인을 찾는 방법:
	- AWS EC2 콘솔 - 인스턴스 - Description 탭
---
## EC2 SSH troubleshooting
- SSH 이슈 주요 발생 원인들			
- private key 파일이 400 권한을 가지고 있어야 한다. 아닐 시 `Unprotected private key file` 에러가 발생한다.
- username 확인하기. (리눅스는 ubuntu, Amazon Image는 ec2-user임. )
	- `Host key not found` 나 `Permission denied` 나 `Connection closed by [instance] port22` 에러가 발생한다.
- `Connection timed out` 이 발생하는 이유는 아래와 같다.
	1. SG 설정
	2. NACL 설정
	3. 인스턴스의 CPU 과부하
	4. 인스턴스의 공인 IP가 없음

> [!important]  
> NACL(Network Access Control List)은 VPC(가상 사설 클라우드) 내에서 서브넷(subnet) 수준에서 인바운드(inbound)와 아웃바운드(outbound) 트래픽을 제어하기 위한 보안 계층입니다. NACL을 사용하면 허용(allow)하거나 거부(deny)할 트래픽을 규칙으로 정의할 수 있으며, 이러한 규칙은 서브넷에 연결된 모든 인스턴스에 적용됩니다.  
### SSH vs EC2 Instance Connect			
![Pasted image 20241219135739.png](/img/user/image/Pasted%20image%2020241219135739.png)
- SSH
	- SSH는 보안그룹에서 허용한 인바운드를 통해서 접근한다.
- EC2 Instance Connect
	- EC2 Instance Connect는 AWS 대시보드에서 제공하는 접근 방법으로 60초동안 사용 가능한 일회용 공개 키를 통해 접속한다.
	- 단, EC2 Instance Connect도 SSH 연결과 마찬가지로 SG에서 허용하지 않은 IP나 포트를 통해 접속하면 접속이 불가능합니다.
---
## EC2 Instance Purchasing Options
### On-Demand
- 사용한만큼 낸다.
	- 리눅스 or 윈도우: 초당 청구
	- 다른 OS: 시간당 청구
- 제일 비싸지만 선불 결제가 없다.
- 장기간 계약(long-term commitment)이 없다.
- 짧은 작업(short term)이나 지속적으로 동작(uninterrupted workloads)에 적합하다.
### EC2 Reserved Instances(RI)
- On-demand에 비해 72% 쌉니다.
- 사양을 예약합니다.(인스턴스 타입, 지역, 테넌시, OS)
- **예약 기간: 1년 or 3년**
- **지불 옵션: 후불, 부분선불, 전체선불 (No Upfront, Partial Upfront, All Upfront)**
- **예약 범위(Reserved Instance’s Scope): Regional or Zonal(특정 리전이거나 특정 AZ)**
- 꾸준히 사용되는 어플리케이션에 용이 eg.Database
- Reserved Instance 마켓플레이스에서 되팔거나 사기 가능

-  **Convertible Reserved Instance(전환 가능한 RI)**
	- 인스턴스 타입, 인스턴스 패밀리, OS, Scope, Tenancy 변경 가능
	- 66% 할인(일반 RI보다 비쌈)
> [!important]  
> 테넌시란? EC2 인스턴스가 물리적으로 저장되는 방식  
### Saving Plans
- 캐시포인트처럼 사용 포인트(commitment)를 사서 서비스를 쓰는 느낌
- 장기간 사용을 예상하고 할인(최대 72%)
- 제일 높은 할인 계약부터 적용된다.
	- 예시:
		- EC2 30% 할인, Lambda 15% 할인
		- EC2 먼저 30% 할인 금액으로 계산하고 만약 비용을 초과했다면 초과 전 금액까지만 EC2를 계산, 초과하지 않았다면 Lambda도 적용 계산
- 사용량에 따라 계약 가능하다($10/hour이 할인으로 1년 or 3년)
	- 자세한 설명
		- $ 10/hour이 이해가 안갔는데 약정 금액이 10$/hour인거다. 만약, EC2 100대를 사용해서 한 시간에 15달러가 나왔다? 10달러 요금까지는 Saving Plans 기준으로 요금을 내고(최대 기준 72%) 나머지 5달러는 EC2 onDemand 요금으로 낸다. 참고로 EC2를 아예 사용하지 않아도 약정 금액은 무조건 나간다.
- Saving Plan 할인 금액을 넘는 금액은 On-Demand로 청부
- 특정 인스턴스 패밀리와 AWS Region에 고정된다. (eg. M5 in us-east-1)
- 유연하다
	- Instance Size(e.g m5.xlarge → m5.x2Large)
	- OS (eg. Linux, Windows)
	- Tenancy (Host, Dedicated, Default)
- 다음은 내가 직접한 예시라 실제 비용과 차이가 있을 수 있음.
	- ![Pasted image 20250123121735.png](/img/user/image/Pasted%20image%2020250123121735.png)
	- 공통 조건
		- `r5.4xlarge` 4개 = 4
		- `m5.24xlarge` 1개 = 10
		-  `Fargate` 400vCPU랑 1,600GB
		-  `Lambda` 100만개 요청, 512MB 3초
	- **케이스 1**: Saving Plan with a $50.00/hour commitment
		- 공식: `r5.4xlarge` + `m5.24xlarge` + `Fargate` + `Lambda`
		- SP 사용 시 1시간 비용: 47.13
		- SP 없을 시 1시간 비용: `(4 * 1)` + `(1 * 10)` + `(400 * 0.04 + 1600 * 0.004)` + `(0.2 + 0.5 * 3 * 1,000,000 * 0.000015)` = 36.4 + (0.2 + 22.5?) = $59.10
	- **케이스 2**: Saving Plane with a $2/hour commitment
		- 제일 높은 할인률보다 적용됨. (`r5.4xlarge`가 30%로 제일 할인률이 높다.)
		- r5.4xlarge 시간당 0.7달러
		- 2 / 0.7 = 2.857142857... === 2.9 => `r5.4xlarge` 4개 유닛 중에서 2.9개 할인만 받고 나머지 1.1은 온디맨드
		- 나머지 `m5.24xlarge`, `Fargate`, `Lambda`는 SP 요금을 모두 다 썼으므로 온디맨드로 요금 부여

### **Spot Instances**
- 온디멘드에 비해 최대 90프로 저렴하다.
- 설정한 max price이 현재 spot 가격보다 낮으면 인스턴스를 뺏긴다. (설정한 예산보다 EC2의 요구 가격이 비싸므로 빌리지 못하는 것)
- 제일 저렴하다.
- 실패해도 좋은 작업들에 좋다.
	- Batch Jobs
	- Data Analysis
	- Image Processing
	- Any distributed workloads
	- Workloads with a flexible start and end time
- 중요한 작업이나 DB에는 적합하지 않다.
### **Dedicated Hosts**
- 물리적 서버를 통째로 빌립니다.
- 복잡한 요구사항을 따를 수 있게 하고 고정 서버 방식의 소프트웨어 라이선스를 재사용 가능합니다.(Allows you address compliance requirements and use your existing server- bound software licenses)
- 결제 옵션
		- 온디멘드: 초당 결제
		- Reserved: 1년, 3년
- 제일 비쌈
- 복잡한 레이센스를 가진 소프트웨어에 유용하다.(eg. BYOL)
- 규제가 심한 회사(외부에 데이터 반입 불가)에서 사용 가능
### **Dedicated Instances**
- 특정 하드웨어를 사용하며 해당 하드웨어에서만 인스턴스를 생성한다.
- 같은 계정에서 만든 인스턴스들이 해당 하드웨어를 사용할 수 있다.
- 인스턴스 배치에 대한 제어 없음
### **Capacity Reservations**
- 특정 AZ에 온디맨드 인스턴스들을 예약함.
- 언제든 EC2에 접근 가능
- 시간 계약이 없다.(언제든지 만들거나 제거할 수 있다.), 할인도 없다.
- Regional Reserved Instances나 Saving Plans와 결합해서 할인 받을 수 있다.
- 인스턴스를 실행하든 안하든 On-Demand 요금 부과한다.
- 짧은 기간, 방해받으면 안되는 작업(short term, uninterrupted workloads)이며 특정 AZ에 있어야 하는 작업이 적합하다.
### **Dedicated Hosts vs Dedicated Instances**
- **일반 인스턴스**: 같은 물리적 서버에서 여러 AWS 고객이 인스턴스를 실행할 수 있는 **멀티 테넌시 구조**.
- **Dedicated Instance**: 물리적 서버(노드)를 **오직 하나의 고객**만 사용하며, 다른 고객의 인스턴스는 같은 서버에서 실행되지 않음.
![image 4 10.png](/img/user/image/image%204%2010.png)

**물리적 서버**: 실제로 물리적으로 돌아가는 하드웨어 컴퓨팅 단위. 노드라고 부를 수도 있다.
**인스턴스**: 물리적 서버에서 돌아가는 OS의 개수. 멀티 테넌시 구조면 하나의 서버에 여러 인스턴스가 올라간다. ==일반적으로== 우리가 사용하는 인스턴스는 **다른 사용자들과 동일한 물리적 서버를 공유**한다.

---

[^1]:  https://docs.aws.amazon.com/savingsplans/latest/userguide/sp-applying.html
