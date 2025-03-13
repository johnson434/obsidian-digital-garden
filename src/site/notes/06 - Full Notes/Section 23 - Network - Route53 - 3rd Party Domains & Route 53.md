---
{"dg-publish":true,"permalink":"/06-full-notes/section-23-network-route53-3rd-party-domains-and-route-53/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ultimate AWS Certified SysOps Administrator Associate 2024\|Ultimate AWS Certified SysOps Administrator Associate 2024]]
---
# 단서 질문 및 답변

### **Domain Registrar와 DNS 서비스의 차이점은?**
- **Domain Registrar**는 **도메인 등록을 관리**하는 서비스 (예: GoDaddy, 가비아, Amazon Registrar Inc.).
- **DNS 서비스**는 도메인의 **DNS 레코드를 관리**하며, Domain Registrar와 별개로 사용 가능.
- 예: **도메인은 가비아에서 구매하고, DNS 레코드는 AWS Route 53에서 관리 가능**.

### **S3 웹사이트를 Route 53과 연결하는 방법은?**
1. **S3 버킷을 도메인 이름과 동일하게 생성** (예: `acme.example.com`).
2. **S3 웹사이트 호스팅을 활성화 및 퍼블릭 액세스 허용**.
3. **Route 53에서 Alias Record를 S3 웹사이트 엔드포인트로 설정**.
4. **HTTP 트래픽만 가능하며, HTTPS를 사용하려면 CloudFront 필요**.

### **Hybrid DNS란 무엇인가?**
- **AWS VPC 내의 Route 53 Resolver**와 **온프레미스 네트워크의 DNS Resolver** 간 DNS 쿼리 해결을 지원.
- 사용 사례:
  - **VPC 내부에서 온프레미스 네트워크의 DNS 조회**.
  - **온프레미스에서 AWS Private Hosted Zone 레코드 조회**.

---
# 핵심 요약
- **도메인 등록(Domain Registrar)과 DNS 레코드 관리는 별개로 진행 가능**.
- **Route 53은 서드파티 도메인의 DNS 레코드를 관리할 수 있음**.
- **S3 웹사이트 호스팅은 Route 53 Alias Record를 사용하여 연결 가능하지만, HTTPS 지원 불가(CloudFront 필요)**.
- **Hybrid DNS는 VPC와 온프레미스 네트워크 간 DNS 쿼리를 해결하는 방식**.
- **Resolver Endpoints**:
  - **Inbound** → 온프레미스에서 AWS Private Hosted Zone 조회 가능.
  - **Outbound** → AWS에서 온프레미스 DNS 서버로 쿼리 전송 가능.
- **Resolver Rules**를 사용하여 특정 DNS 쿼리를 지정된 대상에 포워딩 가능.

---
# 핵심 필기

## **1. Domain Registrar vs DNS Service**
### **Domain Registrar 개념**
- **도메인 등록 및 관리** (예: GoDaddy, Amazon Registrar Inc., 가비아).
- 일반적으로 자체 **DNS 서비스 제공**.
- 그러나, **DNS 서비스는 별도 사용 가능** (예: 도메인은 가비아에서 등록하고 Route 53에서 관리).

![Pasted image 20241213193020.png](/img/user/image/Pasted%20image%2020241213193020.png)

---

## **2. S3 Website with Route 53**
### **S3 웹사이트와 Route 53 연결 방법**
1. **S3 버킷을 도메인과 동일한 이름으로 생성** (예: `acme.example.com`).
2. **S3 웹사이트 호스팅 활성화 및 퍼블릭 액세스 허용**.
3. **Route 53에서 Alias Record를 S3 웹사이트 엔드포인트로 설정**.
4. **HTTPS를 사용하려면 CloudFront 필요** (S3 웹사이트 자체적으로 HTTPS 미지원).

---

## **3. Route 53 Resolvers & Hybrid DNS**
### **Hybrid DNS 개념**
- Route 53 Resolver는 **VPC 내 및 외부 네트워크와의 DNS 쿼리를 처리**.
- **AWS 내부에서 자동 응답하는 DNS 요청**:
  - **EC2 인스턴스의 Local domain**.
  - **Private Hosted Zone 레코드**.
  - **Public Name Server 레코드**.

![Pasted image 20241213195748.png](/img/user/image/Pasted%20image%2020241213195748.png)

- **Hybrid DNS**는 **VPC(Route 53 Resolver)와 온프레미스 네트워크의 DNS Resolver 간 DNS 쿼리를 해결**.
- 지원하는 네트워크:
  - **VPC 및 Peered VPC**.
  - **온프레미스 네트워크 (Direct Connect 또는 VPN 연결된 환경)**.

---

## **4. Resolver Endpoints(시험 중요 파트)**
### **Resolver Endpoints란?**
- **온프레미스 네트워크와 AWS VPC 간의 DNS 쿼리 해결을 지원**.

#### **Inbound Endpoint**
- **온프레미스 DNS Resolver가 AWS Route 53에 DNS 쿼리를 보낼 수 있도록 지원**.
- **Private Hosted Zone의 레코드를 온프레미스에서 조회 가능**.

![Pasted image 20241214131406.png](/img/user/image/Pasted%20image%2020241214131406.png)

1. **온프레미스 서버에서 DNS 쿼리를 실행**.
2. **온프레미스 DNS Resolver가 AWS의 Resolver Inbound Endpoint로 요청 전송**.
3. **Route 53 Resolver가 Private Hosted Zone을 조회하여 응답 반환**.

---

#### **Outbound Endpoint**
- **AWS에서 특정 DNS 도메인의 쿼리를 온프레미스 네트워크로 포워딩 가능**.
- **Resolver Rules을 설정하여 특정 DNS 도메인을 온프레미스 DNS Resolver로 전달**.
- 동일한 Region의 VPC를 1개 이상 사용합니다.
- 가용성을 위해서 2개 가용 영역을 2개 이상 사용합니다.
- 특정 IP로 초당 10,000 쿼리까지 지원합니다.

![Pasted image 20241214131741.png](/img/user/image/Pasted%20image%2020241214131741.png)

---

## **5. Route 53 - Resolver Rules**
### **Resolver Rules란?**
- **Route 53 Resolver가 특정 DNS 쿼리를 특정 DNS 서버로 포워딩하도록 설정**.

### **Resolver Rule 유형**
1. **Conditional Forwarding Rules**
   - 특정 도메인과 그 하위 도메인에 대한 DNS 요청을 특정 IP 주소로 전달.
   - 예: `acme.example.com` 도메인의 모든 DNS 요청을 `172.16.0.10`으로 포워딩.

2. **System Rules**
   - 특정 도메인에 대한 Resolver Rules를 오버라이드.
   - 예: `acme.example.com`을 향한 모든 쿼리를 특정 IP로 포워딩하는 설정을 무시.

3. **Auto-defined System Rules**
   - AWS 내부에서 자동 정의된 규칙.
   - 예: **AWS Private Hosted Zone 레코드를 처리하는 내부 규칙**.

### **Resolver Rules 충돌 시 처리**
- **여러 개의 Rule이 일치할 경우, 가장 정확히 일치하는 Rule이 적용됨**.

### **AWS RAM(Resource Access Manager)를 통한 공유**
- **Resolver Rules를 AWS RAM을 통해 다른 계정과 공유 가능**.

---
# 참고 자료
- [AWS 공식 문서 - Route 53](https://docs.aws.amazon.com/route53/)  
- [AWS 공식 블로그 - Hybrid DNS 및 Resolver Endpoints](https://aws.amazon.com/blogs/)  

---
# 주석