---
{"dg-publish":true,"permalink":"/06-full-notes/section-21-identity-3/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ultimate AWS Certified SysOps Administrator Associate 2024\|Ultimate AWS Certified SysOps Administrator Associate 2024]]
# 단서 질문 및 답변

### AWS Cognito란?

- AWS에서 제공하는 **서버리스 인증 및 권한 부여 서비스**로, 사용자 관리(Authentication)와 AWS 리소스 접근(Authorization)을 처리할 수 있음.
- **Cognito User Pools(CUP)** → 사용자 로그인(Authentication) 제공, JWT 발급
- **Cognito Identity Pools(CIP)** → AWS 자격 증명 발급(Authorization), IAM 역할 부여

### AWS User Pools vs AWS Identity Pools

- **User Pools(CUP)** → 인증(Authentication) 담당, 로그인 및 사용자 관리 (예: JWT 반환)
- **Identity Pools(CIP)** → 권한 부여(Authorization) 담당, AWS 리소스 접근을 위한 IAM Credentials 발급

---

# 핵심 필기

## **Cognito User Pools (CUP) - User Features**

- **유저 인증을 위한 서버리스 데이터베이스** 제공
- 로그인 방식: 유저명/이메일 + 패스워드
- 패스워드 리셋 및 이메일/전화번호 검증
- MFA 지원 (Multi-Factor Authentication)
- **소셜 로그인 연동** (Facebook, Google, Apple, SAML 등)
- Credential(cred)이 유출되었을 경우 해당 유저를 블록 가능
- 로그인 시 **JWT 토큰** 반환
- ![Pasted image 20241029111339.png](/img/user/image/Pasted%20image%2020241029111339.png)

## **Cognito User Pools (CUP) - Integrations**

- **API Gateway & ALB 통합 가능**
- ![Pasted image 20241029111607.png](/img/user/image/Pasted%20image%2020241029111607.png)
    1. 클라이언트 → Cognito에 로그인 요청 및 JWT Token 수신
    2. 클라이언트 → API Gateway/ALB에 JWT Token 포함 요청
    3. API Gateway/ALB → Cognito에 Token 검증 요청
    4. 검증 완료 시 Backend/Target Group으로 요청 전달

---

## **Cognito Identity Pools (Federated Identities)**

- **AWS Credentials 발급을 위한 Identity 관리 서비스**
- 유저가 **Identity를 얻은 후 AWS Credentials를 발급받아 AWS 리소스 접근**
- Identity Pool(Identity Source) 옵션:
    - Public Identity Providers (Facebook, Google, Apple)
    - Amazon Cognito User Pool의 유저
    - OIDC, SAML Providers
    - **인증되지 않은 게스트 사용자(unauthenticated users)도 허용 가능**
- **Credentials를 얻으면 AWS 리소스에 직접 접근하거나 API Gateway 통해 접근 가능**
    - IAM 정책은 Cognito에 정의됨
    - User ID 기반으로 **세분화된 권한 부여 가능**

---

## **Cognito Identity Pools - Diagram**

- ![Pasted image 20241029133205.png](/img/user/image/Pasted%20image%2020241029133205.png)
    1. 사용자가 **User Pool 로그인 → JWT Token 획득**
    2. AWS 리소스 접근을 위해 **Identity Pool에 Token 교환 요청**
    3. Identity Pool → User Pool에서 받은 **JWT 검증**
    4. Identity Pool → **AWS STS에서 IAM Credentials 발급**
    5. 사용자는 발급된 Credentials를 이용하여 AWS 리소스 접근

---

## **Cognito Identity Pools - IAM Roles**

- 인증된 유저 & 게스트 유저에 대해 **IAM Role을 정의**
- User ID 기반으로 IAM Role 할당 가능
- Policy Variables 활용하여 접근 권한을 세분화 가능
- ![Pasted image 20241029134348.png](/img/user/image/Pasted%20image%2020241029134348.png)

---

## **Cognito Identity Pools - Guest User Example**

- 게스트 유저 IAM 정책 예시
    - ![Pasted image 20241029143137.png](/img/user/image/Pasted%20image%2020241029143137.png)
    - ![Pasted image 20241029143223.png](/img/user/image/Pasted%20image%2020241029143223.png) (S3)
    - ![Pasted image 20241029143259.png](/img/user/image/Pasted%20image%2020241029143259.png) (DynamoDB)

---

## **Cognito User Pools vs Identity Pools**

|**구분**|**Cognito User Pools (CUP)**|**Cognito Identity Pools (CIP)**|
|---|---|---|
|**역할**|사용자 인증 (로그인)|AWS 리소스 접근 권한 부여|
|**기능**|이메일/패스워드 로그인, MFA, 소셜 로그인|IAM Role을 활용한 AWS Credentials 발급|
|**출력 결과**|**JWT 토큰 반환**|**AWS Access Key / Secret Key / Session Token 반환**|
|**통합 가능 서비스**|API Gateway, ALB|AWS STS, IAM|
|**게스트 접근**|불가능|**가능 (Unauthenticated Users 허용 가능)**|

**📌 User Pools(CUP) + Identity Pools(CIP) = AWS 기반 인증 + 권한 부여 통합**

---

# 핵심 요약

- **Cognito User Pools (CUP)** → AWS에서 제공하는 **서버리스 인증 서비스** (JWT 반환)
- **Cognito Identity Pools (CIP)** → AWS에서 제공하는 **IAM 권한 부여 서비스** (AWS Credentials 반환)
- CUP은 **사용자 인증**, CIP는 **AWS 리소스 접근 권한 부여**
- 두 서비스를 조합하면 **완전한 인증 및 권한 부여 시스템 구축 가능**

---

# 참고 자료

- [AWS 공식 문서 - Amazon Cognito](https://docs.aws.amazon.com/cognito/index.html)
- [AWS 공식 문서 - Cognito Identity Pools](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-identity.html)
- [AWS 공식 블로그 - Authentication vs Authorization](https://aws.amazon.com/blogs/)

---