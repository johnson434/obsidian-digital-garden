---
{"dg-publish":true,"permalink":"/06-full-notes/burp-suite-proxy-setting/","noteIcon":""}
---

## Tags
- [[03 - Tags/Burp Suite\|Burp Suite]]
---
## 단서 질문 및 답변
- Burp Suite란?
- 프록시 서버란?
---
## 핵심 요약
- Burp Suite는 프록시 서버를 통해서 웹브라우저의 요청을 중간에서 가로채거나 API 요청 캡처 등 다양한 보안 관련 기능을 제공한다.
- Burp Suite를 사용하기 위해선 웹브라우저가 Burp Suite의 프록시 서버를 사용하도록 설정해야 한다.
---
## 핵심 필기
### Burp Suite란?
API 전문가 혹은 보안 전문가들이 사용하는 도구 모음으로 다음과 같은 기능을 가진다.
1. API 요청 캡처
2. Web Application crawling
3. API fuzzing
### 프록시 서버 설정하기
Burp Suite를 실행하면 프록시 서버가 실행된다. 프록시 서버는 클라이언트와 서버 간에 발생하는 트래픽을 가로채거나 스누핑할 수 있다. 흔히 말하는 중간자(Man In the Middle) 역할을 수행한다.
![Pasted image 20250729200850.png](/img/user/image/Pasted%20image%2020250729200850.png)

Burp Suite의 프록시 서버가 웹브라우저의 트래픽을 가로채기 위해선 **웹브라우저에서 프록시 서버 설정이 필요하다.** Firefox나 Chrome 등 대부분의 웹브라우저는 별다른 확장 프로그램을 사용할 필요 없이 설정 메뉴에서 프록시 메뉴를 직접 설정할 수 있다. 하지만, **FoxyProxy와 같은 확장 도구를 사용하는 게 편리하다.**
![Pasted image 20250729201128.png](/img/user/image/Pasted%20image%2020250729201128.png)
Foxy Proxy를 사용하면 프록시 서버 설정을 간단하게 켰다 끌 수 있다.

### Foxy Proxy 설정
![Screenshot from 2025-07-29 20-12-13.png](/img/user/image/Screenshot%20from%202025-07-29%2020-12-13.png)
기본적으로 Burp Suite의 프록시 서버는 로컬호스트의 8080 포트를 사용한다. 
따라서, Foxy Proxy 설정을 127.0.0.1:8080으로 설정한다.

이제 프록시 서버를 설정했으니 `google.com`에 접속해보자.
![Screenshot from 2025-07-29 23-08-50.png](/img/user/image/Screenshot%20from%202025-07-29%2023-08-50.png)
프록시 서버를 설정하니 `google.com`에 접속이 불가능해졌다. 에러 메시지를 보면 `PortSwigger CA` 때문에 이슈가 발생했다고 나온다. 왜 이런 일이 발생할까?

### 웹브라우저는 Burp Suite의 인증서를 신뢰하지 않는다.
![Pasted image 20250730121216.png](/img/user/image/Pasted%20image%2020250730121216.png)
**프록시 서버를 사용하지 않는 경우,** 웹 브라우저는 직접 서버와 TLS 핸드셰이크를 수행하며 이 과정에서 서버가 제공하는 인증서를 전달 받는다. 브라우저는 해당 인증서가 신뢰할 수 있는 CA(Certificate Authority)에서 발급 받은 것인지 검증한다. 그렇다면 프록시 서버를 사용하면 이 과정이 어떻게 변할까?

![Pasted image 20250730125540.png](/img/user/image/Pasted%20image%2020250730125540.png)
정답은 **사용자와 프록시 서버 사이에 TLS 핸드셰이크가 발생**하고 이 과정에서 프록시 서버가 전달한 인증서를 브라우저가 검증한다. 하지만 기본적으로 **웹브라우저는 Burp Suite가 사용하는 PortSwigger CA를 신뢰하지 않는다.** 따라서, Burp Suite의 루트 인증서(PortSwigger CA)를 브라우저의 신뢰 목록에 등록해야 한다.
### FireFox에 Burp Suite CA 추가
Firefox에서 Burp Suite의 인증서를 추가하겠다.
![Pasted image 20250730000402.png](/img/user/image/Pasted%20image%2020250730000402.png)
1. FireFox의 설정으로 들어가서 보안 설정으로 들어간다.
2. 밑으로 쭉 내리면 인증서 구간이 나온다.
3. 인증서 보기를 클릭하면 위처럼 현재 웹브라우저에서 신뢰하는 CA 목록이 나온다.
4. Import를 누르면 인증서를 추가할 수 있다.

![Pasted image 20250730000427.png](/img/user/image/Pasted%20image%2020250730000427.png)
5. 위처럼 해당 CA를 신뢰한다고 설정하면 끝이다.

이제 Burp Suite 인증서 신뢰 설정을 끝냈으니 다시 `google.com`에 접속해보자.
![Pasted image 20250730000527.png](/img/user/image/Pasted%20image%2020250730000527.png)
제대로 접속되는 걸 볼 수 있다. 이제 인증서를 자세히 들여다 보자.

![Pasted image 20250730115957.png](/img/user/image/Pasted%20image%2020250730115957.png)
프록시 서버를 사용하지 않고 직접 `google.com`에 요청하면, 서버는 `Google Trust Services`에서 발급 받은 인증서를 전달한다. 이 인증서는 웹브라우저에 기본적으로 등록된 신뢰할 수 있는 CA가 서명한 것이므로 별다른 경고 없이 정상적으로 HTTPS 통신이 이루어진다.

반면, Burp Suite의 프록시 서버를 거쳐 같은 요청을 보내면 브라우저가 받는 인증서는 `PortSwigger CA`에서 발급 받은 인증서다. 이 CA는 방금 직접 우리가 브라우저에 등록해둔 루트 인증서로 신뢰 목록에 추가했기 때문에 브라우저가 해당 인증서를 유효한 것으로 판단하고 안전하게 통신을 허용하게 된다.

### Burp Suite로 트래픽 Intercept하기
![Pasted image 20250730130351.png](/img/user/image/Pasted%20image%2020250730130351.png)
이제 Burp Suite에서 Intercept를 키면 요청을 가로챌 수 있다.

---
## 참고 자료
- [Installing Burp's CA Certificate](https://portswigger.net/burp/documentation/desktop/external-browser-config/certificate)