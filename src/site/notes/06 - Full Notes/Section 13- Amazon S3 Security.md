---
{"dg-publish":true,"permalink":"/06-full-notes/section-13-amazon-s3-security/","dgPassFrontmatter":true}
---

# Tags
- [[03 - Tags/Ultimate AWS Certified SysOps Administrator Associate 2024\|Ultimate AWS Certified SysOps Administrator Associate 2024]] 

---
# 단서 질문
- 회사 규정 상 모든 저장소는 회사에서 설정한 키를 기반으로 암호화가 되어야 한다. 그렇다면 S3의 암호화는 어떤 식으로 설정해야 할까?
	- KMS에 사용자가 입력 받은 키를 기준으로 암호화를 하도록 설정
	- S3의 암호화 방식은 SSE-KMS를 KMS의 키를 사용하여 해당 키를 기반으로 암호화 및 복호화
- `test` 버킷은 Default 암호화 옵션으로 SSE-S3인 상태다. 이 상태에서 클라이언트가 암호화 옵션 없이 Object 업로드를 시도했지만 요청이 반려당했다. 이유가 뭘까? Bucket 정책은 SSE-S3 암호화 된 Object만 허용한다.
	- User가 접근 권한이 없다.
	- Default 암호화 옵션이 활성화된 상태더라도 Bucket 정책이 먼저 평가되므로 암호화 조건이 없는 요청으로 판단하고 요청을 반려한다.
- 버켓이 Region 마다 1개씩 있는 상태이다. 클라이언트의 요청이 들어오면 가장 빠른 버켓으로부터 응답을 받고 싶으면 어떻게 할까?
	- MultiRegion Access Point를 사용한다. 단, Region별로 최대 버켓은 1개만 사용 가능하다.
- Bucket의 Key 기반으로 접근 권한을 나누고 싶다. 무슨 방법이 좋을까?
	1. Bucket Policy를 통해 Resource별로 Principal을 별개로 준다.
	2. Access Point를 통해서 Key 기반으로 Access Point 정책을 설정한다.
---
# 핵심 요약
- S3 트러블 슈팅 꿀팁: S3 작업에 대해서 거부되면 IAM 기반 정책에서 거부됐는지 리소스 기반 정책에서 거부했는지 먼저 확인할 것
- S3 암호화는 4가지 방법이 있다.
	- SSE-S3
		- AWS에서 키를 관리한다.
		- 단, 사용자는 해당 키를 관리할 수 없다.
	- SSE-KMS
		- AWS의 KMS 서비스에서 키를 관리한다.
		- 따라서, KMS 서비스를 통해서 키를 관리할 수 있다.
		- KMS 서비스에 초당 요청 횟수 제한에 의해 어플리케이션에 영향을 끼칠 수 있다. (S3의 GET/HEAD는 PATH별로 초당 5500)
	- SSE-C
		- Client에서 Key를 넘긴다.
		- HTTPS 통신이 필요하다. (통신이 암호화되지 않으면 Key가 노출 가능성 있음)
		- S3에선 해당 키를 저장하지 않고 단순히 키를 가지고 암호화만 한다.
	- CSE
		- Client-Side Encryption으로 암호화를 한 이후에 데이터를 S3에 넘긴다.
- Bucket Policy
	- 특정 버킷의 정책을 설정 가능하다.
	- 특정 액션을 제한할 수 있다. (Condition을 통해 sse-algorithm이 AES256이 아니면 Put을 허용하지 않는다든가)
- Vault & Object Lock
	- Vault는 Glacier의 저장소로 WORM 모델이다.
	- 한 번 작성하면 수정이나 삭제가 불가능하다.
	- Object Lock은 특정 Object에 WORM 모델을 적용할 수 있다.
- Bucket Access Logs
	- 특정 Bucket에 일어나는 모든 액션을 로그로 남겨서 저장할 수 있는 Bucket이다.
	- Athena를 통해 검색 가능하다. (Athena는 서버리스 쿼리 서비스)
- Pre-signed URL
	- 사전에 사인된 URL로 해당 URL을 통해서 아무런 권한이 없어도 접근이 가능하다.
- Access Point
	- 버켓의 보안 목적으로 사용된다.
	- 특정 Access Point의 정책을 통해서 IAM을 세밀하게 나눌 수 있다.
		- Bucket의 `finance` 프리픽스에 속한 Object들을 Access Point로 분리하고, 해당 Access Point에 특정 IAM 역할을 가진 사용자만 접근할 수 있도록 설정할 수 있다.
- Multi Region Access Point
	- 여러 리전의 Bucket의 접근 포인트다.
	- 요청이 들어오면 Latency와 혼잡도가 낮은 곳으로 라우팅을 한다.
	- failover를 지원하며 특정 Bucket이 죽은 경우에 다른 Bucket으로 요청을 보낸다.

---
# 핵심 필기
## S3 Encryption
### Amazon S3 - Object Encryption
- S3 object 암호화는 4가지 방법이 있다.(현재 DSSE-KMS도 추가되어 5개)
- Server Side Encryption (SSE)
	- Server-Side Encryption with Amazon S3-Managed Keys (SSE-S3) - 디폴트 옵션으로 활성화 되어 있음.
		- AWS의 S3가 관리하는 Key를 통해서 암호화
	- Server-Side Encryption with KMS Keys stored in AWS KMS (SSE-KMS)
		- AWS의 KMS (Key Management Service)의 키를 활용한다.
		- AWS KMS는 서로 다른 계정 간에 공유가 가능.
	- Server-Side Encryption with Customer-Provided Keys (SSE-C)
		- Client에서 Key를 Object랑 같이 넘겨서 S3에서 해당 키를 통해 암호화
		- S3는 Client가 준 Key를 저장하지 않는다.
	- Client Side Encryption (CSE)
		- Client에서 암호화하고 넘기는 방식
### S3 Encryption SSE-S3
- AWS에서 키를 관리
- 암호화 타입은 **AES-256**
- 반드시 헤더에 `x-amz-server-side-encryption`: `AES256`을 넣어야 한다.
- 새로운 버켓과 오브젝트에는 기본적으로 활성화 되어 있다.
``` bash
aws s3api put-object --bucket 버켓명 --key 경로 --body object.jpg --server-side-encryption AES256
```
### S3 Encryption SSE-KMS
- AWS에서 키를 관리
- KMS의 이점: 사용자가 키를 컨트롤 가능 + CloudTrail을 통해 Key 사용을 검사 가능
- 반드시 헤더에 `x-amz-server-side-encryption`: `aws:kms`을 넣어야 한다.
``` bash
aws s3api put-object --bucket bucket_name --key location --body object.jpg --server-side-encryption aws:kms
```
#### SSE-KMS Limitation
- KMS Limit에 의해 영향을 끼칠 수 있다.
- Object 업로드 시엔 KMS API인 `GenerateDataKey`를 호출한다.
- Object 다운로드 시엔 KMS API인 `Decrypt`를 호출한다.
- KMS quota는 초당 5500, 10000, 3000이다.
- KMS quota 요청을 Service Quotas Console을 통해서 늘릴 수 있다.
- https://ap-northeast-2.console.aws.amazon.com/servicequotas/home/services/kms/quotas
### S3 Encryption SSE-C
- Client Side에서 key와 함께 데이터를 업로드한다.
- S3는 클라이언트의 키를 저장하지 않는다.
- 반드시 HTTPS를 사용
- 헤더에 매 번 키를 같이 넣어야 한다.
> [!HTTPS를 쓰는 이유]  
>S3는 VPC EndPoint를 통해 VPC 내부끼리 통신으로도 가능하지만, 외부 인터넷에서 접근할 땐 트래픽이 인터넷에 노출된다. 만약에, HTTPS를 사용하지 않으면 스니핑 기법 등으로 패킷을 훔쳐볼 수 있다.
### S3 Encryption Client-Side Encryption
- 클라이언트가 암호화 후에 데이터를 전달한다. (AWS S3 Client-Side Encryption 라이브러리가 있음)
- 클라이언트가 직접 암호화하고 직접 복호화해야 한다.
> [!DSSE-KMS]
>  새로 나온 암호화 기법인데 아직 시험에 안나옴
---
### S3 Encryption in transit (SSL/TLS)
- in flight에서 암호화를 SSL/TLS라고도 부른다.
- S3는 두 개의 엔드포인트를 가진다.
	- HTTP Endpoint - 전송 중에 암호화x
	- HTTPS Endpoint - 전송 중에 암호화o
- HTTPS를 권장하며 HTTPS는 SSE-C 방식엔 필수다.
![Pasted image 20250123152439.png](/img/user/image/Pasted%20image%2020250123152439.png)
---
## S3 Default Encryption
- Bucket Policy를 사용하면 Encryption을 확인하고 요청을 거부할 수 있다.
- **Note:** 버킷 정책이 "Default Encryption"보다 먼저 평가된다. (Default Encryption이 SSE-S3이고 버킷 정책에서 SSE-S3 암호화만 허용하는 상태에서 Object를 넣을 때 암호화를 선택하지 않았다면 Default Encryption으로 암호화 되기 전에 Bucket 정책에 의해 요청이 반려된다.)
- 예시
``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowDlsrbToPutGetObject",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::계정번호:user/dlsrb"
            },
            "Action": [
                "s3:PutObject",
                "s3:PutObjectTagging",
                "s3:GetObject",
                "s3:GetObjectTagging",
                "s3:GetObjectVersion",
                "s3:GetObjectVersionAttributes",
                "s3:GetObjectVersionTagging"
            ],
            "Resource": "arn:aws:s3:::버킷명/dlsrb/*"
        },
        {
            "Sid": "AllowPutObjectWhenEncryptedBySSES3",
            "Effect": "Deny", // Allow로 허용하더라도 Deny가 우선된다.
            "Principal": {
                "AWS": "arn:aws:iam::계정번호:user/dlsrb"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::버킷명/*",
            "Condition": {
                "StringNotEquals": {
                    "s3:x-amz-server-side-encryption": "AES256"
                }
            }
        }
    ]
}
```

``` bash
# KMS로 암호화 시도 시에
aws s3api put-object --bucket $BUCKET_NAME --key /dlsrb --body ./test.txt --server-side-encryption aws:kms

An error occurred (AccessDenied) when calling the PutObject operation: User: arn:aws:iam::148761649775:user/dlsrb is not authorized to perform: s3:PutObject with an explicit deny in a resource-based policy
```

``` bash
# AES256으로 암호화 시도 시에
aws s3api put-object --bucket $BUCKET_NAME --key /dlsrb --body ./test.txt --server-side-encryption AES256 

ETag: '"15764ba75f8216c17b339145162e1a32"'
ServerSideEncryption: AES256
VersionId: NKbGtk4jXHmNfDO.HNhubOnQfikvcyGJ
```
- ![image.GQS502.png](/img/user/image/image.GQS502.png)
- ![image.7JUS02.png](/img/user/image/image.7JUS02.png)
## S3 CORS
### What is CORS?
- Cross-Origin Resource Sharing(CORS)를 의미합니다.
- 스키마 + 호스트 주소 + 포트 번호로 식별합니다.
- 웹브라우저 기반 기술로 origin이 다른 곳에 요청할 때 사용됩니다.
	1. 브라우저로부터 A origin에 요청
	2. A origin에서 다른 B origin에 자원을 가져오는 코드가 작성되어 있음
	3. 브라우저는 B 오리진에 `pre-flight` request를 통해(OPTIONS 메소드) A 오리진에서 너를 명시했는데 써도 되냐고 물어봄
	4. 만약, B에서 명시를 했다면 요청을 허가하는 응답을 보냄
	5. `pre-flight`가 끝났으면 결과에 따라 웹브라우저가 요청을 보낼 수 있음

---
## MFA Delete
- MFA (Multi-Factor Authentication) - 사용자에게 특정 작업을 할 때, 코드 입력을 받는다. 코드는 Google Authenticator 같은 앱이나 MFA 하드웨어 USB 디바이스 등에서 받을 수 있다.
- MFA Delete를 사용하기 위해선 버저닝이 반드시 활성화된 상태여야 한다.
- bucket owner(root account)만 MFA 활성화/비활성화가 가능하다.
- S3가 MFA가 필요한 곳
	- 이전 버전 오브젝트를 영원히 삭제
	- 버저닝을 중단하는 것
- MFA가 필요하지 않은 곳
	- 버저닝 활성화
	- 삭제된 버전 리스트
	- 오직 bucket owner MFA Delete 설정이 가능함
## S3 Access Logs
### S3 Access Logs
- 검사 목적으로 S3 버켓의 모든 액션을 로그로 남길 때 사용
- S3에 들어오는 요청을 S3 Bucket에 저장
- Athena를 통해 로깅 버킷을 분석할 수 있다.
- 로깅 버켓은 모니터링할 버켓과 동일한 리전에 존재해야 한다.
- 로깅 형식: https://docs.aws.amazon.com/AmazonS3/latest/userguide/LogFormat.html
- **주의사항:** 로깅 버켓을 모니터링하면 재귀 호출이 발생
### S3 Pre-signed URLs
- 사전 승인을 통해서 권한 없이 URL을 통해 액션이 가능하다.
- S3 Console로 제작: 1분 ~ 720분(12시간)
- AWS CLI로 제작: default 1시간 최대 168시간
- 해당 링크로 GET, PUT이 가능하다.
	- 사용 예시
		- 프리미엄 유저만 다운 가능한 영상
		- 매 번 바뀌는 유저들이 다운로드 가능하게 동적으로 만드는 URL
		- 임시로 유저에게 특정 버킷 공간에 업로드 가능하게 설정
---
## Glacier Vault Lock & S3 Object Lock
### S3 Glacier Vault Lock
- S3 Glacier에서 만들 수 있는 저장소
- WORM (Write Once Read Many) 모델이다.
- Policy를 설정하면 수정이나 삭제가 불가능하다.
- 특정 규약 때문에 데이터가 수정 혹은 삭제 되는 걸 방지하기 위해서 쓴다.
	- 예: 은행에서 특정 계좌번호가 삭제되면 안된다. => Glacier Vault 사용하면 데이터가 변하지 않았다는 걸 증명할 수 있음.
### S3 Object Lock
- 특정 Object에 WORM 모델을 적용
- 반드시 버저닝이 활성화된 상태여야 한다.
- 특정 기간동안 Object의 버전 삭제를 방지합니다.
- **Retention Period**: 보유 기간으로 해당 기간동안 오브젝트 수정 불가.
- **Retention mode - Compliance**
	- 루트 유저를 포함한 모든 유저가 Object 버전을 덮어씌우거나 삭제할 수 없다.
	- Retention Mode는 변경이 불가능하다.
	- retention 기간을 줄일 수 없다.
- **Retention mode - Governance**
	- 대부분의 유저는 덮어씌우거나 삭제할 수 없고 잠금 상태가 변경이 불가능하지만, 특정 유저는 가능하다.
	- Retention Mode는 변경이 가능하다.
	- 특정 유저는 retention 기간을 바꾸거나 Object를 삭제할 수 있다.
- **Legal Hold**
	- retention 기간이 지나도 잠시동안 오브젝트 삭제를 유예시킴
	- s3:PutObjectLegalHoldIAM이라는 IAM 권한으로 Legal Hold를 주거나 제거할 수 있음.
---
## S3 Access Points
### S3 Access Points
![image.A7DX02.png](/img/user/image/image.A7DX02.png)
![Pasted image 20250123192050.png](/img/user/image/Pasted%20image%2020250123192050.png)
- Access Points는 버켓의 보안 관리를 간소화한다.
- 버켓은 여러 개의 Access Point를 가질 수 있다.
- 각각의 Access Point는 다음을 가진다.
	- DNS 이름 (Internet Origin이나 VPC Origin)
	- Access Point 정책을 가진다.
- 액세스 포인트엔 적절한 IAM 권한을 가지면 접근이 가능하다.
- Read Write 권한을 별개로 부여해서 특정 권한만 사용하게 설정할 수도 있다.
- VPC 내에 Access Point를 만들어서 내부 트래픽으로만 접근 가능하게 만들거나 인터넷 망 엔드포인트를 사용해서 외부에서 접근 가능하게 만들 수도 있다.

- VPC 내부에서만 접근 가능한 Access Point를 정의할 수도 있다.
- VPC EndPoint(Gateway나 Interface)를 만들어야 Access Point에 접근이 가능하다.
- VPC EndPoint 정책에서 타겟 Bucket과 Access Point에 접근 가능하도록 설정해야 한다.
![Pasted image 20250123192527.png](/img/user/image/Pasted%20image%2020250123192527.png)
### S3 Multi-Region Access Points
![Pasted image 20250123193819.png](/img/user/image/Pasted%20image%2020250123193819.png)
- 여러 Region에 퍼져있는 Bucket 중에서 가장 latency와 네트워크 혼잡도(congestion)이 낮은 곳으로 자동 라우팅해준다.
- 여러 Region의 S3는 양방향 동기화가 이뤄지며 이를 통해 동일한 데이터를 조회할 수 있다.
- s3로 요청이 실패했을 때 자동으로 failover control이 이뤄져 정상적인 S3로 요청을 보낸다. (Active-Active or Active-Passive)
### S3 VPC Endpoints
- 인터넷망을 통해서 접근하는 경우 Internet Gateway를 통해서 S3 Bucket에 접근한다.
- VPC 내부 네트워크를 사용해서 VPC Endpoint Gateway를 통해서 접근하면 인터넷 망으로 트래픽이 전달되지 않는다. (AWS 백본을 사용한다)
- VPC를 사용할지는 S3 Bucket Policy를 통해 설정이 가능하다.
---
# 참고 자료
