---
{"dg-publish":true,"permalink":"/06-full-notes/section-06-ec-2-high-availability-and-scalability-1/","dgPassFrontmatter":true}
---

# Tags
[[03 - Tags/Ultimate AWS Certified SysOps Administrator Associate 2024\|Ultimate AWS Certified SysOps Administrator Associate 2024]]

---
# 단서 질문
- High Availability란?
    높은 가용성으로 특정 리전이나 AZ에 장애가 발생하더라도 서비스의 문제가 없어야 한다. 이중화
- High Availability를 위한 설정 방법은?
    Multi AZ에서 인스턴스를 실행한다. ELB는 Cross-Zone 설정을 통해 균등하게 트래픽을 분배한다.
- High Scalability란?
    높은 확장성으로 트래픽이 몰리거나 줄어들었을 때 유동적으로 자원의 확장 혹은 축소가 일어난다.
- High Scalability를 위한 설정 방법은?
    ASG 그룹을 통해서 EC2 스케일링을 지원한다.
- ALB의 스코프는?
    AZ별로 존재한다. Cross Zone 정책을 통해 서로 다른 AZ의 인스턴스로 패킷 포워딩이 가능하다.

---
# 핵심 요약
**AI 요약**
- **로드 밸런서 인증서와 보안:** HTTPS 리스너에는 기본 인증서가 필요하며, SNI를 통해 여러 도메인 지원이 가능합니다.
- **Connection Draining:** 비정상 인스턴스에 대한 유예 기간을 제공하여 기존 연결을 안전하게 종료합니다.
- **헬스 체크와 모니터링:** 다양한 타겟 상태를 통해 인스턴스 건강을 관리하고, 로그와 메트릭을 통해 성능을 모니터링합니다.
- **타겟 그룹 속성:** 등록 해제 지연, 느린 시작 모드, 로드 밸런싱 알고리즘 등을 설정하여 세밀한 제어가 가능합니다.
- **ALB 규칙과 가중치:** 리스너 규칙을 통해 요청을 라우팅하고, 타겟 그룹 가중치로 트래픽 분배를 조절할 수 있습니다.
**직접 요약**
- **높은 가용성이란? (High Availability)**
	특정 지역이나 서비스에 문제가 발생하더라도 서비스를 지원할 수 있음을 말한다. 높은 가용성을 위해서 AZ 내의 문제가 생겼을 때, 다른 AZ에서 실행되는 서비스가 failover를 통해 지원된다.
- **높은 확장성이란? (High Scalability)**
	수평적 확장성과 수직적 확장성으로 나뉘며 서비스의 요구치를 따라가지 못했을 때, 기계의 스펙을 높이거나 서비스의 개수를 늘려서 서비스의 제공을 원할하게 한다.
- **ELB 종류**
	1. CLB (Classic Load Balancer): 
		- 구버전 로드밸런서로 현재는 사용을 권장하지 않는다.
	2. ALB (Application Load Balancer):
		- 다양한 방법으로 라우팅이 가능하다.(URL 기반, HOST Name 기반, 쿼리스트링 및 헤더 기반)
		- 고정된 호스트네임을 가진다.
	3.  NLB (Network Load Balancer):
		- 4계층인 전송 계층을 사용하는 로드밸런서다.
		- 타겟 그룹으로는 IP, EC2 Instances, ALB가 가능하다.
	4. GLB (Gateway Load Balancer):
		- 3계층인 네트워크 계층을 사용하는 로드밸런서다.
		- 서드파티 소프트웨어 방화벽이나 침입 패킷 방지 같은 어플리케이션을 통해서 패킷을 검사할 수 있다.
		- 타겟 그룹으로는 IP, EC2 Instances다.
---
# 핵심 필기
## [SAA/DVA] What is High Availability and Scalability?
### Scalability and High Availability Section
### **Scalability**
앱이나 시스템이 더 많은 부하에도 적응하는 것
1. **Vertical Scalability**
	- 사이즈를 키우는 것
	- t2.medium → t2.large
	- 분산 시스템이 아닌 경우에 흔히 사용된다. (ex: RDS)
	- 수직 스케일링의 비용은 스펙에 따라 기하급수적으로 증가하므로 한계가 있다.
	- AWS의 RDS, ElasticCache 서비스는 수직 스케일링이 가능하다.
2. **Horizontal Scalability**
	- 인스턴스의 개수를 늘린다.
	- 분산 시스템에 적용이 가능하다. (ex: 웹 어플리케이션)
	- 확장성은 가용성이랑 연결되지만 다른 개념이다.
### **Availability**
- 주로 수평적 스케일링과 같이 간다.
- 높은 가용성은 시스템이 최소한 두 개의 데이터센터에서 동작하는 것을 말한다. (==Availability Zone)
- 데이터 센터 손실에도 살아남는 게 High Availability의 목표다.
- The high availability can be passive (for RDS Multi AZ for example)  
- The high availability can be active (for horizontal scaling)  

---
## High Availability & Scalability for EC2
- 수직 스케일링: 인스턴스 사이즈 키우기 (= scale up / down)
	- From: t2.nano - 0.5G of RAM, 1vCPU
	- To: u-12tb1.metal - 12.3TB of RAM, 448 vCPUs
- 수평 스케일링: 인스턴스 개수 늘리기 (= scale out / in)
	- Auto Scaling Group
	- 로드밸런서
- High Availability: 여러 AZ에서 동일한 애플리케이션의 인스턴스 돌리기
	- Auto Scaling Group multi-AZ
	- 로드밸런서 multi-AZ
---
## [SAA/DVA] Elastic Load Balancing (ELB) Overview
### What is Load Balancing
![image 42.png](/img/user/image/image%2042.png)
- 로드밸런싱을 들어오는 트래픽을 여러 개의 서버로 포워딩한다. 
	- 예: EC2나 클러스터 환경인 경우에 Pod로 포워딩한다.
### Why use a load balancer
- DNS를 하나 사용해서 여러 서버를 구축할 수 있다.
- 서버에 대한 직접적인 접근을 막을 수 있다.
- 서비스를 제공하는 서버의 상태가 좋지 않더라도 로드밸런서에서 해당 서버로 요청을 보내지 않아서 외적으로 문제가 없어보인다.(Seamlessly handle failures of downstream instances)
- 주기적으로 인스턴스들의 헬스체크를 한다.
- SSL 제거를 지원한다.(Provide SSL termination Https for your websites)
	- 따라서, 로드 밸런서 뒤에 서비스들은 HTTP로 통신이 가능하다. (SSL offloading)
- Enforce stickiness with cookies(ALB, CLB는 cookie를 사용해서 로드밸런서로 들어오는 요청을 동일한 인스턴스로 보내준다. NLB는 cookie를 사용하지 않고 이 기능을 제공한다.)
- 높은 가용성을 제공(High Availability across zones)
- 프라이빗 트래픽과 퍼블릭 트래픽을 나눈다. (Separate public traffic from private traffic)
	- Load Balancer를 퍼블릭 엔드 포인트로 설정하고 내부 서비스들은 프라이빗 서브넷으로 설정하여 Load Balancer를 앞뒤로 퍼블릭 트래픽과 프라이빗 트래픽으로 나뉜다.
### Why use an Elastic Load Balancer?
- Elastic Load Balancer는 AWS에서 제공하는 로드밸런서를 총칭한다.
	- ELB는 Managed 서비스로 다음을 보장한다.
	- AWS에선 ELB의 동작을 보장한다.
	- AWS에선 업그레이드, 유지보수, 높은 가용성을 보장한다.
		- Load Balancer를 만들 때, 서로 다른 AZ에 서브넷을 선택하는 부분이 있는데 여기서 가용성을 보장하는 것이다.
	- AWS에선 ELB 설정 구성만 사용자에게 제공한다.
- 로드밸런서를 직접 구축하는 것보단 비싸지만 직접 구축하는 건 까다롭다.
- 다음과 같은 다양한 AWS offerings와 서비스들과 통합된다.
	- EC2: EC2 인스턴스에 트래픽 분배가 가능하다.
	- EC2 Auto Scaling Groups: 오토스케일링을 타겟 그룹으로 설정하여 스케일 아웃/인 시에도 적절히 트래픽을 분배한다.
	- Amazon ECS
	- AWS Certificate Manager (ACM): 인증서를 추가할 수 있다.
	- CloudWatch
	- Route 53: Route53에 DNS 레코드로 추가하여 로드 밸런서에 호스트 네임으로 접근 가능하게 만들 수 있다.
	- AWS WAF: 방화벽을 적용할 수 있다.
	- AWS Global Accelerator
### Health Checks
- 로드밸런서에선 주기적으로 서버들의 상태를 체크한다.
- 헬스 체크는 포트와 라우트에서 이뤄진다. (기본 라우트 설정은 `/health`다.)
- 만약, 헬스 체크가 리턴하는 상태 코드가 200이 아니면 해당 인스턴스는 건강하지 않다는 말이다. (단, 설정으로 Healthy 기준이 되는 상태 코드를 변경할 수 있다. 예를 들어, 202를 Healthy 기준으로 잡으면 202를 리턴해야 건강하다고 판단한다.)
![image 1 15.png](/img/user/image/image%201%2015.png)
### Types of load balancer on AWS
- AWS는 4가지 로드밸런서가 존재한다.
- CLB (v1 - old generation) - 2009
	- HTTP, HTTPS, TCP, SSL
- ALB (v2 - new generation) - 2016
	- HTTP, HTTPS, WebSocket
- NLB (v2 - new generation) - 2017
	- 4계층을 이용하는 로드밸런서 - TCP, TLS, UDP
- GLB - 2020 - GWLB
	- 3계층을 이용하는 로드밸런서 - IP Protocol
- 몇몇 로드밸런서는 내부(internal, private)나 외부(external, public) 로드밸런서로 사용할 수 있다.

![image 2 13.png](/img/user/image/image%202%2013.png)
### Classic Load Balancers (v1)
- 4계층 7계층 모두 지원
- 헬스 체크는 TCP 혹은 HTTP 프로토콜을 사용
- 고정된 호스트 네임 사용 (ex. XXX.region.elb.amazonaws.com)
---
## [SAA/DVA] Application Load Balancer (ALB)
### Application Load Balancer (v2)
- 7계층 로드밸런서다. (HTTP)
- 여러 인스턴스의 여러 어플리케이션으로 포워딩 가능하다. (target groups)
- 동일 인스턴스의 여러 어플리케이션으로 포워딩도 가능하다.(ex. containers)
- HTTP/2와 WebSocket을 지원한다.
- 리다이렉트를 지원한다.(from HTTP to HTTPS for example)
- 라우팅 테이블을 통해 서로 다른 target groups에 포워딩 가능하다.
	- URL을 통해서 포워딩 (example.com/**users** & example.com/**posts**)
	- 호스트네임을 통해서 포워딩 (**one**.example.com & **other**.example.com)
	- 쿼리스트링이나 헤더를 통해서 포워딩(example.com/**users?id=123&order=false**)
- ALB는 마이크로 서비스와 컨테이너 기반 어플리케이션에 효율적이다. (e.g. Docker & AWS ECS)
- 리다이렉팅을 통해서 ECS에 동적으로 포트 매핑이 가능하다.
- CLB는 어플리케이션별로 하나씩 필요하다.

![image 3 12.png](/img/user/image/image%203%2012.png)
	
- Target Groups
	- EC2 instances (Auto Scaling Group을 통해 관리 가능하다) - HTTP
	- ECS tasks (managed by ECS itself) - HTTP
	- Lambda functions - HTTP request is translated into a JSON event
	- IP Addresses - private IP여야만 한다.
	- ALB는 여러 Target group에 라우팅 가능하다.
	- Health Check는 target group 수준에서 진행한다.
### Query Strings/Parametrs Routing
	
![image 4 12.png](/img/user/image/image%204%2012.png)
	
### Application Load Balancer (v2) Good to Know
- 고정된 호스트네임을 가진다. (XXX.region.elb)
- 클라이언트의 IP를 직접적으로 보지 않는다. (데이터 3계층의 IP 주소는 ALB의 주소다.)
	- 클라이언트의 진짜 IP는 헤더의 X-Forwarded-For다.
	- 헤더에서 Port (X-Forwarded-Port)와 proto (X-Forwarded -Proto)를 얻을 수 있다.
	![image 5 10.png](/img/user/image/image%205%2010.png)
---
## [SAA/DVA] Network Load Balancer (NLB)
### Network Load Balancer (v2)
- Network Load Balancer (Layer 4)는 다음과 같은 특징을 가졌다.
	- 초당 수백만 개의 요청을 처리 가능하다. 
	- 굉장히 낮은 Latency
	- TCP와 UDP 트래픽을 인스턴스에 포워딩한다.
- NLB는 AZ 당 1개의 정적 IP를 가진다. 또한, Elastic IP를 할당 받을 수도 있다.
	- Elastic IP로 고정 IP를 사용하면 화이트리스트로 추가하기 좋다.
- NLB는 TCP 또는 UDP 트래픽에 엄청난 퍼포먼스를 원할 때 사용할 수 있다.
- Free Tier에서 지원 안 한다.
### Network Load Balancer (v2) TCP (Layer 4) Based Traffic
![Pasted image 20250114180557.png](/img/user/image/Pasted%20image%2020250114180557.png)
### Network Load Balancer - Target Groups
- EC2 Instances
- IP Addresses
- ALB

- 헬스 체크는 HTTP, HTTPS, TCP 프로토콜을 사용한다.

![image 6 10.png](/img/user/image/image%206%2010.png)
- Network Load Balancer with ALB: NLB는 고정 IP를 제공하고 ALB를 통해서 HTTP 트래픽을 핸들링한다.
## [SAA/DVA] Gateway Load Balancer( GWLB)
### Gateway Load Balancer
- 서드파티 네트워크 가상 제품을 배포, 스케일, 관리합니다.(Deploy, scale, and manage a fleet of 3rd party network virtual appliances in AWS)
- 네트워크 가상 제품 예시
	- Firewalls
	- Intrusion Detection
	- Prevention Systems
	- Deep Packet Inspection
- 3계층에서 동작합니다. - IP Packet을 조사
- 다음 기능들과 결합됩니다.
	- Transparent Network Gateway(투명한 네트워크 게이트웨이) - 단일 진입적이다. (single entry/exit for all traffic)
	- Load Balancer - 트래픽을 virtual appliances에 분배합니다.

![image 7 8.png](/img/user/image/image%207%208.png)
	
### Gateway Load Balancer - Target Groups
- EC2 인스턴스
- IP 주소 - must be private IPs
![image 8 8.png](/img/user/image/image%208%208.png)
	
## [SAA/DVA] Elastic Load Balancer - Sticky Sessions
### Sticky Sessions (Session Affinity)
- stickiness는 클라이언트가 동일한 인스턴스로 요청을 보내도록 합니다.(세션 유지)
- CLB, ALB, NLB 모두 지원합니다.
- CLB와 ALB는 헤더에 있는 쿠키의 값을 통해서 stickiness를 지원하고 해당 쿠키는 만료 기간을 가집니다.
- stickiness를 사용하면 로드밸런서를 통해서 분배되는 작업들의 균형이 무너질 수 있습니다. (로드밸런서는 인스턴스의 균일하게 작업을 분배해야 하는데 stickiness를 활성화하면 동일 클라이언트는 계속 동일 인스턴스에 요청을 보낸다)
> [!NOTE]
> 웬만하면 **웹 계층은 무상태(Stateless)로 구성**하는 게 좋다. sticky session을 통해 동일한 인스턴스로 요청을 보낼 수 있지만, 이는 다시 말하면 스케일링을 통해 서버가 추가되더라도 기존 사용자는 계속 동일한 인스턴스에 요청을 보낸다는 뜻이다.
> 따라서, RDB 혹은 NoSQL DB에 상태값을 저장하는 게 옳다.
### Sticky Sessions - Cookie Names
- Application-based Cookies
	- Custom cookie
		- 타겟에 의해 생성됩니다.(ex. Application에서 생성한다는 뜻)
		- 어플리케이션이 요구하는 커스텀 속성을 담을 수 있습니다.
		- 타겟 그룹마다 쿠키이름을 명시해줘야 합니다.
		- AWSALB, AWSALBAPP, AWSALBTG(Target Group 활성화 관련)는 ELB에서 사용하는 쿠키이므로 사용하지 마세요.
	- Application cookie
		- 로드밸런서에 의해서 만들어집니다. (Generated by the load balancer)
		- 쿠키명은 AWSALBAPP입니다.
- Duration-based Cookies
	- 쿠키는 로드밸런서에 의해서 만들어집니다.
	- 쿠키명은 ALB를 사용하면 AWSALB, CLB를 사용하면 AWSELB입니다.
## [SAA/DVA] Elastic Load Balancer - Cross Zone Load Balancing
- 따라서, AZ 2개에 요청이 들어오면 이 요청은 50 대 50으로 나눠질 것이며 각 AZ에 실제로 가용 가능한 인스턴스가 몇 개 있더라도 로드밸런서는 똑같이 50 대 50으로 나눕니다.
- 이를 해결하기 위해서 존재하는 설정이 Cross-Zone Load Balancing입니다.
- 자신의 지역에 있는 인스턴스 뿐만이 아니라 다른 AZ에 속한 인스턴스에도 로드밸런싱을 합니다.
- 따라서, 요청은 인스턴스 별로 골고루 분배됩니다.
![image 9 8.png](/img/user/image/image%209%208.png)
### ELB별 Cross-Zone 특성
- **ALB**
	- 기본적으로 설정되어 있음(Target Group 수준에서 비활성화 가능)
	- AZ 간 데이터 전송 요금은 무료입니다. (No Charges for inter AZ data)
- **NLB & GLB**
	- 기본적으로 비활성화
	- AZ 간 데이터 전송은 요금이 부과됩니다.
- **CLB**
	- 기본적으로 비활성화
	- AZ 간 데이터 전송 요금은 무료입니다. (No Charges for inter AZ data)

---
## [SAA/DVA] Elastic Load Balancer - SSL Certificates
### SSL/TLS - Basics
- SSL (Secure Socket Layer) Certificate는 클라이언트와 로드밸런서 통신 간에 패킷을 암호화할 수 있게 해줍니다.(in-flight encryption)
- SSL은 Secure Socket Layer로 연결을 암호화하는데 사용됩니다.
- TLS는 전송 계층의 TCP와 SSL을 결합된 기술을 말합니다.
- 공인 SSL 인증서는 CA에서 발행한다. Public SSL certificates are issued by CA (Certificate Authorities)
- SSL 만료일자가 존재하므로 갱신이 필요하다.
### Load Balancer - SSL Certificates
![image 10 7.png](/img/user/image/image%2010%207.png)
- 로드밸런서는 X.509 인증서를 사용 (SSL/TLS server certificate)
- ACM(AWS Certificate Manager)을 통해서 인증서 관리가 가능합니다.
- 자체 인증서를 업로드하여 사용하는 인증서를 바꿀 수 있다.
- HTTPS listener:
	- 반드시 기본 인증서를 설정해야 한다.
	- 여러 도메인을 지원하기 위해 여러 개의 인증서를 사용할 수 있다.
	- 클라이언트는 SNI를 사용하여 도착지 호스트네임을 명시할 수 있다.
	- Ability to specify a security policy to support older versions of SSL / TLS (legacy clients)
### SSL - Server Name Indication
- SNI는 하나의 웹서비스에서 여러 도메인의 인증서를 가져오는 문제를 해결합니다.
	- ALB 뒤엔 여러 개의 웹서비스(서로 다른 도메인을 가진)가 올 수 있는데 이들은 각각의 인증서를 가진다. 따라서, ALB에 요청할 때 요청하는 도메인과 일치하는 인증서를 가져올 수 있게 하는 기능이 있어야 한다.
- SNI는 새로 나온 프로토콜로 클라이언트가 SSL handshake 과정에서 타겟 서버의 호스트 네임을 명시합니다.
- 만약에, 클라이언트가 명시한 호스트 네임과 일치하는 인증서가 있다면 해당 인증서를 리턴하고 만약, 호스트 네임이 없다면 default로 설정된 인증서를 리턴합니다.
**추가 노트**
- ALB, NLB, CloudFront만 지원한다.
- Does not work for CLB(older gen)
### Elastic Load Balancers - SSL Certificates
- CLB
	- 인증서 1개만 사용 가능
	- 호스트네임 하나마다 하나의 CLB가 필요하다.
- ALB
	- 인증서 여러 개 사용 가능
	- SNI를 통해서 해당하는 인증서를 내려줌.
- NLB
	- 인증서 여러 개 사용 가능
	- SNI를 통해서 해당하는 인증서를 내려줌.
## [SAA/DVA] Elastic Load Balancer - Connection Draining
### Connection Draning
- **Feature naming(기능 네이밍)**
	- Connection Draining - for CLB
	- Deregistration Delay - for ALB & NLB
- 인스턴스가 비정상 상태(중단, 등록해제, unhealthy)와 같은 시간일 때, 유예 기간을 주고 해당 시간 내에 회복되지 않으면 해당 인스턴스를 로드밸런서에서 제거합니다.
- 유예 기간동안 들어오는 요청들은 Draning 상태(de-registering)가 아닌 건강한 인스턴스로 요청합니다.
- 유예 기간은 1~3600초 (기본값: 300초)
- 비활성화 가능 (0초로 설정하면 비활성화)
- 요청 시간이 짧으면 낮은 값으로 설정하세요 (요청 시간이 짧으면 길게 유예 시간을 줄 필요가 없다.)
### Elastic Load Balancer - Health Checks
- 타겟 헬스 상태
	- Initial: 타겟 등록
	- Healthy
	- Unhealthy
	- Unused: 타겟이 등록되지 않음
	- Draining: de-registering the target => 타겟을 등록 해제 중
	- Unavailable: health checks disabled => 헬스체크가 비활성화된 상태
- 타겟 그룹의 모든 타겟이 Unhealthy하면 ELB는 Unhealthy한 타겟에 요청을 라우팅한다.

![image 11 7.png](/img/user/image/image%2011%207.png)

---

## Elastic Load Balancer - Monitoring, Troubleshooting, Logging and Tracing
### Load Balancer Error Codes
- Successful request : Code 200.  
- Unsuccessful at client side : 4XX code.  
	• Error 400 : Bad Request  
	• Error 401 : Unauthorized  
	• Error 403 : Forbidden  
	• Error 460 : Client closed connection.  
	• Error 463 : X-Forwarded For header with >30 IP (Similar to malformed request).  
- Unsuccessful at server side : 5xx code.  
	• An error 500 / Internal server error would mean some error on the ELB itself.  
	• Error 502 : Bad Gateway  
	• An error 503 / Service Unavailable  
	• Error 504 / Gateway timeout : probably an issue within the server.  
	• Error 561 : Unauthorized  
### Load Balancers Monitoring
- 모든 로드 밸런서 메트릭은 직접적으로 CloudWatch Metrics로 푸시됩니다.
- BackendConnectionErrors
- Latency
- HealthyHostCount / UnHealthyHostCount
- RequestCount
- RequestCountPerTarget
- HTTPCode_Backend_2XX: 
- **SurgeQueueLength**: Healthy Instance에 대한 요청(HTTP Listener)이나 연결(TCP Listener)이 Pending 상태인 개수를 구한다. ASG 스케일 아웃을 도우며 최대 크기는 1024이다.
- HTTPCode_Backend_3XX,
- HTTPCode_ELB_4XX:
- **HTTPCode_ELB_5XX**:
- **SpilloverCount**: SurgeQueue가 꽉 차서 거부된 요청의 개수
### Load Balancer troubleshooting using metrics
- HTTP 400: BAD_REQUEST => 클라이언트가 오염된(malformed) 요청을 보내고 있다.
- HTTP 503: Service Unavailable => 로드밸런서가 요청을 라우팅할 인스턴스가 Healthy인지 확인해라. (클라우드 워치에서 HealthyHostCount)
- HTTP 504: Gateway Timeout => EC2의 Keep alive 옵션이 활성화 되어 있고, keep-alive 타임아웃이 로드밸런서의 idle t타임 아웃보다 커야한다.
### Load Balancers Access Logs
- 로그는 S3에 저장할 수 있습니다. 저장되는 정보들은 다음과 같습니다.
	- Time
	- Client IP address
	- Latencies
	- Request paths
	- Server Response
	- Trace ID
- S3 Storage 용량만큼만 요금 부과
- Access Logs는 암호화되어 저장된다.
### Application Load Balancer Request Tracing
- Request tracing - 각 HTTP 요청은 `X-Amzn-Trace-Id`라는 커스텀 헤더를 추가한다.
- Example: X-Amzn-Trace-Id: Root=1-67891233-abcdef012345678912345678
- 헤더를 통해서 logs와 분산 추적 플랫폼에서 하나의 요청을 추적할 수 있다. (This is usefule in  logs / distributed tracing platform to track a single request.)
- ALB is not (yet) intergrated with X-Ray
### Target Groups Settings
 - deregisteration_delay.timeout_seconds: time the load balancer waits before deregistering a target
 - slow_start.duration_seconds: (see next slide)
 - load_balancing.algorithm.type: how the load balancer selects targets when routing requests (Round Robin, Least Outstanding Requests)
 - stickiness.enabled
 - stickiness.type: application-based or duration-based cookie
 - stickiness.app_cookie.cookie_name: name of the application cookie
 - stickiness.app_cookie.duration_seconds: application-based cookie expiration period
 - stickiness.lb_cookie.duration_seconds: duration-based cookie expiration period
			
### Slow Start Mode
- 로드밸런서에 인스턴스가 등록되자마자 포워딩하는 게 아닌 일정 시간 기다렸다가 포워딩한다. (웜업 시간을 준다. -> Cold Start일 때, 쓸만한 기능일듯?)
- Slow Start Mode가 종료되는 순간
	- The duration period elapses
	- The target becomes unhealthy
- 시간을 0으로 설정해서 disable 가능하다.
### Request Routing Algorithms - Least Outstanding Requests
- 다음 요청을 받을 인스턴스는 가장 적은 pending/unfinished requests를 가진 인스턴스다.
- ALB, CLB에서 사용 가능하다.
### Request Routing Algorithms - Round Robin
- 순서대로 요청 할당
- ALB, CLB에서 사용 가능하다.
### Request Routing Algorithms - Flow Hash
- 패킷의 출발 IP 주소, 포트, TCP SEQ 번호를 통해서 해싱한다.
- 연결된 동안 계속 동일한 인스턴스에 라우팅된다.
- NLB에서 사용 가능하다.
	
	![image 14 6.png](/img/user/image/image%2014%206.png)
## ALB Rules - Deep Dive
### ALB - Listener Rules

![image 15 6.png](/img/user/image/image%2015%206.png)
- 순서대로 동작한다. (with Default Rule)  
	• Supported Actions (forward, redirect,  
	fixed-response)  
	• Rule Conditions:  
		• host-header  
		• http-request-method  
		• path-pattern  
		• source-ip  
		• http-header  
		• query-string  
### Target Group weighting
- Rule 하나에 속한 Target Group에 weight를 명시해라
- 사용 예시: multiple versions of your app, blue/green deployment
- 어플리케이션에 트래픽 가중치를 조절 가능
![image 16 5.png](/img/user/image/image%2016%205.png)
            
---