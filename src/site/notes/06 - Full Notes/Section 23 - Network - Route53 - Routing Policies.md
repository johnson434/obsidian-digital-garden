---
{"dg-publish":true,"permalink":"/06-full-notes/section-23-network-route53-routing-policies/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ultimate AWS Certified SysOps Administrator Associate 2024\|Ultimate AWS Certified SysOps Administrator Associate 2024]]
# 단서 질문 및 답변

### **Route 53에서 지원하는 Routing Policies는 무엇인가?**

- Route 53의 **Routing Policy**는 **DNS 쿼리에 대한 응답을 결정하는 방식**을 정의한다.
- **로드 밸런서(LB)와는 다르게, 실제 트래픽을 분산하는 것이 아니라 DNS 요청을 처리하는 방식만 결정**한다.
- 지원되는 Routing Policies:
    1. **Simple** - 단일 리소스로 라우팅
    2. **Weighted** - 가중치 기반 라우팅
    3. **Failover** - 장애 발생 시 백업 리소스로 전환
    4. **Latency-based** - 지연 시간이 가장 낮은 리소스로 연결
    5. **Geolocation** - 사용자의 위치 기반 라우팅
    6. **Geoproximity** - 리소스와 사용자 간 거리 기반 라우팅
    7. **IP-based** - 사용자의 IP 주소를 기반으로 라우팅
    8. **Multi-Value Answer** - 여러 IP를 반환하여 분산 요청 가능

### **Simple Routing과 Weighted Routing의 차이점은?**

- **Simple Routing**
    
    - 단 하나의 리소스에 트래픽을 라우팅
    - **헬스 체크 미지원**
    - 하나의 도메인에 대해 **여러 개의 IP 주소를 반환 가능** (클라이언트가 선택)
- **Weighted Routing**
    
    - **비율(Weight)을 설정하여 여러 리소스로 트래픽 분배 가능**
    - **헬스 체크 지원**
    - A/B 테스트 및 새로운 애플리케이션 버전 배포 시 유용

---

# 핵심 요약

- Route 53은 **DNS 쿼리에 응답하는 방식**을 결정하는 다양한 Routing Policies를 제공.
- **Simple Routing**은 단순한 1:1 매핑을 제공하며, 여러 IP 주소를 반환 가능.
- **Weighted Routing**은 트래픽을 가중치를 기반으로 여러 리소스에 분배하며, 헬스 체크를 지원.
- **Latency-based Routing**은 사용자와 리소스 간 지연 시간이 가장 낮은 곳으로 연결.
- **Failover Routing**은 기본(primary) 리소스가 실패하면 대체(secondary) 리소스로 전환.
- **Geolocation / Geoproximity**는 사용자 위치나 리소스와의 거리 기반으로 라우팅.
- **IP-based Routing**은 특정 IP 범위(CIDR)를 기반으로 특정 엔드포인트로 라우팅.
- **Multi-Value Answer**는 여러 개의 IP를 반환하여 요청을 분산하지만 ELB 대체용이 아님.

---

# 핵심 필기

## **1. Route 53 - Routing Policies 개요**

### **Routing Policy란?**

- Route 53이 **DNS 요청에 어떻게 응답할지 결정하는 정책**.
- **트래픽을 직접 라우팅하지 않고**, 도메인 요청에 대해 **적절한 IP 주소를 반환**.

> **주의:** Route 53은 로드 밸런서가 아니며, **단순히 DNS 레벨에서 IP를 반환할 뿐**!

### **지원하는 Routing Policies**

|**Routing Policy**|**설명**|
|---|---|
|**Simple**|하나의 리소스로 트래픽 라우팅|
|**Weighted**|특정 가중치(Weight)를 기반으로 여러 리소스로 트래픽 분배|
|**Failover**|Primary 리소스 장애 시 Secondary 리소스로 전환|
|**Latency-based**|지연 시간이 가장 낮은 리소스로 트래픽 라우팅|
|**Geolocation**|사용자의 위치(국가/대륙/주)를 기반으로 라우팅|
|**Geoproximity**|리소스와 사용자 간 거리 기반 라우팅|
|**IP-based**|사용자 IP를 기반으로 특정 리소스로 라우팅|
|**Multi-Value**|여러 개의 IP를 반환하여 클라이언트가 선택|

---

## **2. Simple Routing**

### **Simple Routing 특징**

- **단 하나의 리소스에 트래픽을 라우팅**.
- 하나의 도메인에 **여러 개의 IP 주소 설정 가능** (`example.com → [12.34.23.41, 63.31.46.102]`).
- **클라이언트가 IP를 선택하여 사용**.
- **Alias 설정 시 하나의 AWS 리소스만 지정 가능**.
- **헬스 체크 지원 안됨**.

![Pasted image 20241112213347.png](/img/user/image/Pasted%20image%2020241112213347.png)

---

## **3. Weighted Routing**

### **Weighted Routing 특징**

- **가중치(Weight)를 설정하여 여러 리소스로 트래픽 분배**.
- A/B 테스트 및 새로운 애플리케이션 배포 시 유용.
- **가중치가 0이면 트래픽이 전달되지 않음**.
- **모든 가중치를 0으로 설정하면 균등 분배됨**.
- **헬스 체크와 함께 사용 가능**.

![Pasted image 20241112214353.png](/img/user/image/Pasted%20image%2020241112214353.png)

---
## **4. Latency-based Routing**

### **Latency-based Routing 특징**

- **사용자와 가장 가까운 AWS 리전에 트래픽 라우팅**.
- 사용자의 물리적 위치가 아니라 **네트워크 레이턴시를 기반으로 결정**.
- **헬스 체크 지원**.

![Pasted image 20241112215235.png](/img/user/image/Pasted%20image%2020241112215235.png)

---

## **5. Failover Routing**

### **Failover Routing 특징**
- HTTP Health Checks는 Public 리소스에만 가능하다.
- **Active-Passive 구성** (Primary 인스턴스 장애 시 Secondary로 전환).
- **Primary 리소스의 헬스 체크가 실패하면 Secondary로 트래픽 전환**.

![Pasted image 20241213145605.png](/img/user/image/Pasted%20image%2020241213145605.png)

---

## **6. Geolocation Routing**

### **Geolocation Routing 특징**

- **사용자의 실제 물리적 위치(대륙, 국가, 주)에 따라 트래픽을 라우팅**.
- **컨텐츠 배포 제한(Restrict Content Distribution) 가능**.
- **Default 레코드 설정 필요** (어느 조건에도 맞지 않는 요청 처리).
- 헬스체크와 같이 사용 가능하다.
---

## **7. Geoproximity Routing**

### **Geoproximity Routing 특징**

- **사용자와 리소스 간의 거리 기반으로 트래픽을 라우팅**.
- **Bias 값을 사용하여 특정 리소스로 더 많은 트래픽을 유도 가능**.
    - `Bias +` 값 증가 → 특정 리소스로 트래픽 집중
    - `Bias -` 값 감소 → 트래픽 분산

![Pasted image 20241213155107.png](/img/user/image/Pasted%20image%2020241213155107.png)
![Pasted image 20241213155151.png](/img/user/image/Pasted%20image%2020241213155151.png)
---

## **8. IP-based Routing**

### **IP-based Routing 특징**

- 사용자의 IP 주소(CIDR Block)를 기반으로 특정 리소스로 라우팅.
- 네트워크 비용 절감 및 성능 최적화 가능.
- CIDR Block을 명시하고 해당 IP 주소에 속한 사용자들이 접속할 엔드포인트/locations를 작성한다. (user-IP-to-endpoint mappings);
- 특정 ISP(인터넷 서비스 제공자) 사용자 그룹을 특정 리소스로 라우팅 가능.

![Pasted image 20241213161337.png](/img/user/image/Pasted%20image%2020241213161337.png)

---

## **9. Multi-Value Answer Routing**

### **Multi-Value Routing 특징**
- **여러 개의 IP 주소를 반환하여 클라이언트가 선택 가능**.
- **최대 8개까지 반환 가능**.
- **헬스 체크된 IP 주소만 반환 가능**.
- **Elastic Load Balancer(ELB)의 대체가 아님!** (ELB는 한 개의 엔드포인트에서 로드 밸런싱을 수행).
## 10. Route 53 Health Checks
- HTTP Health Checks는 Public 리소스에만 가능하다.
- Health Check => Automated DNS Failover:
	1. endpoint를 헬스체크 (e.g. application, server, other AWS resource)
	2. 다른 헬스체크를 헬스체크 (Calculated Health Checks)
	3. CloudWatch Alarm을 헬스체크 (Public 리소스가 아닌 Private 리소스는 이 방법을 통해 헬스체크가 가능하다.)
- Health Check는 자신만의 매트릭스를 가지므로 CloudWatch를 통해서 매트릭스를 볼 수 있다.
---
# 참고 자료

- [AWS 공식 문서 - Route 53](https://docs.aws.amazon.com/route53/)
- [AWS 공식 블로그 - Routing Policies](https://aws.amazon.com/blogs/)

---

# 주석

- **Route 53의 모든 Routing Policy를 정리하였으며, 핵심 개념을 쉽게 정리**.
- **각 Routing Policy의 주요 특징과 이미지 참조를 포함**.
- **Route 53은 단순히 DNS 요청을 처리하는 것이지, 로드 밸런서처럼 트래픽을 직접 분산하는 것이 아님**.
