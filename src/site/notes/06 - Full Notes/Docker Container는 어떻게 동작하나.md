---
{"dg-publish":true,"permalink":"/06-full-notes/docker-container/","dgPassFrontmatter":true,"noteIcon":""}
---

[[03 - Tags/Docker\|Docker]] 
**컨테이너**는 소프트웨어를 실행하기 위한 **가볍고(lightweight), 독립적(standalone)이고, 실행가능한(executable) 패키지**이다.

Docker는 이러한 컨테이너 기술을 사용하는 도구 중에서 가장 유명하다.

Docker는 소프트웨어의 이름이기도 하지만, 동시에 Docker 소프트웨어를 제작한 회사의 이름(Docker Inc)이기도 하다.

Docker를 이해하기 위해선 먼저 두 가지 주제 선적 컨테이너(말 그대로 무역에서 컨테이너를 배타고 보내는 것)와 가상 머신을 알아야 한다.

## 선적 컨테이너의 간단한 역사

마크 레빈슨이 작성한 책 ‘THE BOX 더 박스 컨테이너는 어떻게 세계 경제를 바꾸었는가’에선 선적 컨테이너가 세계무역과 세계 경제에 끼친 영향에 대해서 다룬다.

언뜻 보기에 선박의 컨테이너와 Docker의 컨테이너는 관련이 없어보이지만, 생각보다 이 둘은 많은 공통점을 가진다.

![Untitled 4.png](/img/user/image/Untitled%204.png)

컨테이너가 없었을 시절에 선박을 적재하는 과정은 노동력과 시간 소모가 과다했습니다. 이는 세계 무역을 굉장히 비효율적으로 만들었습니다.

위처럼 컨테이너가 아닌 박스, 포대자루, 조각상 그대로 옮긴다고 생각해보세요. 비효율의 극치일 것입니다.

따라서, 컨테이너를 통한 적재의 표준화를 통해 운송은 간단해졌습니다. Docker 또한 이러한 편의성이 중요 포인트입니다.

  

## 가상 머신이 뭐야?

가상 머신(VMs : Virtual Machines)는 가상화(virtualisation)라는 프로세스를 통해 만들어집니다.

**가상화** 기술을 사용하면 여러 개의 가상 환경이나, OS, 서버, 저장공간, 네트워크 등을 **한 개의 장치에서** 만들 수 있습니다.

가상환경은 마치 실제 물리 장치(하드웨어)와 독립된 것처럼 동작합니다.

![Untitled 1 3.png](/img/user/image/Untitled%201%203.png)

언뜻 보기에 가상 머신은 여러 개의 하드웨어에서 동작하는 것처럼 보이지만, 실제로는 하나의 물리 장치에서 동작합니다.
## 가상 머신은 어떻게 동작하나?

![Untitled 2 2.png](/img/user/image/Untitled%202%202.png)

가상 머신은 그림과 같이 동작합니다.
**Host Hardware & Operating System(OS)** : 우리가 VM을 돌리는 실제 기기와 실제 운영 체제를 말합니다. 예를 들어, 삼성 노트북에 Linux Os를 설치 후에 Virtual Box를 돌린다면, 이 삼성 노트북이 실제 물리 기기(Host Hardware)가 될 것이고 리눅스가 OS가 됩니다.
**Hypervisor(하이퍼바이저)** : 여러 개의 가상 기기가 각자의 OS를 가지고 실행하는데 사용되는 소프트웨어.

그러나, 가상 머신은 컨테이너와 달리 두 가지 큰 문제점을 가집니다.
1. 가상 머신은 더 많은 자원을 사용한다. : 가상머신은 OS의 전체를 필요로 합니다.
2. 이식성(Portability) : 기반이 된 환경에 따라 이식이 힘들어질 수 있다. 다른 하이퍼바이저나 클라우드로 이식하기 힘듭니다.
## 컨테이너는 뭔데?
컨테이너는 프트웨어를 실행하기 위한 **가볍고(lightweight), 독립적(standalone)이고, 실행가능한(executable) 패키지**이다.
코드, 런타임, 시스템툴, 라이브러리 등이 이에 포함된다.
컨테이너는 **서로 다른 환경에서도 동작할 수 있도록** 만든 기술입니다. 따라서, 컨테이너는 환경이나 의존성과 독립되어 있습니다. 따라서, 클라우드가 됐든, 실제 기기가 됐든 컨테이너를 통해 실행되는 작업은 모두 동일하게 동작합니다.

가상머신이 하드웨어를 가상화한다면, **컨테이너는 OS를 가상화**합니다.
이는 간단히 말해서 컨테이너 기술은 하나의 OS를 사용하고, 공유 OS(호스트 OS) 위에서 동작합니다.

![Untitled 3 2.png](/img/user/image/Untitled%203%202.png)

## 내가 위에 내용 이해 안가서 쓴 글
컨테이너는 공유 OS(Shred OS)를 사용한다는 부분이 이해가 안갔는데 여기 내가 이해한 내용을 써보자면 컨테이너와 가상머신의 차이의 가장 중요점은 **하드웨어**와 **커널**까지 가상화하냐의 차이점같다.
가상 머신의 경우, 자신만의 커널을 가지고 있으며 호스트 OS와의 통신을 통해 커널의 동작을 실행한다.

![Untitled 4 2.png](/img/user/image/Untitled%204%202.png)
가상 머신은 위처럼 가상 머신의 OS가 자신의 커널을 가지며, 가상 머신의 커널과 Host OS의 커널 간 네트워킹을 통해 동작한다. 예를 들어, 윈도우에서 Virtual Box를 통해 리눅스를 실행한다고 쳐보자. Host OS인 윈도우의 커널과 Virtual Box를 통해 실행된 리눅스의 커널. 총 2개의 커널이 동작하고 이 둘 간에 네트워킹이 이루어진다.

  

![Untitled 5.png](/img/user/image/Untitled%205.png)
반면에, Container는 위와 같다. 컨테이너를 통해 만들어진 OS는 자신만의 커널을 가지지 않으며, 호스트 OS의 커널을 사용한다.
예를 들어, Docker를 통해 리눅스를 실행한다고 쳐보자. Host OS인 윈도우 커널만 존재하며, 컨테이너로 실행된 리눅스는 커널이 존재하지 않는다.

위와 별개로 **Kata Container(카타 컨테이너)**라는 오픈 소스도 있는데 여기선 컨테이너도 자신만의 커널을 가진다.

공유 커널을 사용한다는 점이 보안적으로 문제가 된다는 점에서 시작된 프로젝트 같다.

![Untitled 6.png](/img/user/image/Untitled%206.png)
## 컨테이너의 장점
1. **이식성** : 플랫폼과 독립적이므로 어떤 시스템에서도 동작할 수 있다.
2. **효율성** : 단, 하나의 OS. 호스트 OS만 사용하므로 가상 머신에 자원을 덜 쓴다.
3. **일관성(Consistency)** : 컨테이너는 실행을 위해 필요한 모든 환경(code, runtime, library)를 포함하므로 어디서든 일관성 있게 동작한다.(내 맥북에선 돌아가는데 << 이런 소리 불가능)
4. **독립성(Isolation)** : 가볍고 독립적인 환경으로 프로그램을 돌린다. Container끼리 서로 소통하지 않는다.
5. **빠른 배포속도(Fast Deployment)** : 배포 속도 빠르다.
## Docker는 뭔데?
가상 머신과 컨테이너에 대해 공부를 완료했으므로 이제 Docker에 대해 다룰 차례다.
Docker는 컨테이너를 만들고, 관리할 수 있는 툴이다.
Docker를 이해하기 위해선 2가지 핵심 주제를 알면 도움이 된다. 바로 Dockerfile과 Docker Image다.
Dockerfile은 Docker Image를 만들기 위한 절차 과정을 모아 놓았다.
Docker Image는 Docker 컨테이너를 만들기 위해 사용되는 템플릿이다. Docker Image는 코드, 런타임, tool, 라이브러리, 세팅 등의 요소가 포함된다.

![Untitled 7.png](/img/user/image/Untitled%207.png)

## 그래서 선적 컨테이너가 나왔던 이유가 뭔데?
선적 컨테이너의 표준화를 통해 작업을 효율화했다. 컨테이너 기술도 이와 동일하다.
선적에선 컨테이너의 규격에 맞춰서 컨테이너를 만든다.
Docker에선 Dockerfile의 규격에 맞춰서 컨테이너를 만든다.
둘 다 규격에 맞춰서 만들어졌기 때문에 이식이 쉽고 효율성이 높다.

# 참고 자료
> [!info] How Docker Containers Work – Explained for Beginners  
> A container is a lightweight, standalone, and executable software package that includes everything needed to run a piece of software.  
> [https://www.freecodecamp.org/news/how-docker-containers-work/](https://www.freecodecamp.org/news/how-docker-containers-work/)