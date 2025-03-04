---
{"dg-publish":true,"permalink":"/06-full-notes/linux-various-kernels/","noteIcon":""}
---

# Tags
- [[03 - Tags/Linux\|Linux]]
# 단서 질문
- Micro Kernel의 이점은?
	- 서버의 문제가 생기더라도 커널의 크게 영향을 끼치지 않는다.
	- 실제론 아무리 커널 프로세스와 파일 서버 프로세스가 다른 프로세스이더라도 서로 의존하는 관계일 수 있다. 이러면 영향을 끼칠 수밖에 없다.
- Micro Kernel의 단점은?
	- 커널 프로세스와 File Server 같은 기존에 모놀리식 아키텍처에선 동일한 프로세스에 존재하던 기능이 서로 다른 프로세스로 분리되었으므로 IPC를 통해 커널 프로세스와 File Server가 통신해야 한다.
- Monolithic Kernel의 이점은?
	- File Server, Network Server 등이 동일한 커널 내에 존재하므로 IPC가 필요 없음. 성능상 Micro Kernel보다 빠름.
- Monolithic Kernel의 단점은?
	- 모든 서비스가 커널이라는 단일 프로세스에 속하므로 서비스에 문제가 생기면 커널을 포함한 전체 프로세스에 영향을 끼친다.
# 핵심 요약
- 리눅스의 아키텍처는 크게 2가지가 존재한다.
- Monolithic Kernel Architecture와 Micro Kernel Architecture
- 모놀리식 아키텍처는 파일 서버와 네트워크 서버 등이 모두 커널 프로세스에 존재한다.
- 따라서, 서버에 문제가 생기면 커널에 영향을 끼칠 수 있다.
- Micro Kernel의 경우에 IPC, 스케쥴러, 메모리 관리와 같은 핵심 기능만 커널 프로세스에 존재하며 나머지 기능은 유저 프로세스로 분리했다.
- 파일 서버에 문제가 생기더라도 커널과 분리되어 있으므로 전체 장애를 끼치지 않는다.
- 단, 파일 서버와 같은 서비스들이 커널과 분리되어 있으므로 IPC 통신을 통해 서로 네트워킹해야 한다.
# 핵심 필기
## Typical OS Architecture
![Pasted image 20250213111949.png](/img/user/image/Pasted%20image%2020250213111949.png)
- 커널에선 `System Call Interface`를 제공한다.
- 일반적인 API와 다르게 시스템 호출 라이브러리는 유저 모드와 커널 모드 전환이라는 경계에 있다. (유저 모드인 프로세스가 시스템 호출 라이브러리를 통해서 커널 모드로 전환이 가능하다.)
- 논리적으로 core 커널 코드와 디바이스 드라이버 코드는 분리
	- 디바이스 드라이버는 특정 하드웨어에 접근하기 위함.
	- 디바이스 드라이버에 비해 커널은 더 일반적이다.
- 코어 커널은 여러 서브시스템으로 나뉜다.
	- File 서비스, 네트워크 서비스, 프로세스 관리 등
## 모놀리식 커널
![Pasted image 20250213113051.png](/img/user/image/Pasted%20image%2020250213113051.png)
- 모놀리식 커널은 서로 다른 서브시스템 간에 호출이 자유롭다..
- 호출에 제한은 없지만 모놀리식 커널은 서브시스템 간에 논리적 분리를 강제한다.
## Micro kernel
- 마이크로 커널 아키텍처에선 **IPC나 스케쥴러, 메모리 관리 등 핵심 기능만 커널에 존재**한다. (최소한의 기능만 가진 커널이므로 micro kernel이라 부름)
- 나머지 서버들(파일 서버, 네트워크 서버)은 유저 모드에서 커널과 별개로 동작한다. 
- 모든 서버와 기능을 커널에 넣은 모놀리식과 다르게 모듈화가 된 상태
![Pasted image 20250213113711.png](/img/user/image/Pasted%20image%2020250213113711.png)
- 마이크로 커널은 단순하게 서로 다른 프로세스 간에 메시지 전송을 위한 코드정도만 작성되어 있다.
- 마이크로 커널 아키르지 텍처의 장점은 하나에 서비스에 문제가 생겨도 다른 서비스에 영향을 끼치지 않는다.
	- 실제론 해당 서비스에 의존하는 서비스에  영향을 끼칠 수 있다. (ex. 파일 서버에 문제 => 파일 디스크립터를 사용하는 모든 어플리케이션에 영향)
- 모놀리식 아키텍처에선 커널 내부에서 서버와 통신을 하면 됐지만, 마이크로 커널 아키텍처에선 커널과 서비스들이 서로 다른 프로세스에서 동작하므로 IPC 통신을 한다. 따라서, 오버헤드가 발생하여 퍼포먼스가 모놀리식에 비해 떨어진다. 예를 들어, 파일 서버를 이용하기 위해선 모놀리식 아키텍처에선 커널 내부에서 호출로 처리가 가능하지만 마이크로 커널 아키텍처에선 파일 서버가 프로세스로 유저 영역에서 돌고 있으므로 해당 프로세스와 IPC를 통해세 네트워킹이 필요하다.
> [!커널의 서비스]
> 커널에서 서비스란 파일 서버, 네트워크 서버 등 커널의 동작을 위해 제공되는 서비스를 의미한다.
## Micro-kernels vs monolithic kernels
마이크로 커널이 모듈식 디자인 때문에 모놀리식보다 우월하다고 주장하는 사람도 있다. 그러나, 모놀리식 커널도 모듈을 지원하는 방법이 있다.
- Components can enabled or disabled at compile time
- Support of loadable kernel modules (at runtime)
- Organize the kernel in logical, independent subsystems
- Strict interfaces but with low performance overhead: macros, inline functions, function pointers
## Hybrid kernel?
윈도우에선 하이브리드 커널을 사용하는데 모놀리식 커널과 크게 다르지 않다. "모놀리식 서비스들이 모놀리식 아키텍처처럼 커널 모드에서 동작하는데 이게 모놀리식 아키텍처랑 뭐가 다르냐?"라고 생각하는 사람들이 있다.
리눅스 토발즈는 "하이브리드 커널"을 마케팅 용어라고 생각한다.
# References
https://linux-kernel-labs.github.io/refs/heads/master/so2/lec1-intro.html#typical-operating-system-architecture
https://brewagebear.github.io/linux-kernel-internal-2/