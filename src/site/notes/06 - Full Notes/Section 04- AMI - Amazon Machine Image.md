---
{"dg-publish":true,"permalink":"/06-full-notes/section-04-ami-amazon-machine-image/","dgPassFrontmatter":true}
---

# Tags
[[03 - Tags/Ultimate AWS Certified SysOps Administrator Associate 2024\|Ultimate AWS Certified SysOps Administrator Associate 2024]] 

---
# 단서 질문
- AMI가 뭐야?
    - Amazon Machine Image로 패키지가 이미 세팅(pre-packed)되었으므로 부팅 속도와 설정 속도가 빠릅니다.
- 인스턴스를 다른 AZ로 옮기는 방법
	1. EC2의 AMI 생성
	2. EC2 중단
	3. 이전할 AZ에서 AMI를 사용하여 EC2 생성
- AMI를 백업하는 방법
    1. AWS Backup을 사용
    2. EventBridge를 통해서 AWS Lambda를 트리거 → AWS Lambda가 AMI 이미지 백업 호출 → 백업 시작
- AWS Backup을 통해 AMI이미지 생성 단점
	- EC2 인스턴스를 중단하지 않고 이미지를 만드므로 데이터 무결성을 보장하지 않는다.
- EC2 Image Builder란?
    - 가상 머신이나 컨테이너를 위해 사용되는 이미지를 만드는 자동화 시스템
- EC2 Image Builder의 동작 방식
    - EC2 인스턴스를 생성 → 정의한 설정에 맞게 인스턴스를 초기화 → 해당 인스턴스로부터 AMI를 생성 → AMI 생성된 후에 인스턴스 종료 → 만들어진 AMI를 사용해서 인스턴스 생성 → 정상적으로 동작하는지 테스트

---
# 핵심 요약
- AMI(Amazon Machine Image)는 EC2 인스턴스가 사용할 수 있는 커스터마이징 이미지다.
- AMI는 리전에 종속된다.
- Reboot 옵션을 설정하지 않으면 인스턴스를 중지하지 않고 AMI를 만들 수 있다.
- 단, Reboot하지 않으면 파일 시스템 무결성을 보장하지 않는다.
- 인스턴스 AZ 변경은 AMI를 만들고 해당 이미지로 새로운 AZ에서 인스턴스를 생성하면 된다.
- EC2 Image Builder는 가상머신이나 컨테이너 이미지를 만들기 위해 사용되는 서비스다.
---
# 핵심 필기

## [CCP/SAA/DVA] AMI Overview
### AMI
- AMI = Amazon Machine Image
- AMI는 EC2 인스턴스의 커스터마이징이다.
	- 자신이 원하는 세팅을 미리 해놓는다. (e.g software, configuration, os, monitoring…)
	- 부팅 속도가 빠르고 설정이 금방 된다. AMI 내에 설정이 미리 되어 있으므로(pre-packed)
- AMI는 특정 리전에 종속된다. (리전 간의 복사 가능)
- EC2 인스턴스는 다음 이미지로부터 시작할 수 있다:
	- A Public AMI: AWS가 제공하는 공식 이미지
	- Your own AMI: 직접 만든 AMI
	- An AWS Marketplace AMI: 마켓에 등록된 ami
### AMI Process (from an EC2 instance)
- EC2를 시작한다.
- 세팅을 설정한다.
- EC2를 중단(Stop)한다. (this is for data integrity 데이터 무결성)
- AMI를 만든다. (This will also create EBS snapshots)
![image 39.png](/img/user/image/image%2039.png)
### AMI No-Reboot Option
![image 1 12.png](/img/user/image/image%201%2012.png)
- AMI를 끄지 않고 AMI를 만들 수 있다.
- No-Reboot Option은 기본적으로 활성화되지 않은 상태로 만들어진다.
### AWS Backup Plans to create AMI
- Plan 1: AWS BackUp
	- no-reboot 설정되어 있는 경우엔 EC2는 스냅샷을 찍을 때, 인스턴스가 종료된 상태가 아니므로 파일 시스템 무결성(File System Integrity)을 보장하지 못함.
	- 단점:
		- AWS Backup은 스냅샷을 찍기 위해서 CreateImage API를 호출하는데 이 때 기본적으로 `NoRebbot=True`로 파라미터를 넘긴다. (https://repost.aws/questions/QUQfmBbmcMTYuEJ3qvgAZqEQ/does-aws-backup-guarantees-no-reboot-to-ec2-instances
		- 따라서, AWS Backup 서비스는 EBS 스냅샷을 찍을 때 인스턴스를 재실행(reboot)하지 않는다.
		- 이는 데이터 무결성을 보장하지 않는 결과로 이뤄진다. (실행 중에 백업이 이뤄지므로)
- Plan 2: EventBridge + Lambda
	- EventBridgeRule을 통해 스냅샷 이벤트를 예약한다.
	- 이벤트가 호출되면 Lamda Function을 호출한다.
	- Lambda에선 CreateImage API를 호출하면서 매개변수로 `--reboot` 옵션을 넣는다.
	- 장점:
		- 데이터 무결성 보장
	- 단점:
		- 복잡성 증가
![Screenshot_from_2024-08-26_10-28-30.png](/img/user/image/Screenshot_from_2024-08-26_10-28-30.png)

### EC2 Instance Migration to difference AZ

![image 2 10.png](/img/user/image/image%202%2010.png)
- 앞에서 말했듯이 AMI는 Region에 종속되므로 AZ 간에 이동은 간단하다.
- 서로 다른 AZ 간에 이동은 아래와 같다.
	- 이동할 인스턴스로부터 AMI 이미지 생성
	- 이동할 인스턴스 제거
	- 다른 AZ에서 AMI 이미지로부터 인스턴스 생성
### Cross-Account AMI Sharing
![image 3 9.png](/img/user/image/image%203%209.png)
- AMI 공유는 계정 간에 공유 가능합니다.
- AMI를 공유해도 AMI 소유권(ownership)엔 영향 없습니다.
- 공유할 AMI는 EBS가 암호화되지 않았거나 CMK(Customer Managed Key)를 통해 암호화된 볼륨만 사용이 가능합니다.
- 암호화된 AMI를 공유하려면 암호화에 사용한 CMK도 공유가 필요합니다.
### AMI Sharing with KMS Encryption
![image 4 9.png](/img/user/image/image%204%209.png)
암호화되지 않은 볼륨을 사용한 AMI는 CMK를 사용할 IAM 권한을 타겟 계정에 부여해야 합니다.
부여할 권한

- kms: DescribeKey
- kms: CreateGrant
- kms: Decrypt
- kms: GenerateDataKey
- kms: ReEncrypt

### Cross-Account AMI Copy
![image 5 8.png](/img/user/image/image%205%208.png)
- 공유되는 AMI를 복사(copy)하면 복사한 AMI의 소유자가 됩니다.
- 복사를 하기 위해선 원본 AMI 소유권자가 해당 AMI가 저장된 EBS에 접근 권한을 줘야 합니다.
- 원본 AMI가 암호화되었다면 공유와 동일하게 CMK 키를 공유해야 합니다.
- 복사하는 동안에 자신만의 CMK 키로 다시 AMI를 재암호화할 수 있습니다.
![[image 6 8.png\|image 6 8.png]]
### [CCP] EC2 Image Builder
![image 7 6.png](/img/user/image/image%207%206.png)
- 컨테이너 이미지나 가상머신 생성의 자동화를 위한 서비스입니다.
- EC2 ImageBuilder는 특정 스케쥴(e.g weekly, 패키지가 업데이트 되었을 때, …)마다 인스턴스를 만들고 해당 인스턴스를 통해 AMI 이미지를 만듭니다.
- 또한, AMI 이미지가 만들어진 이후엔 인스턴스를 새로 만들어서 해당 이미지를 테스트합니다.
- 테스트가 끝나면 해당 이미지를 배포합니다. (여러 Region에 배포할 수 있습니다.)
### AMI in Production
- IAM 정책들(Policies)을 통해 유저가 특정 태그를 가진 AMI만 사용하게 강제할 수 있다.

![image 8 6.png](/img/user/image/image%208%206.png)
- AWS Config를 사용해서 Non-approve AMI를 사용한 인스턴스들을 찾을 수 있다.

![image 9 6.png](/img/user/image/image%209%206.png)

