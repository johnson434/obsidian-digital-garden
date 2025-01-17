---
{"dg-publish":true,"permalink":"/06-full-notes/section-09-lambda-for-sys-ops/","dgPassFrontmatter":true}
---

# Tags
[[03 - Tags/Ultimate AWS Certified SysOps Administrator Associate 2024\|Ultimate AWS Certified SysOps Administrator Associate 2024]] 

---
# 단서 질문
- EC2 vs Lambda
    1. EC는 지속적으로 서버에서 돌아가지만 Lambda는 함수를 한 번 실행할뿐 서버를 점유하고 있지 않는다.
    2. Lambda는 시간 제한이 있다. (최대 30분이었나)
    3. Lambda는 오토스케일링이 자동으로 된다. (요청만큼 생성)
- Lambda의 CPU 스펙을 향상 시키는 방법은?
    
    RAM 사이즈를 키우면 CPU와 네트워크 성능이 알맞게 향상된다.
    
- Lambda는 어떻게 실행되는가?
    
    해당 코드가 실행될 컨테이너(JS면 NodeJS, Python이면 Python)에서 해당 코드가 실행됩니다.
    
- Lambda를 사용할 수 없는 경우는?
    
    함수 실행 시간이 15분 이상인 경우. (최대 시간 제한이 15분임)
    
- EventBridge란?
    
    EventBridge란 이벤트 버스 역할을 하며 서로 다른 AWS 서비스끼리 유기적으로 동작하게 만듭니다. 예를 들어, S3의 파일이 업로드 되면 EventBridge가 이 이벤트를 관찰하고 있다면 특정 행동(Lambda를 호출, SQS로 메시지 전달)이 트리거 됩니다.
    
- DynamoDB란?
    
    **DynamoDB**는 AWS에서 제공하는 **서버리스 기반 Key-Value NoSQL 데이터베이스**입니다.
    
    **DynamoDB**를 사용하면 높은 성능과 비용적인 측면에서 이점을 가져올 수 있습니다.
    
- AWS X-Ray란?
    
    분산 애플리케이션의 성능 추적을 위한 **서비스**로 함수 호출 시간, 실행에 걸리는 시간 등을 측정해서 CloudWatch에 제공한다.
    
- Lambda Execution Context란?
    
    Lambda 실행 환경(Runtime Environment)입니다. Lambda의 실행되기 전에 해당 함수가 다시 호출되면 WarmStart로 해당 Execution Context 재사용이 가능합니다.
    
- Lambda 함수를 처음 호출할 때 Latency를 줄이는 방법은?
    1. Lambda 함수를 주기적으로 실행해서 Cold Start를 방지
    2. **Provisioned Concurrency 설정을 통해 미리 Lambda를 예약해놓는다.**

---
# 핵심 요약
- 람다는 요청 시에 컨테이너를 생성 후에 함수를 실행한다.
- 함수가 실행을 끝마치고 종료되면 컨테이너가 제거됩니다.
- 컨테이너가 제거 되기 이전에 람다 함수를 다시 한 번 호출하면 해당 컨테이너를 제거하지 않고 해당 컨테이너를 재사용합니다.(Warm Start)
- 탑 레벨에 선언한 요소들은 다시 호출되지 않으며, 함수 내부만 다시 호출됩니다.
- 만약, 컨테이너가 제거 되었다면 다시 컨테이너를 재생성하므로 속도가 느립니다.(Cold Start)
- 이를 방지하기 위해 Provisioned Start로 람다를 미리 생성해놓는다.
---
# 핵심 필기
## Lambda - Overview
### Why AWS Lambda
**EC2**
- Cloud의 가성 서버다.
- RAM과 CPU가 제한된다.
- EC2는 서버가 계속 돌아가는 상태다.
- 스케일링은 서버의 개수를 조절하는 것
**Lambda**
- 가상 함수로 서버를 관리할 필요가 없다.
- 시간 제한이 있다. (15분)
- On-Demand로 실행된다.
- 스케일링이 자동이다. (EC2는 ASG 그룹을 통해서 지원)
### Benefits of AWS Lambda
- 쉬운 가격 정책
	- 리퀘스트와 Compute time으로 요금 청구
	- 프리 티어는 1,000,000 AWS Lambda 요청과 400,000 GBs(Lambda의 메모리 크기가 1GB라면 400,000초만큼 사용 가능) 컴퓨팅 타임을 제공한다.
- 많은 AWS 서비스와 통합해서 사용 가능하다.
- 많은 프로그래밍 언어와 통합해서 사용 가능하다.
- RAM 크기를 증가시키면 CPU와 네트워크도 향상된다.
### AWS Lambda Language support
**AWS Lambda 지원 언어**
- Python, Java 등 다양한 언어
- Lambda Container Image
	- Lambda Runtime API를 구현하는 Container Image를 사용 가능하다.
	- 단, 임의의 Docker Image 실행하는데는 보통 ECS/Fargate가 선호된다.
		- ECS는 EC2에 Container 실행하는 방식이고 Fargate는 컨테이너 자체를 실행하는 방식일거임.
### AWS Lambda Integrations Main ones
![Pasted image 20250117111650.png](/img/user/image/Pasted%20image%2020250117111650.png)
- 여기에는 안나왔는데 ALB에서 바로 Lambda 호출도 가능함. 정확하게는 Target Group(EC2, IP 주소들, Lambda 함수, ALB가 Target Group의 대상이 될 수 있음.)
### Example: Serverless Thumbnail creation
- AWS Lambda에서 Trigger를 액션에 따라 지정 가능하다. (예: PutObject, GetObject, All)
![Pasted image 20250117113146.png](/img/user/image/Pasted%20image%2020250117113146.png)
### Example: Serverless CRON Job
![Pasted image 20250117113424.png](/img/user/image/Pasted%20image%2020250117113424.png)
- EventBridge는 AWS의 리소스들을 이벤트를 통해 연결
### AWS Lambda Pricing: example
- 비용: https://aws.amazon.com/lambda/pricing/
- Lambda의 비용 정책은?
	- 호출 마다
		- 1,000,000회는 무료이며 이후부턴 백만 번마다 $0.20 비용이 든다.
	- 지속 시간으로
		- 한 달에 400,000 GB-seconds는 무료다.
		- 만약, 1GB면 400,000초 호출 가능
		- 만약, 128mb면 3,2000,000초
		- 무료 사용이 끝나면 600,000GB-seconds당 $1.00
> [!람다 비용 계산하기]
> 이 비용 계산법이 헷갈련던 이유가 둘이 통합해서 조합이 가능하다고 생각했다. 사실은 별개로 두 개의 요금을 내는 것이다. `람다 총 비용` = `컴퓨팅 요금` + `요청 요금`

---
## Lambda & CloudWatch Events / EventBridge
### CloudWatch Events / EventBridge
![Pasted image 20250117114425.png](/img/user/image/Pasted%20image%2020250117114425.png)
- CRON이나 EventBridge Rule을 설정해서 특정 주기로 Lambda 실행하기
- EventBridge Rule로 CodePipeline을 관찰하다가 변경되면 Lambda 실행하기

---
## Lambda & S3 Event Notifications
### S3 Events Notifications
- S3 Event Notifications는 대부분 수십초 이내에 이벤트를 전송하지만 가끔 그 이상으로 시간이 걸릴 수도 있다.
- 만약, Non-versioned object에 2개 이상 파일 쓰기 요청이 들어오면 이벤트가 한 개만 전달 될 수도 있다.
- 이러한 동일한 오브젝트에 쓰기 요청이 성공할 때마다 이벤트를 전달하고 싶으면 S3 Bucket에서 Object Versioning 옵션을 활성화해라. (파일의 버전이 생성됨)
### Simple S3 Event Pattern - Metadata Sync
![Pasted image 20250117115651.png](/img/user/image/Pasted%20image%2020250117115651.png)

---
## Lambda Permissions - IAM Roles & Resource Policies
### Lambda Execution Role (IAM Role)
- 람다에게 AWS 서비스나 리소스에 접근할 수 있는 권한을 부여
- 이벤트 소스 매핑을 사용하여 람다를 호출할 때 람다는 event data를 읽기 위해 Execution Role을 사용합니다.
- 제일 좋은 설정 방법은 Lambda 별로 Execution Role을 하나씩 주는 것입니다.
### Lambda Resource Based Policies
- 리소스 기반 정책을 통해서 다른 계정이나 AWS 서비스에서 람다를 사용할 수 있게 만들 수 있습니다.
- IAM Principal이 Lambda 함수에 접근하려면:
	- **IAM 정책**이 해당 Principal에게 attached 되어 있어 접근을 허용해야 하거나
	- **리소스 기반 정책**이 Lambda 함수에 대해 Principal(IAM User 등) 혹는 다른 AWS 서비스로부터의 호출을 허용해야 합니다.
		- AWS Service, AWS Account, Function URL을 통해서 리소스 기반 정책 부여가 가능
	- Lambda Permission 항목에 들어가면 Lambda가 수행할 Role이랑 리소스 기반 정책 설정하는 부분이 있음.
- S3와 같은 서비스들이 Lambda를 호출할 때는 리소스기반 정책을 통해서 S3가 Lambda를 호출할 수 있게 설정합니다.

---
## Lambda Monitoring & X-Ray Tracing
### Lambda Logging & Monitoring

- CloudWatch Logs
	- 람다 실행 로그는 **CloudWatch Logs**에 기록된다.
	- 람다가 로그를 작성하기 위해선 CloudWatch Execution Role이 있어야 한다.
		- AWS managed Policy에서 기본 Lambda 정책을 보면 logs 권한이 있음.
``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "*"
        }
    ]
}
```
- CloudWatch Metrics
	- 호출, 지속시간, 동시 실행, 에러 횟수, 성공 비율, Throttle이 포함된다.
	- Async Delivery Failures: 비동기 호출 실패
	- Iterator Age: 반복 주기

> [!important]  
> Throttle이란? 엔진 등의 과부하를 막기 위해서 출력을 조절하는 것을 스로틀링이라고 한다. AWS Lambda에선 여러 번 호출되는 함수를 특정 시간동안 함수의 호출을 멈춰서 함수의 실행 횟수를 조절한다.  

### Lambda Tracing with X-Ray
- X-Ray는 함수 호출 시간, 실행에 걸리는 시간 등을 측정하는 서비스다.
	- Lambda 설정에서 Active Tracing을 켜는 것으로 사용 가능
	- X-Ray SDK를 Lambda 코드 내에 작성해야 합니다.
- X-Ray와 커뮤니케이션하기 위환 환경 변수
	- `_X_AMZN_TRACE_ID`: 트레이싱 헤더를 포함함.
	- `AWS_XRAY_CONTENT_MISSING`: by default, LOG_ERROR
	- `AWS_XRAY_DAEMION_ADDRESS`: the X-Ray Daemon IP_ADDRESS:PORT

---
## Lambda Function Performance
### Lambda Function Configuration
- RAM
	- 128MB부터 10GB까지 1MB 단위로 늘릴 수 있다.
	- RAM의 크기를 늘리면 vCPU와 네트워크가 향상된다.
	- 1,792MB는 vCPU 1개와 동일하다.
	- 따라서, 만약 1,792MB 이상 램을 사용하면 멀티스레딩 사용이 효율적일 수 있다. (vCPU가 1코어 이상이면 동시에 한 개 이상의 스레드가 동작하는 게 효율적이니까. 코어당 하나의 실행 흐름을 가지니까)
- 시간제한: default는 3초 최대 15분
### Lambda Execution Context
- Execution Context는 람다 함수를 호출하는 런타임 환경입니다.
- DB 커넥션이나 HTTP Client 설정, SDK Client 설정을 한다.
- Lambda 함수 호출이 다시 될 것을 예상해서 환경의 값을 유지하기도 합니다.
- Lambda가 다시 호출되면 이전 Context를 재사용할 수 있습니다.
- Execution Context엔 /tmp 디렉터리가 포함됩니다.
### Initialize outside the handler
``` python
# 나쁜 예제 코드
# 함수가 호출될 때마다 커넥션을 생성한다.
import os
def get_user_handler(event, context):
	# 커넥션을 설정하는 부분이 함수 내부에 있다. 
	# Execution Context에 커넥션을 유지하고 싶으면 Top Level에 작성해야됨.
	DB_URL = os.getenv("DB_URL")
	db_client = db.connect(DB_URL)
	user = db_client.get(user_id = event["user_id"])
	
	return user
	
# 좋은 코드 예시
# DB 커넥션은 한 번만 이뤄지고 여러 호출에서 해당 커넥션을 이용한다.
import os
DATABASE_URL = os.getenv("DB_URL")
db_client = db.connect(DATABASE_URL)

def get_user_handler(event, context):
	return db_client.get(user_id = event["user_id"])
```
- Lambda를 사용할 때는 커넥션과 같은 유지되어야 할 설정들은 Top Level에 정의할 것
### Lambda Functions /tmp space
- 사용하는 상황:
	1. Lambda가 큰 파일을 다운로드 받을 필요가 있을 때
	2. 연산을 위해 디스크 공간이 필요할 때
- 최대 크기는 10GB
- 영구적인 저장소가 아니고 Execution Context가 frozen인 상태인 동안 남아있는다.
- 전이적 캐시(transient cache)를 제공하고 여러 호출에서 동시에 사용 가능하다. (Execution Context는 공유되니까 당연한 사실)
- /tmp 디렉터리를 암호화하기 위해서는 KMS Data Keys를 사용하세요

내 예상으로는 Lambda가 Container로 돌아가는 동안에 Execution Context는 해당 컨테이너에서 모두 공유하고 새로 들어오는 호출은 해당 컨테이너로 요청이 갈 것이다. 따라서, 컨테이너의 tmp를 모두가 공유가능한 것. -> 찾아보니까 firecracker라는 KVM을 이용하여 람다를 제공한다. Docker Image를 별개 옵션으로 제공하는 것 보면 별개의 내부 구현인덧.

---
## Lambda Concurrency
### Lambda Concurrency and Throttling
- **계정 당** 동시에 최대 1000개 실행 가능 (Lambda 함수 하나가 1000개가 아님)
- 함수 차원에서 "예약된 동시성"(reserved concurrency)를 설정할 수 있다.
- 제한을 넘는 호출은 Throttle을 호출한다.
- Throttle이 발생했을 때 처리 방식
	- Lambda 함수가 동기라면 ⇒ ThrottleError를 던진다.
	- Lambda 함수가 비동기라면 ⇒ SQS의 DLQ로 간다.
### Lambda Concurrency Issue
- 내 생각엔 Concurrency 예약은 필수다.
- 만약, Concurrency Limit를 넘는 요청이 들어오면 다른 유저들은 Throttle을 겪는다.
- 뉴스 피드백 서비스를 기준으로 생각해보자.
	- 람다 함수는 2가지다.
		- 뉴스 발행
		- 뉴스 조회
	- 위 상황에서 뉴스 발행이 엄청 많은 요청이 들어와서 Concurrency 1000개를 다 쓴다고 가정하자.
	- 뉴스 발행은 되는데 뉴스 조회는 Throttling이 일어난다.
![Pasted image 20250117130158.png](/img/user/image/Pasted%20image%2020250117130158.png)
### Concurrency and Asynchronous Invocations
- Throttling 에러(429)나 시스템 에러(500번대 에러들)로 실패한 람다 함수는 queue에 전달되어 실패한 람다함수는 최대 6시간동안 재시도된다.
- 재시도 주기는 1초부터 5분까지다. (재시도 주기는 점층적으로 늘어난다.)
### Cold Starts & Provisioned Concurrency
- Cold Start
	- 새로 인스턴스를 생성 => 코드가 메모리에 올라가고 handler 바깥 부분이 실행된다. (init)
	- 따라서, 첫번째 요청은 지연성이 크다.
- Warm Start
	- 람다 함수가 실행이 끝나기 이전에 재호출되는 경우에 handler 내부에 부분만 실행된다. (동일한 Execution Context를 사용하므로 /tmp 디렉터리도 공유)
- Provisioned Concurrency
	- 람다 함수가 호출되기 전에 사전에 람다를 준비시켜 놓는다.
	- 따라서, Cold Start는 발생하지 않으므로 낮은 Latency를 가진다.
	- Application Auto Scaling이 동시성을 관리할 수 있다. (스케쥴링이나 타겟 utilization에 따라서 관리)
- Note
	- VPC 내에서 Cold Start는 2019년 이후로 확 줄었다.
	- https://aws.amazon.com/blogs/compute/announcing-improved-vpc-networking-for-aws-lambda-functions/
### Reserved and Provisioned Concurrency
- Reserved Concurrency는 람다 함수 별로 동시성에 Limit 제한 설정
- Provisioned Concurrency는 Cold Start를 방지하기 위해 미리 Lambda 프로비저닝을 해놓는 것
---
## Lambda Monitoring - Extras
### Lambda Monitoring - CloudWatch Metrics
![Pasted image 20250117131643.png](/img/user/image/Pasted%20image%2020250117131643.png)

---
# 참고 자료