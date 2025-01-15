---
{"dg-publish":true,"permalink":"/06-full-notes/section-08-cloudformation-for-sys-ops/","dgPassFrontmatter":true}
---

# Tags
[[03 - Tags/Ultimate AWS Certified SysOps Administrator Associate 2024\|Ultimate AWS Certified SysOps Administrator Associate 2024]] 

---
# 단서 질문

- CloudFormation이 뭐야?
    CloudFormation은 선언형 프로그래밍을 통해서 인프라를 정의하는 IaC입니다.
- CloudFormation 사용하기 위해선 template을 어떡하나?
    template을 S3 Bucket에 업로드하고 이후엔 해당 버킷을 참조하는 Stack을 CloudFormation에서 만듭니다. template은 삭제하고 재업로드하는 것이 아닌 업데이트를 통해 버저닝을 진행합니다.
- CloudFormation의 장점
    1. IaC
	    - 코드로 작성되므로 인프라 작성을 코드리뷰로 대체 가능
	    - 코드를 통해 생성되므로 코드를 통한 동작이 일관성 있다.
	2. 비용
	    - stack은 태그를 통한 식별자를 가지므로 해당 태그를 통해서 비용 측정 가능
	    - template 선언된 자원을 통해서 최종 비용 예상 가능
- CloudFormation의 Parameters란?
    - 입력 값을 AWS CloudFormation Template에 전달하기 위해 사용
- CloudFormation의 Mappings는?
    - Key, Value의 쌍으로 CloudFormation에 하드 코딩 되어 있음.
- CloudFormation의 Parameters와 Mappings의 차이점은?
    1. 작성 시점
	    - Parameters는 CloudFormation이 리소스를 만들 때
	    - Mappings는 CloudFormation의 Template을 작성할 때
    2. 동적 vs 정적
	    - Parameters는 동적으로 입력을 받을 수 있다.
	    - Mappings는 템플릿의 값을 사용
- Parameters와 Mapping의 사용 방법
    CloudFormation yaml 파일 내에 Mappings로 작성 후에 FindInMap Intrinsic Function인 FindInMap 함수로 조회
- CloudFormation에서 Create Stack 실패 시에 AWS에서 취하는 조치는?
    Create Stack 생성 실패 시에 기본 대처는 리소스 전체 삭제다. 만약에, 트러블 슈팅을 원할하게 진행하려면 리소스 전체 삭제 설정을 Disable하자. 혹은 단순하게 로그를 통해 해결해도 된다.
- CloudFormation에서 롤백 실패 시에 대응 조치는?
    리소스에 대해 조치를 취한 후에 CloudFormation에서 ContinueUpdateRollback API를 호출한다.
- CloudFormation에서 Outputs이란?
    다른 스택에서 만들어진 값을 사용하고 싶을 때, 해당 스택에서 값을 Export하고 Output을 통해 사용할 수 있다.
    ```JavaScript
    !ImportValue (사용할값)
    ```
- CloudFormation Capabilities
    CloudFormation에서 특정 리소스(IAM)를 생성하거나 Macro를 통해 동적으로 리소스를 구축하기 위해선 Capabilities를 부여합니다.
    **InsufficientCapabilitiesException**은 Capability가 없는 상태에서 템플릿에서 해당 권한이 필요한 리소스를 생성하면 발생하는 예외입니다.
- Deletion Policy란?
    클라우드 포메이션에서 리소스가 제거되었거나 Template 파일이 삭제됐을 때, 리소스들을 어떻게 처리할지 정하는 정책. (ex: Delete, Retain, Snapshot)
- Stack Policy란?
    스택에 포함된 리소스가 업데이트 될 때 생략할지 업데이트할지 정하는 정책
- cfn-init과 cfn-signal이란?
    cfn-init: CloudFormation init으로 EC2에서 CloudFormation으로부터 받는 스크립트다.
    cfn-signal: cfn-init 결과를 CloudFormation에 전송하는 것
---
# 핵심 요약
• CloudFormation은 AWS 리소스를 코드로 관리하는 IaC(Infrastructure as Code) 서비스이다.
• 템플릿을 통해 리소스를 선언적으로 정의하고, 스택으로 리소스 그룹을 관리한다.
• cfn-init, cfn-signal, WaitCondition 등을 통해 인스턴스 초기 설정과 상태 확인이 가능하다.
• 중첩 스택(Nested Stacks)을 사용해 공통 컴포넌트를 재사용할 수 있다.
• Cross Stacks를 통해 스택 간 값을 공유하고, 다른 라이프사이클을 가진 스택을 관리할 수 있다.
• StackSets를 사용하여 여러 계정과 리전에 걸쳐 스택을 배포하고 관리할 수 있다.
• 트러블슈팅 시 로그 확인, 권한 검증, 리소스 제한 확인 등이 중요하다.
• CloudFormation의 DependsOn 속성을 사용하여 리소스 간의 종속성을 명시적으로 정의할 수 있다.
• CloudFormation Designer를 활용하면 시각적으로 템플릿을 생성하고 수정할 수 있어 복잡한 아키텍처 설계가 용이하다.
• 변경 세트(Change Sets)를 통해 스택 업데이트 전 변경 사항을 미리 검토하고 영향을 평가할 수 있다.

---
# 핵심 필기
## [DVA] Cloudformation - Overview
### AWS CloudFormation
- CloudFormation은 선언적으로 AWS 인프라를 정의하는 방법
- 선언하면 해당 리소스들을 순서와 설정에 따라서 자원들을 만들어줍니다.
### CloudFormation - Template Example
![image 1 17.png](/img/user/image/image%201%2017.png)
### Benefits of AWS CloudFormation(1/2)
- IaC
	- 수동으로 리소스를 만들지 않으므로 컨트롤이 편리하다.
	- Git을 통해 버전 관리 가능하다.
	- 변경 사항을 코드로 리뷰 할 수 있다.
- 비용
	- stack에 있는 리소스들은 태그가 되어 있으므로 태그를 통해 비용을 볼 수 있따.
	- CloudFormation Template을 통해 리소스를 만드는데 이 Template으로 예산을 추정할 수 있다.
	- 비용 절감 전략: 특정 시간에 자동 생성 및 자동 삭제가 가능하다.
### Benefits of AWS CloudFormation(2/2)
- 생산성
	- 템플릿에서 다이어그램을 자동으로 생성해준다.
	- 선언형 프로그래밍 (순서와 조율을 신경 쓸 필요 없다.)
- 관심사 분리: 앱과 계층을 스택 별로 분리가 가능하다.
	- 예: VPC Stacks, EKS Stacks, Network Stacks처럼 스택으로 모듈화가 가능하다.
- 바퀴를 발명하지 마라
	- 웹에 template이 있다.
	- 다큐먼트에 의존해라
### How CloudFormation Works
![Pasted image 20250115164633.png](/img/user/image/Pasted%20image%2020250115164633.png)
- S3에 템플릿 업로드 후 CloudFormation에서 참조
- 파일 수정 불가하며 새로운 버전을 업로드하는 것만 가능.
- Stack은 이름으로 식별
- Stack을 삭제하면 CloudFormation으로 만든 모든 요소들이 삭제됨.
### Deploying CloudFormation Templates
- Manual way (수동 방식)
	- Application Composer나 code Editodr로 수정하기
	- 콘솔을 통해서 파라미터 넣기, 기타 등등...
- Automated way (자동화 방식)
	- 템플릿을 YAML 파일에서 수정
	- AWS CLI나 Continuous Delivery (CD) tool을 사용해서 배포하기
	- 모든 작업을 자동화하고 싶을 때 추천하는 방식

---
### CloudFormation - Building Blocks
- 템플릿 구성 요소
	- AWSTemplateFormatVersion: 템플릿 버전 명시 “2010-09-09”
	- Description: 템플릿 설명
	- Resources: 사용할 리소스
	- Parameters: 템플릿을 위한 동적 입력값
	- Mappings: 템플릿을 위한 정적 변수들
	- Outputs: 다른 리소스에서 만들어진 값을 참조할 때 사용 (만든 사람은 Export)
	- Conditionals: 리소스 생산을 위한 조건문
- Template’s 도우미
	- References
	- Functions
---
## [DVA] YAML Crash Course
### YAML Crash Course
![image.UWLM02.png](/img/user/image/image.UWLM02.png)
---
## [DVA] CloudFormation - Resources
- 리소스들은 CloudFormation template의 필수 항목
- 만들어지는 AWS 컴포넌트들을 나타낸다.
- 리소스들은 정의되며 서로 참조 가능하다.

- AWS가 리소스의 생성, 업데이트, 삭제를 알아낸다.
- 리소스 타입은 700개가 넘는다.
- 리소스 식별자 형태:
	- service-provider::service-name::data-type-name
### CloudFormation - Resources
- 동적 개수만큼 리소스를 만들 수 있나?
	- 가능하다. CloudFormation Macros와 Transform을 사용.
	- 시험이랑 관련 없으므로 스킵
- 모든 리소스를 지원하나?
	- 지원 안하는 애들 있음.
	- 그런 애들은 커스텀 리소스 쓰면 됨.
---
## [DVA] CloudFormation - Parameters
### CloudFormation - Parameters
- CloudFormation의 입력 값을 전달하기 위해 사용
- 언제 쓰냐?
- 입력 값을 통한 템플릿 재사용
- 템플릿에 사용될 값이 동적으로 적용되어야 할 때
- 입력을 주는 방법 AWS CLI나 AWS System Manager를 통해서
![Pasted image 20250115170927.png](/img/user/image/Pasted%20image%2020250115170927.png)
### When should you use a Parameter?
``` yaml
Parameters:
	SecurityGroupDescription:
		Description: Security Description
		Type: String
```
- 만약, 해당 값이 가변적이라면 Parameter로 사용하셈.
- Parameter를 사용하면 특정 값을 바꿀 때, Template을 재업로드 하지 않아도 된다는 장점이 있음.
### CloudFormation - Parameters Settings
- Type:
	- String
	- Number
	- CommaDelimitedList
	- List\<Number\>
	- AWS-Specific Parameter (to help catch invalid values – match against existing values in the AWS account)
	- List\<AWS-Specific Parameter\>
	- SSM Parameter (get parameter value from SSM Parameter store)
	- Description
	- ConstraintDescription (String)
	- Min/MaxLength
	- Min/MaxValue
	- Default
	- AllowedValues (array)
	- AllowedPattern (regex)
	- NoEcho (Boolean)
### CloudFormation - Parameters Example
![image 16 6.png](/img/user/image/image%2016%206.png)
### How to Reference a Parameter?
![image 17 5.png](/img/user/image/image%2017%205.png)
### CloudFormation - Pseudo Parameters
- AWS에서 제공하는 파라미터로 모든 템플릿에서 사용 가능하다.

- 종류
	- AWS::AccountId
	- AWS:Region
	- AWS:StackId
	- AWS:StackName
	- AWS::NotificationARNs
	- AWS: NoValue
## [DVA] CloudFormation - Mappings

![[image 19 5.png\|image 19 5.png]]

- Mappings는 CloudFormation과 같이 작성되어 있음. (Parameters와 같이 입력 값을 받는 게 아니다)
- 서로 다른 환경에서 편리하다.
- 모든 값은 Template에 하드코딩되어 있음

![[image 20 5.png\|image 20 5.png]]



![[image 21 5.png\|image 21 5.png]]



- [DVA] CloudFormation - Outputs & Exports

![[image 22 4.png\|image 22 4.png]]

- Output은 선택적 출력 값들을 선언한다. 이를 다른 Stack에서 사용할 수 있다.
- Outputs를 AWS Console이나 AWS CLI를 통해 사용 가능합니다.
- 네트워크를 CloudFormation으로 정의하고 Output을 통해 VPC IP나 Subnet ID를 사용할 수 있다.
- 스택끼리 협동하기 위해 가장 좋은 방법입니다.

![[image 23 4.png\|image 23 4.png]]



![[image 24 4.png\|image 24 4.png]]



- [DVA] CloudFormation - Conditions
- CloudFormation - Conditions

![[image 25 4.png\|image 25 4.png]]

- 조건에 따라서 리소스 생성이나 Outputs를 조절한다.
- 어떤 것도 조건이 될 수 있지만 다음 3가지가 일반적인 사용 방법이다.
- 환경 (dev / test / prod)
- AWS Region
- Parameter Value
- Condition은 다른 Condition이나 parameter value, mapping을 참조할 수 있다.
- How to define a Condition

![[image 26 4.png\|image 26 4.png]]



- How to use a Condition

![[image 27 4.png\|image 27 4.png]]

- Condition은 resource / outputs 기타 등등 다양한 곳에 적용 가능하다.
- [DVA] CloudFormation - Intrinsic Functions
- CloudFormation - Intrinsic Functions

![[image 28 2.png\|image 28 2.png]]



- Intrinsic Functions
- Fn::Ref

![[image 29 2.png\|image 29 2.png]]

Fn::Ref

- Parameters에 사용하면 Parameter 값을 가져온다.
- Resources에 사용하면 Resource ID를 가져온다.
- Fn:GetAtt

![[image 30 2.png\|image 30 2.png]]

Fn:GetAtt

- Attribute는 모든 리소스와 결합되어 있다.
- Attribute를 확인하려면 AWS 공식 문서를 봐라
- !GetAtt EC2Instance.AvailabilityZone은 EC2 인스턴스의 AZ를 가져온다.
- FindInMap

![[image 31 2.png\|image 31 2.png]]

Fn::FindInMap

- Mappings에서 Key 값을 통해서 값을 조회 가능
- Fn::ImportValue

![[image 32 2.png\|image 32 2.png]]

**Fn::ImportValue**

- 다른 스택에서 Export 된 값을 Import 할 수 있다.
- Fn::Base64

![[image 33 2.png\|image 33 2.png]]

**Fn::Base64**

- String을 Base64로 변경한다.
- Fn::Condition Functions

![[image 34 2.png\|image 34 2.png]]

	

- [DVA] CloudFormation - Rollbacks

![[image 35 2.png\|image 35 2.png]]

**Rollbacks**

- Stack 생성 실패 시:
- 기본 설정: 모든 게 롤백 된다.
- 롤백 설정을 꺼서 트러블 슈팅을 할 수 있다.
- Stack 업데이트 실패 시:
- 이전 상태로 돌아간다.
- 로그로 에러 확인 가능
- 롤백 실패 시 대처 방법은?
- ContinueUpdateRollback API를 호출
- [DVA] CloudFormation - Service Role

![[image 36 2.png\|image 36 2.png]]

**Service Role**

- CloudFormation이 User 액션에 따라 스택 생성/업데이트/삭제를 할 수 있게 허용한다.
- CloudFormation으로 만드려는 해당 리소스 권한을 추가한다.

```JavaScript
// CloudFormation의 자격증명기반정책
{
"Version": "2012-10-17",
"Statement": [
{
"Effect": "Allow",
"Action": [
"s3:*Bucket"
],
"Resource": "*"
}
]
}
```

- 언제쓰냐?
- 최소한의 권한을 주고 싶을 때

**Service Role 내가 이해한 내용**

- Stack을 만드는 유저는 iam:PassRole이랑 CloudFormation의 권한이 있어야한다.
- CloudFormation은 자격증명기반정책으로 CloudFormation 템플릿을 통해 만들 리소스에 대한 권한이 있어야 한다.

- 자격증명기반 vs 리소스 기반

### 1. **자격 기반 정책 (Identity-Based Policy)**

- **적용 대상**: IAM 사용자(User), IAM 그룹(Group), 또는 IAM 역할(Role).
- **사용 방법**: 특정 유저, 그룹, 역할에게 어떤 리소스에 접근할 수 있는지, 그리고 어떤 작업(예: EC2 인스턴스 시작, S3 버킷 읽기 등)을 수행할 수 있는지를 정의합니다.
- **예시 상황**: "이 IAM 역할이 모든 S3 버킷에 접근할 수 있도록 설정"하는 경우 자격 기반 정책을 사용합니다.

**예시: IAM 역할에 적용된 자격 기반 정책**

```JSON
json
코드 복사
{
"Version": "2012-10-17",
"Statement": [
{
	"Effect": "Allow",
	"Action": "s3:ListBucket",
	"Resource": "arn:aws:s3:::*"
}
]
}

```

위 정책은 **IAM 역할**이 모든 S3 버킷을 나열할 수 있는 권한을 부여합니다. 여기서 중요한 점은 정책이 **IAM 역할**에 부착된다는 것입니다.

### 2. **리소스 기반 정책 (Resource-Based Policy)**

- **적용 대상**: AWS 리소스 자체에 부착됩니다. S3 버킷, SQS 큐, SNS 주제, KMS 키 등이 리소스 기반 정책을 사용할 수 있습니다.
- **사용 방법**: 특정 리소스에 대해 어떤 엔터티(IAM 사용자, 역할, 또는 AWS 서비스)가 해당 리소스에 접근할 수 있는지를 정의합니다.
- **예시 상황**: "이 S3 버킷에 특정 IAM 역할만 접근할 수 있도록 설정"하는 경우 리소스 기반 정책을 사용합니다.

**예시: S3 버킷에 적용된 리소스 기반 정책**

```JSON
json
코드 복사
{
"Version": "2012-10-17",
"Statement": [
{
	"Effect": "Allow",
	"Principal": {
		"AWS": "arn:aws:iam::123456789012:role/SomeIAMRole"
	},
	"Action": "s3:GetObject",
	"Resource": "arn:aws:s3:::example-bucket/*"
}
]
}

```

위 정책은 **S3 버킷**에 부착된 리소스 기반 정책으로, 특정 IAM 역할(`SomeIAMRole`)만 해당 S3 버킷의 객체를 읽을 수 있는 권한을 부여합니다. 여기서 정책이 S3 버킷 리소스에 부착된다는 점이 핵심입니다.

### 3. **차이점 요약**

- **자격 기반 정책**: IAM 엔터티(사용자, 그룹, 역할)에게 권한을 부여합니다. 예를 들어, "이 IAM 역할은 S3 버킷에 접근할 수 있다"와 같은 형태로, 정책은 **유저나 역할에 적용**됩니다.
- **리소스 기반 정책**: 리소스(S3 버킷, SNS 주제, SQS 큐 등) 자체에 부착됩니다. 리소스가 어떤 엔터티에게 접근 권한을 부여하는지를 정의합니다. 예를 들어, "이 S3 버킷은 특정 IAM 역할이 접근할 수 있다"와 같은 형태로, 정책은 **개별 리소스에 적용**됩니다.

### 4. **적용 예시**

### 자격 기반 정책

- 특정 IAM 역할에게 EC2 인스턴스를 시작하고 종료할 수 있는 권한을 부여하려면, **자격 기반 정책**을 해당 IAM 역할에 부착합니다.

### 리소스 기반 정책

- 특정 IAM 역할이 **S3 버킷**에만 접근할 수 있도록 하려면, S3 버킷에 **리소스 기반 정책**을 부착하여 해당 IAM 역할만 접근할 수 있도록 제한합니다.

### 정리

- **자격 기반 정책**은 **IAM 사용자, 그룹, 역할**에 적용되는 정책입니다.
- **리소스 기반 정책**은 **AWS 리소스 자체(S3, SNS, SQS 등)**에 적용되며, 특정 엔터티가 해당 리소스에 어떻게 접근할 수 있는지를 제어합니다.

이 두 정책을 적절하게 사용하면, 권한 관리에서 보다 정교한 통제가 가능합니다.

- [DVA] CloudFormation - Capabilities

![[image 37 2.png\|image 37 2.png]]

**Capabilities (능력들)**

- **CAPABILITY_NAMED_IAM, CAPABILITY_IAM**
- CloudFormation이 IAM 리소스를 만들거나 업데이트할 때 필요하다. (IAM 유저, 역할, 정책 등)
- 리소스의 이름을 지을 거면 CAPABILITY_NAMED_IAM을 써라
- **CAPABILITY_AUTO_EXPAND**
- CloudFormation이 Macros나 Nested Stack가 있으면 사용해야 한다.
- **InsufficientCapabilitiesException**
- 만약, Capabilities가 없으면 해당 에러가 난다.

```Bash
# 예시
aws cloudformation \
create-stack \
--stack-name message-store \
--template-body file://bucket_with_keys.yaml \
--parameters file://cfg_bucket_with_keys.json \
--capabilities CAPABILITY_NAMED_IAM
```

- [DVA] CloudFormation - Deletion Policy

Deletion Policy란?

- CloudFormation Template이 삭제되었거나 리소스가 CloudFormation Template에서 삭제되었을 때 조절하는 정책
- Extra Safety Measure(추가적인 보안 조치)로 리소스를 보존하고 백업한다.

- DeletionPolicy Delete

![[image 38 2.png\|image 38 2.png]]

- 기본 삭제 정책은 Delete다.



- DeletionPolicy Retain

![[image 39 2.png\|image 39 2.png]]

- 유지하는 정책
- DeletionPolicy Snapshot

![[image 40 2.png\|image 40 2.png]]

- 삭제하기 전에 스냅샷을 찍는다.
- 가능한 리소스:
- EBS Volume
- ElaticCache Cluster, ElasticCache ReplicationGroup
- RDS DB Instance
		- RDS DB Cluster
		- Redshift Cluster
		- Neptune DBCluster
		- DocumentDB DBCluster
- [DVA] CloudFormation - Stack Policy

![[image 41 2.png\|image 41 2.png]]

- 업데이트가 일어나도 되는 리소스를 정의한다.
- Stack Policy를 작성하면 디폴트 값으로 모든 리소스가 보호된다.
- Stack Policy를 작성하면 업데이트를 원하는 리소스엔 무조건 Allow를 추가하라.
- [DVA] CloudFormation - Termination Protection

![[image 42 2.png\|image 42 2.png]]

Termanation Protection을 통해 Stack 삭제를 방지할 수 있다.

- [DVA] CloudFormation - Custom Resources

![[image 43 2.png\|image 43 2.png]]

- CloudFormation에서 아직 지원하지 않는 리소스를 사용하기 위해서 정의하는 것

![[image 44 2.png\|image 44 2.png]]



> [!important]  
> ARN이란? Amazon Resource Name으로 아마존 리소스의 고유한 식별자  

![[image 45.png\|image 45.png]]



- [DVA] CloudFormation - Dynamic References
- Dynamic References

![[image 46.png\|image 46.png]]

- SSM이나 Secrets Manager의 값을 CloudFormation 내에서 사용합니다.
- create/update/delete 오퍼레이션 중에 값들을 가져올 수 있습니다.
- 예: 템플릿을 통해서 RDS를 만들 때, SM에서 유저의 패스워드를 가져올 수 있다.
- 지원 예:
- ssm - Plain text
- ssm-secure - SSM에서 암호화된 String
- secretsmanager - Secrets Manager에 저장된 값



![[image 47.png\|image 47.png]]

- AWS System Manager vs AWS Secrets Manager
- **비용**: SSM Parameter Store는 표준 파라미터에 대한 무료 제공이 있어 소규모 프로젝트나 간단한 설정 관리에 유리합니다. 반면, Secrets Manager는 자동 비밀번호 갱신 기능을 제공하므로 보안이 중요한 애플리케이션에 적합하지만 비용이 더 높습니다.
- **용량**: Secrets Manager는 하나의 비밀에 대해 최대 64KB를 지원하며, Parameter Store는 최대 8KB까지만 저장할 수 있습니다.
- **기능**: Secrets Manager는 자동 비밀 교체 및 더 높은 보안 관리 기능을 제공하는 반면, SSM Parameter Store는 더 단순한 설정 정보 저장을 위한 서비스입니다.
- Secrets Manager & RDS Setting

![[image 48.png\|image 48.png]]



![[image 49.png\|image 49.png]]

- CloudFormation - User Data
- User Data in EC2 for CloudFormation

![[image 50.png\|image 50.png]]

- Fn::Base64로 초기화 스크립트를 EC2에 전달할 수 있다.
- User Data 스크립트 로그는 /var/log/cloud-init-output.log에 작성된다.
- The Problems with EC2 User Data

![[image 51.png\|image 51.png]]

- 설정 파일이 엄청 클 때는 어떻게할까?
- 인스턴스를 프로비저닝 과정을 거치지 않고 실행 중인 인스턴스들은 어떻게 UserData를 실행할까?
- 어떻게 user-data를 더 읽기 쉽게 만들까
- 스크립트 성공 여부는 어떻게 파악할까?



- 이를 해결하는 것이 CloudFormation Helper Scripts
- cfn-init, cfn-signal 등 여러 설정 파일 스크립트를 제공한다.
- CloudFormation - cfn-init

![[image 52.png\|image 52.png]]

- 유저명, 그룹, 패키지 등 인스턴스에 초기 설정에 필요한 내용들을 YAML 파일에 작성한다.

![[image 53.png\|image 53.png]]

- 리소스를 획득하고 리소스 메타데이터를 해석하는데 사용된다.
- EC2 인스턴스가 CloudFormation에 cfn-init 스크립트를 요청하고 받은 후에 실행한다.
- 로그는 /var/log/cfn-init.log에 작성된다.
- CloudFormation - cfn-signal & Wait Condition

![[image 54.png\|image 54.png]]

- cfn-signal을 통해서 cfn-init 설정 결과를 CloudFormation으로 보낼 수 있습니다.
- cfn-signal을 위해선 WaitCondition을 작성해야 한다.
- 템플릿 동작을 명시한 WaitCondition이 끝나기 전까지 Block한다.
- WaitCondition엔 Creation Policy를 명시한다. (EC2와 ASG에서도 동작한다.)
- Count를 사용하여 WaitCondition이 끝나는 조건인 cfn-signal 수신 개수를 명시한다.
- CloudFormation - cfn-signal Failures

![[image 55.png\|image 55.png]]

- cfn-init과 cfn-signal이 정상적으로 동작했는지 /var/log/cloud-init.log나 /var/log/cfn-init.log에서 확인 가능합니다.
- 단, CloudFormation의 실패 시에 롤백 기능을 꺼야 합니다.
- 인스턴스가 인터넷과 연결됐는지 확인하세요.
- CloudFormation - Nested Stacks
- Nested Stacks

![[image 56.png\|image 56.png]]

- 중첩 스택은 스택 내에서 또 다른 스택의 스택 역할을 합니다.
- 반복되는 패턴과 공통 컴포넌트를 격리화하여 또 다른 스택에서 격리된 컴포넌트들을 호출합니다.
- 중첩 스택을 업데이트하기 위해선 root stack을 업데이트해야 합니다.
- 여러 스택을 사용하는 방법 두 가지 Cross Stacks vs Nested Stacks

![[image 57.png\|image 57.png]]

- **Cross Stacks**
- 스택들이 서로 다른 라이프 사이클을 가질 때 도움이 된다.
- Outputs Export를 통해 스택에서 값을 출력하고 ImportValue를 통해 값을 입력 받는다.
- VPC Id와 같은 값들을 여러 스택에 전달할 필요가 있을 때 사용
- Nested Stacks
- 동일한 설정의 component를 재사용할 때 도움된다. (동일한 오브젝트를 사용하는 게 아니라 동일한 설정을 가진 Component를 만들어서 사용하는 것)
- 예: ALB을 어떻게 적절하게 설정할지 재사용
- 중첩 스택은 상위 스택에만 중요합니다. (모든 중첩 스택들은 상위 스택에 의해서 관리된다.)
- CloudFormation - Depends On
- DependsOn

![[image 58.png\|image 58.png]]

- 특정 리소스가 만들어진 후에 리소스를 만들기 위해 사용
- GetAtt나 Ref를 사용하면 자동으로 해당 리소스의 DependOn하는 효과를 발휘한다.
- CloudFormation - StackSets
- StackSets

![[image 59.png\|image 59.png]]

- StackSets를 사용해서 여러 계정, Region의 Stack을 한 번에 수정할 수 있다.
- Administrator 계정을 사용해서 StackSets를 만들 수 있다.
- StackSets를 업데이트하면 모든 연결된 Stack들이 업데이트된다.
- StackSets Permission Models

![[image 60.png\|image 60.png]]

**StackSets 권한 모델(Permission Models)**

- Self-Managed Permissions (AWS Organization을 안 쓰는 경우)
- Administrator와 타겟 유저 계정에 IAM 역할(Administration Role, Execution Role)을 만든다.
- 위처럼 역할을 가졌다면 해당 계정에 Stack을 만들 수 있습니다.
- Service-managed Permissions(AWS Organization을 쓰는 경우)
- AWS Organization을 통해 관리되는 계정들에 배포합니다.
- AWS Organization에 **trusted access**를 활성화하면 StackSets는 알아서 IAM 역할을 만듭니다.
- **반드시** AWS Organizations에서 모든 기능을 Enable해야 합니다.



- StackSets with AWS Organizations

![[image 61.png\|image 61.png]]

- 새로운 계정이 Organization에 추가되면 자동으로 해당 스택도 배포한다.
- StackSet 어드민 권한을 Organization에 위임 가능하다.
- Trusted Access가 반드시 활성화 되어 있어야 한다.

![[image 62.png\|image 62.png]]



- Hands-On: StackSets

![[image 63.png\|image 63.png]]



- **Troubleshooting**
- Troubleshooting

![[image 64.png\|image 64.png]]

**CloudFormation 트러블 슈팅**

- DELETE_FAILED
		- S3 같은 애들은 먼저 내용물(버킷)이 비어야 한다.
				- Lambda로 버킷 내용 비우기를 자동화해라
		- Security Group을 삭제하기 전에 먼저 모든 EC2를 삭제해야 한다.
		- 특정 리소스 삭제가 계속 실패한다면 DeletionPolicy를 Retain으로 둬서 스킵할 수도 있다. → 수동 삭제해야함
- UPDATE_ROLLBACK_FAILED(업데이트에 실패하고 롤백을 하려했지만 롤백도 실패한 경우)
		- CloudFormation 외부에서 문제가 발생한 경우, 권한이 부족한 경우, ASG가 충분한 Signal을 받지 못한 경우
		- 수동으로 고치고 ContinueUpdateRollback을 실행하시오.(롤백을 마저 진행함)
- StackSet Troubleshooting

![[image 65.png\|image 65.png]]

**CloudFormation - StackSet Troubleshooting**

- 스택이 작업에 실패했고, 스택 인스턴스의 상태가 OUTDATED면
		- 타겟 계정의 충분한 권한이 없어서 리소스를 만들 수 없는 경우
		- 글로벌 리소스라서 Unique해야 하는데 Unique하지 않게 명시해서(예: S3명)
		- Admin 계정과 target 계정이 trust Relation이 없어서(Admin Role, Execution Role)
		- Target 계정의 리소스가 너무 많아서 Limit를 초과한 경우
---
# 참고 자료
