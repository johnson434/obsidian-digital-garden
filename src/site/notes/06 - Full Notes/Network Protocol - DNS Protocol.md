---
{"dg-publish":true,"permalink":"/06-full-notes/network-protocol-dns-protocol/","noteIcon":""}
---

# Tags
- [[03 - Tags/Network\|Network]]
- [[03 - Tags/Network Protocol\|Network Protocol]]
---
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

### **DNS 요청 흐름**

![Pasted image 20241030064516.png](/img/user/image/Pasted%20image%2020241030064516.png)

1. 클라이언트가 **Local DNS 서버**에 `example.com` 요청.
2. Local DNS 서버가 캐싱된 IP가 없으면 **Root DNS 서버**에 쿼리.
3. Root DNS 서버 → **TLD DNS 서버 정보 반환**.
4. TLD DNS 서버 → **SLD DNS 서버 정보 반환**.
5. SLD DNS 서버 → **실제 IP 주소 반환**.
6. Local DNS 서버가 **캐싱하여 성능 최적화**.