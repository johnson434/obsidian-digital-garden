---
{"dg-publish":true,"permalink":"/06-full-notes/section-06-ec-2-high-availability-and-scalability-2/","dgPassFrontmatter":true}
---

# Tags
[[06 - Full Notes/Section 06- EC2 - High Availability and Scalability(1)\|Section 06- EC2 - High Availability and Scalability(1)]]

---
# 단서 질문
- ASG란?
    ASG란 AutoScaling Group의 약자로 ASG를 생성하면 해당 그룹을 통해 인스턴스의 생성이나 제거를 할 수 있습니다.
- ASG 스케일링 기법들
    1. Dynamic Scheduling
        1. 동적 스케쥴링 기법으로 매트릭스 기반으로 인스턴스의 개수를 조절합니다.
            1. Target Tracking Scaling
                1. 원하는 목표치를 설정하면 해당하는 상태에 맞게 오토스케일링
                2. ex: CPU 사용량 40%
            2. Simple / Step Scaling
                1. 클라우드 워치 알람이 트리거되면(CPU > 70%) 인스턴스를 2개 추가
                2. 클라우드 워치 알람이 트리거되면(CPU < 30%) 인스턴스를 1개 제거
- AWS DynamoDB(AWS에서 제공하는 NOSQL 서버리스 DB)와 AWS Aurora(AWS에서 제공하는 RDS)가 AutoScaling이랑 무슨 연관이 있나?
    두 서비스 모두 오토스케일링을 지원
---
# 핵심 요약
- Auto Scaling Group (ASG)은 EC2 인스턴스의 수를 자동으로 조정하는 AWS 서비스입니다.
- ASG는 Launch Template을 사용하여 인스턴스를 생성하며, 이는 여러 버전을 지원하고 재사용이 가능합니다. (EKS NodeGroup도 Launch Template을 통해 생성 가능하다.)
- ASG는 EC2 상태 확인, ELB 헬스 체크, 커스텀 헬스 체크를 통해 인스턴스의 상태를 모니터링합니다.
- SQS의 대기열 길이를 기반으로 ASG를 조정할 수 있습니다.
- CloudWatch는 ASG의 성능을 모니터링하고 지표를 제공합니다.
- DynamoDB는 WCU와 RCU를 통해, Aurora는 동적 읽기 복제본 자동 확장을 통해 Auto Scaling을 구현합니다.
---
# 핵심 필기
## [SAA/DVA] Auto Scaling Groups(ASG) Overview
### What’s an Auto Scaling Group?
- 실생활에선 웹사이트나 어플리케이션에 부하량이 달라질 수 있습니다.
- 클라우드에선 서버를 순식간에 만들고 제거가 가능합니다.

- Auto Scaling Group(ASG)의 목표
	- 부하 증가에 따라 Scale out (EC2 인스턴스 추가)
	- 부하에 감소에 따라 Scale in (EC2 인스턴스 제거)
	- 최소한이면서 최대의 EC2 인스턴스 실행을 보장(Ensure we have a minimum and a maximum number of EC2 instances running)
	- 자동으로 로드밸런서에 인스턴스를 등록 (Target Group으로 ASG가 올 수 있다.)
	- 인스턴스의 상태가 좋지 않을 경우 재생성 (ex: unhealthy)
- ASG는 무료입니다. (EC2 인스턴스 비용만 낸다.)
### Auto Scaling Group in AWS
![image 1 11.png](/img/user/image/image%201%2011.png)
### Auto Scaling Group Attributes
-  A Launch Template
	- AMI + Instance Type
	- EC2 User Data
	- EBS Volumes
	- SSH Key Pair
	- IAM Roles for your EC2 Instances
	- Load Balancer Information
- Min Size / Desired Size / Max Size
- Scaling Polices

---
## [SAA/DVA] Auto Scaling Groups - Scaling Policies
### Auto Scaling - CloudWatch Alarms & Scaling
- CloudWatch Alarms를 통해서 ASG 스케일링이 가능합니다.
- Alarm은 매트릭을 모니터링합니다. (평균 CPU 사용량이나 Custom Metric 등)
- 평균 CPU 사용량은 ASG에 속한 모든 인스턴스의 평균 사용량입니다.
- Alarm을 기반으로 다음과 같은 정책 설정이 가능합니다:
	- scale-out 정책
	- scale-in 정책
### Auto Scaling Groups - Scaling Policies
- 동적 스케일링
	- 타켓 스케일링 (Target Tracking Scaling): 
		- 목표하는 수치를 정해놓는 스케쥴링 기법 (ex: CPU 사용량 40% 이하)
	- 간단 스케일링 (Simple / Step Scaling): 
		- 이벤트에 대한 인스턴스 수치를 정의 (ex: CPU 사용량 70% 이상이면 인스턴스 2개 증가, CPU 사용량 30% 이하면 인스턴스 1개 감소)
	- 스케쥴 스케일링 (Scheduled Scaling): 
		- 어플리케이션 사용 환경에 따른 사용량을 예측해서 인스턴스 개수를 예상
		- ex: 금요일 5시 이후에 인스턴스 개수 증가
	- 예측 스케일링 (Predicted Scaling)
		- 지속적으로 현재 사용량을 모니터링하여 사전에 인스턴스 개수를 늘리는 방법
### Good metrics to scale on
- CPU 사용량: 인스턴스들의 평균 CPU 사용량
- RequestCountPerTarget: 인스턴스 당 받는 요청 개수
- 평균 네트워크 In / Out (Application이 Network bound라면)
- Any custom metric (CloudWatch에 Push한 커스텀 메트릭)
	
### Auto Scaling Groups - Scaling Cooldowns
![Pasted image 20250115134312.png](/img/user/Pasted%20image%2020250115134312.png)
- 스케일링 액션이 일어나면 쿨다운 상태에 들어간다.(default 설정은 5분)
- 쿨다운 상태에선 인스턴스에 추가/삭제가 일어나지 않는다. (메트릭 안정화를 위해서)
- Advice: AMI를 사용하면 환경이 셋업 되어 있기 때문에 cooldown 기간을 줄일 수 있다. -> 아무래도 직접 패키지를 설치하는 것보단 설치되어 있는 AMI를 사용하는 게 빠를 수밖에 없다.

---
## ASG for SysOps
### ASG - Lifecycle Hooks
- ASG 내에 인스턴스가 시작되면 InService 상태가 됩니다.
- Pending 상태에서 인스턴스가 InService 상태가 되기 전에 추가적으로 작업을 할 수 있습니다.
	- Pending 상태 내에 인스턴스 시작하면 실행할 스크립트를 정의하세요.
- Terminating 상태에서 인스턴스가 제거(Terminated)되기 전에 몇 가지 작업을 할 수 있습니다.
	- Terminating 상태에서 인스턴스가 제거 되기 이전에 트러블 슈팅을 할 수 있습니다.
- 사용 예
	- 클린업(인스턴스가 제거 되기 전에 환경을 클린)
	- 로그 추출
	- 특별한 헬스 체크
- EventBridge, SNS, SQS와 통합해서 사용 가능합니다.
![Pasted image 20250115134721.png](/img/user/Pasted%20image%2020250115134721.png)
### Launch Configuration vs Launch Template

![image 10 5.png](/img/user/image/image%2010%205.png)

- 공통점
	- AMI, 인스턴스 타입, 키페어, Security Group 및 인스턴스를 실행하기 위한 여러 매개변수들(tags, EC2 user-data)
	- 둘 다 수정은 불가능
- Launch Configuration (Legacy):
	- 매 번 재생성해야함
- Launch Template (Newer):
	- 여러 버전 사용 가능
	- **파라미터 부분 집합**을 만들어 놓으면, 여러 설정들을 재사용하고, 필요할 때마다 기존 템플릿을 상속받아 유사한 환경을 쉽게 구성할 수 있다는 의미입니다.
	- Spot이나 On-Demand를 통해 프로비저닝(인스턴스 생성) 가능
	- AWS에서 현재 권장하는 방식 (Launch Configuration 뜨면 Alert창 뜸)

---
## SQS with Auto Scaling Group (ASG)

![image 11 5.png](/img/user/image/image%2011%205.png)

- SQS의 Queue Length를 기반으로 AutoScaling 하는 방법
- SQS의 Queue Length가 커지면 CloudWatch Alarm을 트리거해서 AutoScaling 그룹에 Scale 이벤트를 호출할 수 있다.
### ASG Health Checks
- 높은 가용성을 확신하기 위해선 최소 2개 인스턴스가 2개 AZ에서 실행되어야 합니다. (AZ가 죽을 경우에 대비)
- 헬스 체크
	- EC2 Status Checks
	- ELB Health Checks
	- Custom Health Checks: AWS CLI 또는 AWS SDK를 사용해서 인스턴스의 상태를 ASG에 송신합니다.
- ASG는 건강하지 않은(unhealthy) 인스턴스를 제거하고 새로운 인스턴스를 실행합니다.
- ASG는 건강하지 않은 인스턴스를 재시작(reboot)하지 않습니다.
- 알면 좋은 CLI
	- set-instance-health: custom health checks를 하는 데 사용됩니다.
	- terminate-instance-in-auto-scaling-group: 인스턴스를 ASG에서 제거합니다.
### Troubleshooting ASG Issues
- 이미 ASG에서 인스턴스들이 실행 중인데 새로운 인스턴스 실행을 실패한다.
	- 실행 중인 인스턴스 개수가 MaximumCapacity에 도달하여 새로운 인스턴스 실행이 불가하다.
- EC2 인스턴스 Launching 실패:
	- 보안 그룹이 삭제되었거나 존재하지 않는다.
	- key pair가 존재하지 않는다.
- 만약, ASG가 24시간 넘게 인스턴스 launching에 실패한다면 프로세스를 중단한다. (어드민 중단)
### CloudWatch Metrics for ASG
- 매트릭은 1분 마다 수집된다.
- ASG-level metrics: (opt-in)
	- GroupMinSize, GroupMaxSize, GroupDesiredCapacity
	- GroupInServiceInstances, GroupPendingInstances, GroupStandbyInstances
	- GroupTerminatingInstances, GroupTotalInstances
	- 위에 매트릭을 보기 위해선 매트릭 수집을 켜야한다.
- EC2-level metrics: (enabled): CPU Utilization, etc...
	- Basic Monitoring: 5분 주기
	- Detailed Monitoring: 1분 주기

---
## Auto Scaling Overview
![image.AJTA02.png](/img/user/image.AJTA02.png)
- AWS 확장 가능한 리소스들을 위한 중요 서비스

지원하는 서비스들은 다음과 같습니다.
- EC2 ASG
- EC2 Spot Fleet requests
- ECS
- DynamoDB: WCU(Write Capacity Units) & RCU(Read Capacity Units)
- Aurora: 동적으로 읽기 복제본 추가가 가능하다.
> [!NOTE]
> **WCU (Write Capacity Unit)**: DynamoDB 테이블에 데이터를 쓸 때의 처리량을 나타냅니다. 1 WCU는 초당 1KB 이하의 데이터를 하나의 아이템으로 기록할 수 있는 용량입니다. 예를 들어, 만약 테이블에 초당 10KB 크기의 데이터를 쓰려면 10 WCU가 필요합니다.
> **RCU (Read Capacity Unit)**: DynamoDB 테이블에서 데이터를 읽을 때의 처리량을 나타냅니다. 1 RCU는 초당 강력한 일관성(Strong Consistency) 읽기 기준으로 4KB 이하의 데이터를 하나의 아이템으로 읽을 수 있는 용량입니다. 만약 캐시된 데이터를 읽는 Eventually Consistent 방식이라면 1 RCU로 두 배인 8KB 데이터를 읽을 수 있습니다.
---

---
# 참고 자료