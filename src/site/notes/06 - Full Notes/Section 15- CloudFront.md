---
{"dg-publish":true,"permalink":"/06-full-notes/section-15-cloud-front/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ultimate AWS Certified SysOps Administrator Associate 2024\|Ultimate AWS Certified SysOps Administrator Associate 2024]]
---
# 단서 질문
- CloudFront란?
	- Content Delivery Network로 자주 사용되는 데이터들을 엣지 단에서 캐싱하여 사용자가 더 빨리 응답을 받을 수 있도록 합니다.
- CloudFront의 Origin
    - S3
    - ALB
    - EC2 등 HTTP 기반 모든 origin
- CloudFront의 트러블 슈팅
    - 403 에러
	    - 유저가 해당 자원에 접근할 수 있는 권한이 없다.
    - 404 에러
        - 해당 자원이 존재하지 않는다.
    - 500 에러
        - 게이트웨이 이슈
- CloudFront의 Geo Restriction이란?
    - 클라우드의 접근 가능한 IP 대역을 설정을 하는 기능입니다.
    - 화이트리스트를 통해서 특정 지역 접근이 가능하게 하거나 블랙리스트를 통해서 특정 지역 접근이 불가능하게 설정할 수 있습니다.
    - 지역별 IP 대역은 3rd Party Geo Database를 사용합니다.
---
# 핵심 요약
- CloudFront는 Content Delivery Network다.
- 전세계에 퍼져있는 캐싱 서버를 통하여 사용자가 더 빠른 서비스 이용이 가능하다.
- 쿠키, 헤더, 쿼리스트링을 통해 캐싱 전략을 사용 가능하다.
- 이들을 화이트리스트처럼 특정 쿠키 내용이나 헤더만 오리진에 보낼 수 있다.
- 그러나, 아무 것도 오리진에 안보내는 게 낫다.
- 단, ALB Sticky Session 사용 시엔 ALB가 쿠키를 기반으로 특정 서버와 Binding 되므로 쿠키를 오리진에 보내도록 허용할 필요가 있다.
- Custom Origin Header로 사용자가 설정한 헤더를 오리진에 보낼 수 있다.
- S3의 Multi Region Replication은 리전에 버켓을 만든다.
- 반면에, CloudFront는 요청이 오면 근처에 Edge 단에 요청이 간다.
- 해당 캐싱 서버에 캐싱이 되어있다면 오리진까지 요청이 가지 않는다.
---
# 핵심 필기
## CloudFront Deep Dive
![Pasted image 20250211110237.png](/img/user/Pasted%20image%2020250211110237.png)
- CloudFront는 먼저 Edge 단에 캐싱 서버를 둔다.
- 클라이언트가 요청을 한다면 Edge Location에서 확인한다.
- Edge Location에 요청한 데이터가 없다면 Regional Edge를 확인한다.
- 거기도 없다면 CloudFront로 요청이 가서 Origin의 데이터를 받아온다.
- CloudFront는 Edge Location이 죽더라도 AWS에서 다른 Edge Location으로 요청을 보내도록 한다. (고가용성)

## CloudFront Overview
### Amazon CloudFront
- Content Delivery Network (CDN)
- 엣지단에 캐싱 서버를 둬서 조회 속도를 향상시킨다.
- 현재 216개 엣지 로케이션 운영 중
- DDoS 방어, Shield 통합, AWS WAF 지원
### CloudFront - Origins
- S3 Bucket
	- 엣지단에 파일을 캐싱하여 조회 속도를 향상
	- Origin Access Control(OAC)을 통해서 CloudFront로부터 오는 요청만 받게 설정 가능하다.
	- OAC는 OAI(Origin Access Identity)의 신버전
	- CloudFront에 파일을 업로드함으로써 S3에 파일 업로드 가능.
- Custom Origin (HTTP)
	- ALB
	- EC2
	- S3 Website
	- Any HTTP backed you want
### CloudFront at a high level
![Pasted image 20250210174551.png](/img/user/image/Pasted%20image%2020250210174551.png)
### CloudFront - S3 as an Origin
![Pasted image 20250210174628.png](/img/user/image/Pasted%20image%2020250210174628.png)
### CloudFront vs S3 Cross Region Replication
- CloudFront:
	- Global Edge Network
	- 파일은 TTL 기간동안 캐싱(maybe a day)
	- **어느 장소에서도 가용 가능**한 **정적 컨텐츠**에 적합하다.
- S3 Cross Region Replication:
	- 복제를 원하는 리전에 bucket을 생성해야한다.
	- Read Only
	- **특정 리전**에서 가용 가능한 **동적 컨텐츠**에 적합하다.
### CloudFront - ALB or EC2 as an origin
![Pasted image 20250210190906.png](/img/user/image/Pasted%20image%2020250210190906.png)
- CloudFront로 Ingress를 받아야하므로 Origin의 Ingress엔 CloudFront의 IP 주소가 허용될 필요가 있다.
### CloudFront - Geo Restriction
- distribution 접근을 제한할 수 있다.
- AllowList/BlockList 방식으로 제한 가능하다.
	- AllowList: 접근 가능한 나라 설정
	- BlockList: 접근 불가능한 나라 설정
## CloudFront Reports, Logs and Troubleshooting
### CloudFront Access Lgs
- CloudFront로 가는 요청들은 S3 Bucket에 로그로 기록된다.
![Pasted image 20250210192226.png](/img/user/image/Pasted%20image%2020250210192226.png)
### CloudFront Reports
- 생성 가능한 리포트
    - Cache 통계
    - Popular Objects
    - Top Referrers (가장 많이 인용하는 사이트)
    - Usage Reports
    - Viewers Report
- 위의 리포트들은 `Access Logs`를 기반으로 만들어진다.
### CloudFront 트러블 슈팅
- CloudFront는 오리진으로부터 오는 400, 500번대 상태 코드도 캐싱한다.
- 5xx 에러 코드는 게이트웨이 이슈다.
## CloudFront Caching - Deep Dive
### CloudFront Caching
- 캐싱은 다음을 통해 가능하다.
	- Headers
	- Session Cookies
	- Query String Parameters
- CloudFront 캐시는 `Edge Location`에 존재한다.
- TTL: 0 ~ 1년
- `CreateInvalidation` API를 통해서 캐시 무효화가 가능하다.
	- 프론트엔드 웹페이지를 배포했거나, 특정 파일을 배포했는데 요청 시에 이전 버전이 오는 경우가 있다. 아직 Cache가 남아있어서 기존 Cache로 응답을 계속 보내서 생기는 문제. -> `CreateInvalidation`으로 Edge Location의 캐시를 무효화하면 캐시가 날아감. -> 요청 시에 캐시가 비었으므로 Origin으로 요청 전달
### CloudFront Cache Behavior for Headers
1. 모든 헤더를 오리진에 포워딩
	- 캐싱x, 모든 요청이 오리진으로 감
	- TTL을 0으로 설정하면 된다.
2. 전체 헤더에서 화이트리스트로 설정한 헤더만 포워딩
	- 특정 헤더에 값을 기준으로 캐싱
3. 디폴트 헤더만 포워딩
	- 이 방법은 헤더 기반 캐싱을 하지 않는다.
	- 따라서, 캐싱 퍼포먼스가 제일 좋다.
### CloudFront Caching - Whitelist Headers
  ![Pasted image 20250210193556.png](/img/user/image/Pasted%20image%2020250210193556.png)
### CloudFront Origin Headers vs Cache Behavior(시험 중요 포인트로 차이점을 묻는다.)
- Origin Custom Headers: 
	- Origin-level setting
	- constant 헤더를 설정하여 모든 Origin으로 가는 요청에 해당 헤더가 추가된다.
	- 사용 예:
		- 이 요청은 CloudFront로부터 오는 거라고 알리고 싶을 때 Custom Header로 이 요청은 CloudFront에서 온 요청인 걸 알 수 있음.
- Behavior setting
	- Cache-related settings
	- Contains the whitelist of headers to forward
![Pasted image 20250210194126.png](/img/user/image/Pasted%20image%2020250210194126.png)
### CloudFront Caching TTL
- "Cache-Control: max-age"가 "Expires" 헤더보다 선호됨.
- 만약, 오리진이 항상 Cache-Control 헤더를 보낸다면 Cache의 TTL을 오리진의 헤더로만 설정하도록 할 수 있다.
- 캐시의 TTL min/max를 범위로 설정하고 싶다면 Object Caching 옵션을 커스터마이즈로 두면 된다.
- 만약에, 오리진에 응답으로 Cache-Control 헤더가 없다면 TTL 기본값으로 캐싱된다.
![Pasted image 20250210200856.png](/img/user/image/Pasted%20image%2020250210200856.png)
![Pasted image 20250210200907.png](/img/user/image/Pasted%20image%2020250210200907.png)

> [!Cache-Control이란?]
> 캐싱하는 기간을 헤더에 작성해놓는 것. CloudFront에서 Origin에 요청이 갔을 때, Origin이 헤더에 캐시 만료기간을 명시한다. 이를 헤더에 명시하는데 CloudFront는 "Cache-Control" 헤더를 사용한다. 
### CloudFront Cache Behavior for Cookies
1. Default 설정: 쿠키를 처리하지 않음
	- 캐싱에 쿠키를 사용하지 않음.
	- 따라서, 오리진으로 쿠키가 전달되지 않음
2. 화이트리스트 쿠키를 전송
	- 화이트리스트에 등록된 부분만 전달한다.
3. 모든 쿠키를 오리진에 전송
	- 최악의 퍼포먼스를 가지는 캐싱 방법
### CloudFront Cache Behavior for Query Strings
1. Default 설정: 쿼리스트링을 처리하지 않음.
	- 캐싱에 쿼리스트링 사용x
	- 따라서, 오리진으로 쿼리스트링이 전달되지 않음.
2. 화이트리스트
3. 모든 쿼리스트링 전송
### CloudFront - Maximize cache hits by separating static and dynamic distributions
- 동적 컨텐츠와 정적 컨텐츠를 분리
![Pasted image 20250210201720.png](/img/user/image/Pasted%20image%2020250210201720.png)
### CloudFront - Increasing Cache Ratio
- CloudWatch 매트릭으로 `CacheHitRate`를 모니터링

- 최소한의 헤더, 쿠키, 쿼리스트링을 오리진으로 전달
- 정적 컨텐츠와 동적 컨텐츠를 위한 캐싱을 나눠라(two origins)

## CloudFront with ALB Sticky Sessions
### CloudFront with ALB Sticky Sessions
- ALB Sticky Session 사용하려면 Cookie를 무조건 오리진에 포워딩하게 설정해야함.
- ALB Sticky Session은 쿠키를 기반으로 동일한 서버에 요청을 라우팅해줌.
- 만약에, CloudFront에서 오리진으로 보낼 때, 캐시를 제거하고 보낸다면 Sticky Session이 제대로 동작하지 않는다.
- 캐싱 TTL은 Sticky Session 토큰의 만료일보다 작게 설정한다.
---