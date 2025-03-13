---
{"dg-publish":true,"permalink":"/06-full-notes/section-22-network-route53-dns/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ultimate AWS Certified SysOps Administrator Associate 2024\|Ultimate AWS Certified SysOps Administrator Associate 2024]]
---
# 단서 질문 및 답변

### **DNS란 무엇인가?**
- **DNS(Domain Name System)**는 **도메인 네임을 IP 주소로 변환**하는 서비스.  
- 사람이 이해하기 쉬운 도메인 이름(`example.com`)을 컴퓨터가 이해할 수 있는 IP 주소(`192.0.2.44`)로 변환하는 역할.  

### **CNAME과 Alias의 차이점은?**
- **CNAME (Canonical Name) 레코드**  
  - **Non-Root 도메인에서만 사용 가능** (예: `app.example.com`)  
  - 다른 **호스트네임을 가리키는 DNS 레코드** (예: `app.example.com` → `service.provider.com`)  
  - 대상 도메인은 반드시 A/AAAA 레코드를 가져야 함.  

- **Alias 레코드**  
  - **Root 및 Non-Root 도메인 모두 사용 가능** (예: `example.com`, `app.example.com`)  
  - **AWS 리소스**(ELB, CloudFront, API Gateway 등)를 직접 가리킬 수 있음.  
  - **무료 사용 가능**, AWS에서 **IP 변경 자동 반영**, **헬스체크 가능**.  
  - CNAME과 달리 **IP 주소(A/AAAA)와 직접 연결** 가능.  

---
# 핵심 요약
- **DNS(Domain Name System)**는 **도메인을 IP로 변환하는 시스템**.  
- **Route 53**은 **AWS의 관리형 DNS 서비스**로, 헬스 체크 및 100% SLA 제공.  
- **TTL (Time To Live)**을 이용해 DNS 응답을 캐싱하여 트래픽 관리 가능.  
- **CNAME**은 Non-Root 도메인에서만 사용 가능하며 다른 도메인으로 매핑.  
- **Alias**는 Root & Non-Root 도메인 모두 가능하며 AWS 리소스를 직접 연결.  
- **Alias는 무료이며, AWS 리소스의 IP 변경을 자동으로 반영**.  
- **Route 53 Hosted Zone**은 Public과 Private으로 나뉘며, 트래픽을 라우팅하는 컨테이너 역할.  

---
# 핵심 필기

## **1. DNS 개요**
### **DNS (Domain Name System)란?**
- **도메인 네임을 IP 주소로 변환**하는 서비스.  
- 사람이 기억하기 쉬운 도메인(`example.com`)을 숫자로 된 IP(`192.168.1.1`)로 변환.  
- **주요 DNS 용어**
  - **Domain Registrar**: 도메인 등록 서비스 (예: AWS Route 53, GoDaddy).  
  - **DNS Records**: A, AAAA, CNAME, NS 등.  
  - **Zone File**: DNS 레코드를 포함한 파일.  
  - **Name Server**: DNS 쿼리를 처리하는 서버 (Authoritative / Non-Authoritative).  
  - **TLD(Top-Level Domain)**: `.com`, `.net`, `.org`.  
  - **SLD(Second-Level Domain)**: `amazon.com`, `google.com`.  
![Pasted image 20241030064053.png](/img/user/image/Pasted%20image%2020241030064053.png)
### **DNS 요청 흐름**
![Pasted image 20241030064516.png](/img/user/image/Pasted%20image%2020241030064516.png)  
1. 클라이언트가 **Local DNS 서버**에 `example.com` 요청.  
2. Local DNS 서버가 캐싱된 IP가 없으면 **Root DNS 서버**에 쿼리.  
3. Root DNS 서버 → **TLD DNS 서버 정보 반환**.  
4. TLD DNS 서버 → **SLD DNS 서버 정보 반환**.  
5. SLD DNS 서버 → **실제 IP 주소 반환**.  
6. Local DNS 서버가 **캐싱하여 성능 최적화**.  

---

## **2. Amazon Route 53 개요**
### **Route 53 특징**
- AWS의 완전 관리형 DNS 서비스.  
- 높은 가용성, 확장성, 헬스 체크 기능 제공.  
- 도메인 등록(Registrar) 기능 지원.  
- AWS에서 유일하게 100% SLA[^1]보장.  
- 53은 DNS 서버의 포트(53번 포트)에서 유래됨.  

### **Route 53 - Records**
- 도메인 트래픽을 라우팅하는 방법.  
- 주요 DNS 레코드 유형  

| **Record Type** | **설명** |
|---------------|---------|
| **A** | hostname을 IPv4와 매핑 |
| **AAAA** | hostname을 IPv6와 매핑 |
| **CNAME** | hostname을 **다른 hostname**에 매핑 (Root Domain 사용 불가) |
| **NS** | Hosted Zone을 위한 Name Server (트래픽 라우팅 관리) |

### **Route 53 - Hosted Zones**
- **DNS 레코드를 관리하는 컨테이너 역할**.  
- **Public Hosted Zone**  
  - **인터넷 트래픽을 처리** (`application1.mydomain.com`).  
- **Private Hosted Zone**  
  - **VPC 내부 트래픽만 처리** (`application1.company.internal`).  
- **Hosted Zone 비용**: 한 달에 $0.50.  
- Public Hosted Zone은 인터넷과 VPC을 포함한 모든 네트워크에서 DNS 쿼리가 가능하지만 Private Hosted Zone은 VPC 내부에서만 요청이 가능합니다.[^2]

![Pasted image 20241030205141.png](/img/user/image/Pasted%20image%2020241030205141.png)  

---
## **3. Route 53 - TTL (Time To Live)**
### **TTL 개념**
- 클라이언트가 DNS 응답을 캐싱하는 기간.  
- 클라이언트는 TTL 동안 동일한 레코드를 캐싱하여 성능 최적화.  
![Pasted image 20241030205936.png](/img/user/image/Pasted%20image%2020241030205936.png)  
- 별칭 레코드(Alias Records)를 제외하곤 TTL은 DNS 레코드에 필수다.
### **TTL 값 선택 가이드**
| **TTL 값** | **장점** | **단점** |
|------------|---------|---------|
| **높은 TTL (24시간)** | Route 53 요청 감소 | 오래된 IP 캐싱 가능성 |
| **낮은 TTL (60초)** | 빠른 변경 반영 | Route 53 트래픽 증가 |

> **Alias 레코드**는 AWS에서 TTL을 자동 설정하므로 수동 설정 불가능.  

---

## **4. CNAME vs Alias**
- 둘 다 AWS Route53에서 제공하는 레코드 타입이다.
### **CNAME vs Alias 차이점**

| **특징** | **CNAME** | **Alias** |
|----------|----------|---------|
| **용도** | **호스트네임 → 다른 호스트네임** | **호스트네임 → AWS 리소스** |
| **Root Domain 사용** | ❌ 불가능 (Non-Root 전용) | ✅ 가능 (Root & Non-Root) |
| **AWS 리소스 연결** | ❌ 불가능 | ✅ 가능 (ELB, CloudFront, API Gateway) |
| **무료 여부** | 비용 발생 | ✅ 무료 |
| **헬스 체크** | ❌ 불가능 | ✅ 가능 |
| **TTL 설정** | 직접 설정 가능 | ❌ AWS 자동 설정 |

### **Route 53 - Alias Records**
- **AWS 리소스에 직접 연결 가능**.  
- **IP 변경 자동 반영**.  
- **A/AAAA(IPv4/IPv6) 타입만 지원**.  
- **TTL은 Route 53이 자동 설정**.  

![Pasted image 20241030215142.png](/img/user/image/Pasted%20image%2020241030215142.png)  

### **Alias Records 지원 대상**
- **Elastic Load Balancer (ELB)**  
- **CloudFront Distributions**  
- **API Gateway**  
- **Elastic Beanstalk 환경**  
- **S3 웹사이트**  
- **VPC Interface Endpoints**  
- **Global Accelerator**  
- **Route 53 Hosted Zone 내부 레코드**  

🚨 **EC2 Public DNS는 Alias로 사용할 수 없음!**  

![Pasted image 20241030215558.png](/img/user/image/Pasted%20image%2020241030215558.png)  

---
# 참고 자료
- [AWS 공식 문서 - Route 53](https://docs.aws.amazon.com/route53/)  
- [AWS 공식 블로그 - CNAME vs Alias](https://aws.amazon.com/blogs/)  

---
# 주석
[^1]: Service Level Availability

[^2]: Resolver 설정을 통해서 온프로미스에서 Private Hosted Zone에 쿼리가 가능하게 만들 수 있다.
