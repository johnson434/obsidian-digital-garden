---
{"dg-publish":true,"permalink":"/06-full-notes/it-1-osi-7-vs-tcp-ip-4/","noteIcon":""}
---

[[03 - Tags/IT Engineer를 위한 네트워크\|IT Engineer를 위한 네트워크]]
# 핵심 요약
- **프로토콜이란?**
    통신을 위해 사용되는 규약
    물리적 측면에서 프로토콜: 전송 매체, 신호 규약, 회선 규격 등
    논리적 측면에서 프로토콜: 노드끼리 통신을 위한 프로토콜 규격
- **OSI 7계층이란?**
    네트워크 프로토콜의 규격화를 통해 통신을 간편히 하기 위해서 사용된다. 
    계층이 나뉘어져 있어서 각 계층은 독립적이므로 재사용이 가능하고 별개로 개발이 가능하다.
- **TCP/IP 모델이란**
    OSI 7계층이 물리, 데이터, 네트워크, 전송, 세션, 표현, 응용 계층으로 나눈 것과는 달리 물리, 데이터 계층을 합치고 네트워크, 전송, 애플리케이션까지 총 4개 계층으로 나눈다. 네트워크 통신에 초점을 둔 모델이다.
- **OSI 7계층에 대해서 간단히 설명**
    **물리:** 전기 신호를 통해 데이터를 주고 받는 영역. 전기 신호에 전송이 핵심이다. 리피터, 허브 등이 포함된다.
    데이터 단위는 비트
    **데이터:** MAC 주소를 통해 데이터의 송신지와 수신지를 판별한다. 스위치의 경우 이를 통해 수신자가 아닌 노드엔 데이터를 전달하지 않는다. NIC에서 전기 신호를 비트로 변환하고 주소를 파악 후에 자신의 주소와 일치하지 않으면 데이터를 수신하지 않는다.
    데이터 단위는 프레임
    **네트워크:** MAC 주소와는 달리 논리적인 주소인 IP를 사용한다. 데이터 단위는 패킷이다.
    **전송:** 이전 계층들이 데이터의 전송 주소에 신경 썼다면 전송 계층은 데이터의 전송 방식에 신경 쓴다.
    **세션:** 응용 프로세스 간에 연결과 연결 해제를 처리하는 계층.
    **표현:** 데이터의 표현 방식을 다루는 계층. MIME 인코딩, 암호화 등이 있다.
    **응용:** 응용프로세스의 처리를 다루는 계층
- **헤더에 필수적으로 들어가야할 요소**
    1. 데이터의 정보: 전송 계층의 경우 SEQ(전송 순서) ACK(도착 순서)를 헤더에 포함한다.
    2. 상위 프로토콜: 디캡슐레이션 과정에서 데이터를 상위 계층으로 보내기 위해선 상위 프로토콜이 무엇인지를 알아야 한다.
# 핵심 필기
## 1.1 네트워크 구성도 살펴보기
### 1.1.1 홈네트워크(p11)
![Untitled 31.png](/img/user/image/Untitled%2031.png)
외부 인터넷과 연결되는 케이블을 모뎀을 통해 받고 모뎀에서 케이블을 통해 공유기에 네트워크를 연결.
노드(통신 기기)에선 무선의 경우 무선랜카드를 통해 공유기와 연결하거나 유선의 경우 유선랜카드를 통해 공유기와 케이블로 연결합니다.
### 1.1.2 데이터 센터 네트워크
안정적이고 빠른 데이터 전송을 지원해야 하므로 이중화 통신(Full Duplex)을 사용합니다.
기존에는 3계층 구성을 사용했지만 스케일 아웃과 가상화 기술에 도입으로 인해 2계층 구성인 스파인-리프(Spine-Leaf) 구조로 변화되었습니다.

---
## 1.2 프로토콜

**프로토콜(Protocol)** 은 통신을 위한 규정 규약을 의미한다. 최근엔 이더넷-TCP/IP 기반 프로토콜을 많이 사용합니다.
**물리적 측면에서 프로토콜**: 전송 매체, 신호 규약, 회선 규격 등. 이더넷이 널리 쓰인다.
**논리적 측면에서 프로토콜**: 노드끼리 통신을 위한 프로토콜 규격. TCP/IP가 널리 쓰인다.

과거엔 자원의 빈약함으로 인해 비트 기반 프로토콜을 사용했지만 현재는 문자 기반 프로토콜(HTTP, SMTP) 등이 많이 쓰인다.
TCP/IP의 경우엔 프로토콜이 아닌 프로토콜 스택이라고 한다. 왜냐면 3계층인 IP 프로토콜과 4계층인 TCP 프로토콜의 조합이므로(프로토콜의 묶음을 프로토콜 스택이라고 한다).
![Untitled 1 17.png](/img/user/image/Untitled%201%2017.png)

---
## **1.3 OSI 7계층과 TCP/IP**
### **1.3.1 OSI 7계층(P16)**
통합되지 않은 네트워크 규약 때문에 발생하는 문제를 해결하기 위해 도입된 규격화 방안.
현재는 대부분 TCP/IP 규약을 사용한다.

![Untitled 2 15.png](/img/user/image/Untitled%202%2015.png)
모듈화를 통해 계층별 모듈의 재사용이 가능해진다.
OSI 계층은 다시 두 가지 계층으로 나눌 수 있다.
1. 1~4 계층(Data flow 계층, 하위 계층): 데이터의 전달에 중점을 둔 계층. 물리, 데이터, 네트워크 전송 계층이 속한다. 데이터의 표현은 중요치 않다.
2. 5~7 계층(Application 계층, 상위 계층): 데이터의 표현에 중점을 둔 계층. 세션, 표현, 어플리케이션 계층이 속한다. 데이터의 전송을 중요치 않다. 일반 백엔드 개발자들이 TCP/UDP 신경을 안쓰는 것처럼

![Untitled 3 13.png](/img/user/image/Untitled%203%2013.png)
### **1.3.2 TCP/IP 프로토콜 스택(P19)**
값싸고 성능이 우수한 TCP/IP와 이더넷이 많이 채택 되었다.
![Untitled 4 12.png](/img/user/image/Untitled%204%2012.png)
TCP/IP 모델은 OSI처럼 계층을 세세하게 나눌 필요는 없다고 생각하면서 나온 모델이다.
실제로 물리 계층, 데이터 링크 계층은 웹개발을 해본 사람들은 크게 신경 써본 적이 없을 것이다. 인터넷 계층(IP)과 트랜스포트 계층(TCP, UDP)이 핵심이다. 따라서, 핵심으로만 분리를 했다.

---
## 1.4 OSI 7계층별 이해하기

![Untitled 5 9.png](/img/user/image/Untitled%205%209.png)

### **1.4.1 1계층(피지컬 계층)**
**핵심 목표:** 전기 신호 전달이 핵심
**주요 장비:** 리피터, 허브, 케이블, 커넥터, 트랜시버, 탭 등
**데이터 형식**: 비트(Bit)
허브의 경우, 스위치와는 달리 단순하게 연결된 모든 노드에 전기 신호를 전송할뿐 다른 역할을 하지 않는다.
### **1.4.2 2계층(데이터 계층)**

**핵심 목표:**
1. MAC 주소를 사용해서 통신한다.
2. 출발지와 도착지 주소를 확인하고 데이터 처리를 수행한다.
3. 전기 신호를 우리가 알아볼 수 있는 데이터 형태로 처리한다.
4. 정확한 주소로 데이터를 보내는 것에 초점이 맞춰져 있다.
5. 수신자가 데이터를 받을 수 있는지 확인하고 플로 컨트롤(Flow Control) 처리를 한다.

**주요 장비:**
1. NIC(네트워크 인터페이스 카드)
2. 스위치(Switch)
![Untitled 6 8.png](/img/user/image/Untitled%206%208.png)
### **1.4.3 3계층(네트워크 계층)**
논리적 주소를 사용하는데 여기엔 IP가 속합니다. 데이터 통신을 할 땐 두 가지 주소가 사용되는데 2계층인 MAC 주소와 3계층인 IP 주소입니다. IP 주소를 통해 노드가 속한 네트워크를 찾고 여기서 ARP(2계층)을 통해 IP 주소를 MAC 주소로 변환한 후에 해당 MAC 주소를 가진 노드로 데이터를 전송합니다.

**주요 장비:**
1. 라우터: IP 주소를 통해 최적화 된 경로를 찾는다.

### **1.4.4 4계층(전송 계층)**
1,2,3 계층이 신호와 데이터를 올바른 주소로 보내는데 집중한다면 4계층은 데이터를 올바르게 주고 받았는지 확인한다(패킷의 손실 유무 확인).
패킷의 전송 순서를 **시퀀스 번호(Sequnce Number)**
패킷의 받는 순서를 **ACK 번호(Acknowledgement Number)**
또한, 포트 번호를 사용해서 어떤 애플리케이션에 전달하는지 명시한다.

**주요 장비:** 로드밸런서, 방화벽

### **1.4.5 5계층(세션 계층)**
종단 노드의 프로세스 간의 연결을 성립하도록 도와주고 이를 안정적으로 유지하도록 관리하고 작업이 끝나면 이를 끊는다.(TCP/IP 세션을 만들고 끊는 것도 주요 역할)
에러로 중단된 통신에 대한 에러 복구와 재전송도 수행합니다.

### **1.4.6 6계층(표현 계층)**
표현 방식이 다른 애플리케이션이나 시스템 간의 통신을 위해 통일된 구문 형식으로 변환하는 기능
**주요 역할:**
1. MIME 인코딩
2. 암호화
3. 압축
4. 코드 변환

### **1.4.7 7계층(Application 계층)**
애플리케이션 프로세스를 정의하고 애플리케이션 서비스를 수행합니다.
**대표적인 프로토콜:**
1. FTP
2. SMTP
3. HTTP
4. TELNET
## 1.5 인캡슐레이션과 디캡슐레이션
**인캡슐레이션(Encapsulation)**: 상위 계층에서 하위 계층으로 헤더를 추가하면서 데이터를 전달하는 과정
**디캡슙레이션(Decapsulation)**: 하위 계층에서 상위 계층으로 헤더를 제거하면서 데이터를 전달하는 과정

![Untitled 7 8.png](/img/user/image/Untitled%207%208.png)

**보내는 과정**
1. 애플리케이션에서 만들어진 데이터와 트랜스포트 계층에서 헤더를 더합니다
2. 네트워크 계층에서 다시 헤더를 추가하고 데이터 계층으로 보냅니다.
3. 데이터 계층에선 헤더와 테일을 더한 후에 피지컬 계층으로 보냅니다.
4. 피지컬 계층에선 이를 전기신호로 변환한 후에 데이터를 전송합니다.

**받는 과정**
1. 피지컬 계층에서 받은 전기 신호를 데이터 형태로 변환합니다.
2. 헤더를 확인 후에 MAC 주소가 자기 자신의 주소와 동일하지 않으면 데이터 수신을 멈추고 동일하면 데이터 수신을 진행합니다.

![Untitled 8 7.png](/img/user/image/Untitled%208%207.png)
위처럼 보면 너무 복잡하지만 핵심적인 개념은 헤더에는 **두 가지 정보**는 반드시 포함되어야 한다는 걸 알면 됩니다.
1. **현재 계층에서 정의하는 정보**
		1. 따라서, 전송 계층에선 SEQ Number와 ACK Number를 통해 전달 순서와 도착 순서를 넣었습니다.
2. **상위 프로토콜 지시자**: 인캡슐레이션 과정에선 필요 없지만 디캡슐레이션 과정에서 상위 계층으로 보낼 때 해당 프로토콜이 무엇인지 알지 못하면 디캡슐레이션이 불가능하므로 사용합니다. 예를 들어, 인터넷 계층에서 전송 계층으로 전달할 때 TCP인지 UDP인지 알지 못하면 데이터 해석이 불가합니다.

**MSS & MTU**
MSS(Maximum Segment Size)와 MTU(Maximum Transmission Unit)은 데이터를 쪼갤 때 최대 크기를 지정합니다. MSS는 말 그대로 세그먼트이므로 전송 계층까지의 인캡슐레이션의 크기를 의미하고 MTU는 2계층 이더넷까지 캡슐레이션을 진행한 전체 크기를 의미합니다.