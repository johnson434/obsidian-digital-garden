---
{"dg-publish":true,"permalink":"/06-full-notes/linux-network-namespace/","noteIcon":""}
---

# Tags
- [[03 - Tags/Linux\|Linux]]
- [[03 - Tags/Network\|Network]]
# 핵심 요약
- 네트워크 네임스페이스는 하나의 호스트에서 격리된 네트워크 공간을 구성
- 모든 네트워크 네임스페이스는 루트(Root) 네임스페이스에 속한다.
- 네임스페이스 마다 각각의 네트워크 인터페이스, 라우팅 테이블, 포워딩 테이블을 가진다.
- 서로 다른 네트워크 네임스페이스끼리 통신하기 위해선 veth가 필요하다.
- Docker와 같은 컨테이너는 네트워크 네임스페이스를 통해 호스트와 네트워크를 분리한다.
# 핵심 필기
## 리눅스 네트워크 네임스페이스란?
- 하나의 호스트에서 여러 개의 격리된 네트워크 공간을 형성하는 기술.
    - 호스트 자체도 하나의 네트워크 네임스페이스를 가진다고 볼 수 있다 → Root Namespace
    - 즉, 일반적으로 호스트의 1번 프로세스(init)에 할당된 Root Namespace를, 자식 프로세스들이 모두 공유하는 구조로 이루어져 있다.
- 각 네트워크 네임스페이스마다 네트워크 인터페이스, 라우팅 테이블, 포워딩 테이블을 가진다.
- 격리된 네임스페이스에 할당된 프로세스는, 다른 네임스페이스에 영향을 줄 수 없다.
- 리눅스는 네트워크 외에도 다양한 네임스페이스를 지원한다. 네임스페이스는 컨테이너(container) 기반의 가상화 기술을 지탱하는 주요 기능.

> [!important]  
> 실습이 필요할 경우, 가상머신을 사용할 것을 권장합니다. 여기서는 Ubuntu 20.04를 사용하였습니다.  
  
> [!important]  
> 아래 실습 자료는 ‘참고자료’ 섹션에서 소개하는 44bits 블로그의 예제를 따릅니다.  

## 리눅스 네트워크 네임스페이스 실습
- 호스트와 격리된 네임스페이스에 Nginx 서버를 띄우고, 접속하는 것을 목표로 한다.
- `ip` 명령어를 많이 사용할 예정… 이 김에 친숙해지자!

```Bash
       Host
       +--------------------------------------------------+
       |                                 +--------------+ |
   [lo]+                             [lo]+              | |
       |                                 |    [Nginx]   | |
 [eth0]+                +-------->[veth1]+              | |
       |                |                |  direct_netns| |
[veth0]+<---------------+                +--------------+ |
       |                                                  |
       |                                   Root Namespace |
       +--------------------------------------------------+
```

### 1. 네트워크 디바이스 확인
- 먼저, 호스트의 네트워크 인터페이스를 확인한다.
```Bash
$ ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 02:18:ce:0d:00:12 brd ff:ff:ff:ff:ff:ff
```

### 2. 가상 인터페이스 만들기

- 호스트와 새로운 네트워크 네임스페이스를 연결하는 가상 네트워크 인터페이스를 만들어야 한다.
- `veth` : 가상 이더넷 인터페이스(Virtual Ethernet Interface)를 의히하며, 항상 쌍으로 만들어진다. `veth` pair로 서로 다른 네임스페이스를 연결한다.

```Bash
# veth0과 veth1을 만든다.
$ sudo ip link add veth0 type veth peer name veth1

# 만들어진 가상 네트워크 인터페이스 확인.
$ ip -br link
lo               UNKNOWN        00:00:00:00:00:00 <LOOPBACK,UP,LOWER_UP>
eth0             UP             02:18:ce:0d:00:12 <BROADCAST,MULTICAST,UP,LOWER_UP>
veth1@veth0      DOWN           be:d4:d8:12:d3:e8 <BROADCAST,MULTICAST,M-DOWN>
veth0@veth1      DOWN           5a:ce:e1:3b:6a:17 <BROADCAST,MULTICAST,M-DOWN>
```

### 3. 새로운 네트워크 네임스페이스 만들기

- `direct_netns`라는 이름의 네트워크 네임스페이스를 만든다.
- `netns` 오브젝트는 네트워크 네임스페이스를 관리한다.
- 해당 네임스페이스는 `/var/run/netns/` 안에 생성된다. 하지만 해당 네임스페이스에서 네트워크 설정을 해야하는 경우, `/etc/netns/<NAME>/` 경로에서 수정을 해야한다.
    - 가령, 네트워크 네임스페이스의 `resolv.conf` 파일을 따로 만들고 싶은 경우, `/etc/netns/<NAME>/resolv.conf` 파일을 생성/수정해야 한다.

```Bash
$ sudo ip netns add direct_netns

# 만들어진 네트워크 네임스페이스 목록 확인하기.
$ ip netns list  # 또는 ls /var/run/netns
direct_netns
```

- `ip netns exec`으로 특정 네트워크 네임스페이스에서 프로세스를 실행시킬 수 있다.
- 하지만 매번 위 명령어 입력하기가 슬슬 귀찮아진다면, `ip netns exec bash`로 해당 네트워크 네임스페이스에 들어가, 호스트에서와 동일하게 커맨드를 입력할 수도 있다. (물론 `exit` 명령어로 언제든지 나올 수 있다.)

```Bash
# direct_netns에서 ip 명령어를 실행하여, 해당 네임스페이스의 네트워크 인터페이스 확인.
$ sudo ip netns exec direct_netns ip -br link
lo               DOWN           00:00:00:00:00:00 <LOOPBACK>
```

### 4. 격리된 네임스페이스에서 Nginx 서버 실행하기

- `direct_netns` 네임스페이스의 루프백 인터페이스를 활성화한다.

```Bash
$ sudo ip netns exec direct_netns ip link set dev lo up
$ sudo ip netns exec direct_netns ip -br link
lo               UNKNOWN        00:00:00:00:00:00 <LOOPBACK,UP,LOWER_UP>
```

- 호스트에 Nginx를 설치하고, 호스트 위에서 동작하지 않도록 설정한다.

```Bash
$ sudo apt updage
$ sudo apt install nginx-core

# nginx 동작 확인
$ curl 127.0.0.1

# 만약 웹페이지 데이터가 불러와진다면, 서비스를 중지한다.
$ sudo systemctl stop nginx
$ sudo systemctl disable nginx
```

- Nginx를 `direct_netns`의 포어그라운드로 실행한다.

```Bash
$ sudo ip netns exec direct_netns nginx -g 'daemon off;'
```

- 터미널 창을 새로 열어서, 호스트와 `direct_netns` 네임스페이스의 연결 상태를 확인한다.
- `direct_netns`의 Nginx 서버는 잘 돌아가는 상태.
- 하지만 아직 두 네임스페이스가 연결되지 않았기에, 호스트에서 `direct_netns`의 네임스페이스로 접근할 수 없다.

```Bash
# direct_netns의 Nginx 서버는 잘 돌아간다.
$ sudo ip netns exec direct_netns curl 127.0.0.1
<!DOCTYPE html>
...

# 하지만 호스트에서 direct_netns의 Nginx 서버로 접근할 수는 없다.
$ curl 127.0.0.1
curl: (7) Failed to connect to 127.0.0.1 port 80: Connection refused
```

```
       Host
       +--------------------------------------------------+
       |                                 +--------------+ |
   [lo]+                             [lo]+              | |
       |                                 |    [Nginx]   | |
 [eth0]+                                 |              | |
       |                                 |  direct_netns| |
[veth0]+<---+                            +--------------+ |
       |    |                                             |
[veth1]+<---+                              Root Namespace |
       +--------------------------------------------------+
```

### 5. 각 네임스페이스를 가상 인터페이스로 연결하기

- 현재 `veth0(@veth1)`과 `veth1(@veth0)` 인터페이스는 모두 호스트 네임스페이스에 있는 상태.
- `veth0`은 호스트 네임스페이스에 남겨놓고, `veth1`을 `direct_netns`에 옮겨놓는다.

```
$ sudo ip link set veth1 netns direct_netns

# 호스트 네임스페이스의 네트워크 디바이스 목록 출력
$ ip -br link
lo               UNKNOWN        00:00:00:00:00:00 <LOOPBACK,UP,LOWER_UP>
eth0             UP             02:18:ce:0d:00:12 <BROADCAST,MULTICAST,UP,LOWER_UP>
veth0@if3        DOWN           5a:ce:e1:3b:6a:17 <BROADCAST,MULTICAST>

# direct_netns 네임스페이스의 네트워크 디바이스 목록 출력
$ sudo ip netns exec direct_netns ip -br link
lo               UNKNOWN        00:00:00:00:00:00 <LOOPBACK,UP,LOWER_UP>
veth1@if4        DOWN           be:d4:d8:12:d3:e8 <BROADCAST,MULTICAST>
```

- 가상 인터페이스를 활성화하기 위해선, 먼저 IP를 할당해야 한다.

```Bash
# 호스트의 veth0 인터페이스에 IP 할당
$ sudo ip a add 10.200.0.2/24 dev veth0

# direct_netns의 veth1 인터페이스에 IP 할당
$ sudo ip netns exec direct_netns ip a add 10.200.0.3/24 dev veth1
```

- `ping`으로 각 인터페이스의 활성 상태를 확인해본다.

```Bash
$ ping 10.200.0.3
PING 10.200.0.3 (10.200.0.3) 56(84) bytes of data.
64 bytes from 10.200.0.3: icmp_seq=1 ttl=64 time=0.043 ms
64 bytes from 10.200.0.3: icmp_seq=2 ttl=64 time=0.039 ms
^C
--- 10.200.0.3 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1029ms
rtt min/avg/max/mdev = 0.039/0.041/0.043/0.002 ms

$ sudo ip netns exec direct_netns ping 10.200.0.2
PING 10.200.0.2 (10.200.0.2) 56(84) bytes of data.
64 bytes from 10.200.0.2: icmp_seq=1 ttl=64 time=0.023 ms
64 bytes from 10.200.0.2: icmp_seq=2 ttl=64 time=0.035 ms
^C
--- 10.200.0.2 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1029ms
rtt min/avg/max/mdev = 0.023/0.029/0.035/0.006 ms
```

- 마지막으로, `direct_netns`에서 실행되는 Nginx를, `veth` 인터페이스로 호스트에서 접근할 수 있는지 확인해본다.

```Bash
$ curl 10.200.0.3
<!DOCTYPE html>
...
```

```Bash
       Host
       +--------------------------------------------------+
       |                                 +--------------+ |
   [lo]+                             [lo]+              | |
       |                                 |    [Nginx]   | |
 [eth0]+                +-------->[veth1]+              | |
       |                |      10.200.0.3|  direct_netns| |
[veth0]+<---------------+                +--------------+ |
10.200.0.2                                                |
       |                                   Root Namespace |
       +--------------------------------------------------+
```
---
# 참고자료
> [!info] ip로 직접 만들어보는 네트워크 네임스페이스와 브리지 네트워크 - 컨테이너 네트워크 기초 2편  
> 컨테이너 네트워크 기초 1편에서는 리눅스 네임스페이스 중 하나인 UTS 네임스페이스에 대해서 소개 했습니다.  
> [https://www.44bits.io/ko/post/container-network-2-ip-command-and-network-namespace](https://www.44bits.io/ko/post/container-network-2-ip-command-and-network-namespace)  

> [!info] veth: 리눅스 가상 이더넷 인터페이스란?  
> veth는 리눅스의 버추얼 이더넷 인터페이스를 의미합니다.  
> [https://www.44bits.io/ko/keyword/veth](https://www.44bits.io/ko/keyword/veth)  

> [!info] Introduction to Linux Network Namespaces  
> An introduction to Linux network namespaces.  
> [https://www.youtube.com/watch?v=_WgUwUf1d34](https://www.youtube.com/watch?v=_WgUwUf1d34)  

> [!info] ip-netns(8) - Linux manual page  
>  
> [https://www.man7.org/linux/man-pages/man8/ip-netns.8.html](https://www.man7.org/linux/man-pages/man8/ip-netns.8.html)