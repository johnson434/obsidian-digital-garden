---
{"dg-publish":true,"permalink":"/06-full-notes/linux-processes/","noteIcon":""}
---

# Tags
- [[03 - Tags/Linux\|Linux]]
- [[03 - Tags/Linux Process\|Linux Process]]
---
# 핵심 요약
- 프로세스는 프로그램이 실행되면 만들어진다. 주소 공간, 세마포어 등 해당 프로그램이 사용할 리소스를 OS 차원에서 추상화한 것이 프로세스다.
- struct ns_proxy는 프로세스가 만들어질 때마다 새로운 네임스페이스와 해당 프로세스를 연결하여 독립적으로 네임스페이스를 가지도록 한다.
- 이를 통해 docker와 같은 컨테이너들이 다른 컨테이너의 PID를 확인할 수 없다.
---
# 핵심 필기
## 주제
- 프로세스와 스레드
- 문맥 전환
- blocking and waking up
- 프로세스 문맥
## Processes and threads
프로세스는 여러 리소스를 그룹화하는 운영체제 추상화다.(OS abstraction)
- address space
- multiple threads
- Opened files
- Sockets
- Semaphores(세마포어)
- Shared memory regions
- Timers
- Signal handlers
- 이외에도 여러 리소스와 상태 정보

프로세스의 정보는 Process Control Block에 저장된다. Linux에선 `struct task_struct`다.

## 프로세스 리소스 개요
/proc/\<pid\>를 통해서 해당 프로세스가 가진 리소스 요약을 볼 수 있다.
``` bash
$ head -5 /proc/self/status
Name:   head
Umask:  0002
State:  R (running)
Tgid:   446525
Ngid:   0
```
### struct task_struct
스킵 너무 딥해보임.
## [Threads](https://linux-kernel-labs.github.io/refs/heads/master/so2/lec3-processes.html#threads)
스레드는 커널의 프로세스 스케쥴러가 어플리케이션이 CPU를 실행하도록 허용하는 기본 단위다.
- 스레드는 각자 스택을 가지며. 레지스터 값들을 통해서 스레드 실행 상태를 정한다.
- 스레드는 process 문맥에서 동작하며 해당 프로세스의 자원을 공유한다.
- 커널은 프로세스를 스케쥴링하는 것이 아닌 스레드를 스케쥴링한다.
- 유저 레벨 스레드(fibers, coroutines, etc.)는 커널 레벨에서 보이지 않는다.[^1]
## 시스템 호출 복사 (The clone system call)
새로운 프로세스나 스레드는 `clone()`이라는 시스템 호출을 통해서 만들어진다.
`fork()`랑 `pthread_create()`는 내부적으로 `clone()`을 사용한다.
`clone()` 시스템 호출은 호출자가 부모로부터 자원을 공유 받거나 복사하거나 격리할 수 있다.
- CLONE_FILES - shares the file descriptor table with the parent
- CLONE_VM - shares the address space with the parent
- CLONE_FS - shares the filesystem information (root directory, current directory) with the parent
- CLONE_NEWNS - does not share the mount namespace with the parent
- CLONE_NEWIPC - does not share the IPC namespace (System V IPC objects, POSIX message queues) with the parent
- CLONE_NEWNET - does not share the networking namespaces (network interfaces, routing table) with the parent
## [네임스페이스와 컨테이너](https://linux-kernel-labs.github.io/refs/heads/master/so2/lec3-processes.html#namespaces-and-containers)
`컨테이너`는 `경량형 가상 머신`으로 호스트의 커널을 공유한다. 반면에 하이퍼바이저로 실행되는 가상 머신은 자체 커널을 가진다.
컨테이너 기술의 예로는 `LXC`가 있다. `LXC`는 경량형 "VM"과 docker 실행을 가능하게 한다. 

컨테이너는 커널의 몇몇 기능을 통해서 동작할 수 있다. 그 중 하나가 네임스페이스다.
네임스페이스는 서로 다른 네임스페이스를 사용하면 서로 식별이 불가능하다.(격리) 네임스페이스를 사용하지 않으면 전역으로 식별이 가능하다.(globally visible)
예를 들어, 컨테이너가 없다면 모든 프로세스는 `/proc`에서 확인이 가능하다.
반면에, 컨테이너를 사용하면 하나의 컨테이너에 존재하는 프로세스들은 다른 컨테이너에서 보이지 않는다(/proc에 안보인다).
이건 docker 컨테이너 내부에서 `ps aux` 명령어를 통해 간단히 확인이 가능하다.

컨테이너 내부에서 `ps aux` 명령어---를 치면 다른 container의 pid가 확인이 불가능하다.
``` bash
john@john:Playground$ docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED              STATUS              PORTS            NAMES
cf95ec497666   my_ubuntu   "/bin/sh ./entry_poi…"   2 seconds ago        Up 1 second         22/tcp, 80/tcp   ubuntu2
1382a359d29d   my_ubuntu   "/bin/sh ./entry_poi…"   About a minute ago   Up About a minute   22/tcp, 80/tcp   ubuntu
john@john:Playground$ docker exec -it ubuntu sh

# ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.1  0.0   2800  1792 ?        Ss   02:28   0:00 /bin/sh ./entry_point.sh
root           8  0.0  0.0  12020  2824 ?        Ss   02:28   0:00 sshd: sshd [listener] 0 of 10-100 startups
root           9  0.0  0.0   2696  1280 ?        S    02:28   0:00 sleep infinity
root          19  4.2  0.0   2800  1664 pts/0    Ss   02:29   0:00 sh
root          26  0.0  0.0   7888  4096 pts/0    R+   02:29   0:00 ps aux
```

반면에, 호스트 OS에선 컨테이너의 Pid를 확인할 수 있다.
``` bash
john@john:Playground$ docker inspect -f "{{.State.Pid}}" ubuntu
85029
john@john:Playground$ docker inspect -f "{{.State.Pid}}" ubuntu2
87119
john@john:Playground$ ps aux | grep 85029
root       85029  0.0  0.0   2800  1792 ?        Ss   11:28   0:00 /bin/sh ./entry_point.sh
john       97523  0.0  0.0  10524  2816 pts/1    S+   11:38   0:00 grep --color=auto 85029
john@john:Playground$ ps aux | grep 87119
root       87119  0.0  0.0   2800  1664 ?        Ss   11:29   0:00 /bin/sh ./entry_point.sh
john       97525  0.0  0.0  10524  2816 pts/1    S+   11:38   0:00 grep --color=auto 87119
```

실제로 해당 프로세스를 Host에서 `kill`하면 컨테이너가 죽는다.
``` bash
$ sudo kill -9 87119
$ ps aux | grep 87119
john      102164  0.0  0.0  10524  2816 pts/1    S+   11:42   0:00 grep --color=auto 87119

$ docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS          PORTS            NAMES
1382a359d29d   my_ubuntu   "/bin/sh ./entry_poi…"   13 minutes ago   Up 13 minutes   22/tcp, 80/tcp   ubuntu

$ docker ps --all
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS                        PORTS            NAMES
---cf95ec497666   my_ubuntu   "/bin/sh ./entry_poi…"   12 minutes ago   Exited (137) 12 seconds ago                    ubuntu2
1382a359d29d   my_ubuntu   "/bin/sh ./entry_poi…"   13 minutes ago   Up 13 minutes                 22/tcp, 80/tcp   ubuntu
```

## struct nsproxy
격리를 구현하기 위해서 `struct nsproxy` 구조체를 사용합니다.  
`struct nsproxy`는 IPC, 네트워킹, cgroup, 마운트, PID, Time 네임스페이스를 지원합니다. 
예를 들어, 각 프로세스는 루트 네트워크 네임스페이스를 참조하는 것이 아닌 해당 프로세스만을 위한 `struct net` 구조체를 참조합니다.
시스템이 초기화될 때, 모든 프로세스는 기본 네임스페이스인 `init_net`을 공유합니다. 
그 후 새로운 네임스페이스가 생성되면, 새로운 프로세스들은 기본 네임스페이스 대신 새로운 네임스페이스를 가리킬 수 있습니다.
`struct nsproxy`는 이러한 다양한 네임스페이스를 관리하는 역할을 합니다.(그니까 얘가 모든 네임스페이스 정보를 가지고 있다가 프로세스가 만들어지면 새로운 struct net 즉, 네임스페이스를 만들고 프로세스가 해당 네트워크 네임스페이스를 가리킴으로써 프로세스 간에 격리가 가능하다는 느낌인듯)
## Accessing the current process[¶](https://linux-kernel-labs.github.io/refs/heads/master/so2/lec3-processes.html#accessing-the-current-process "Permalink to this headline")
스킵
## [Context switching](https://linux-kernel-labs.github.io/refs/heads/master/so2/lec3-processes.html#context-switching)
![Pasted image 20250226142749.png](/img/user/image/Pasted%20image%2020250226142749.png)
## Preempting tasks[¶](https://linux-kernel-labs.github.io/refs/heads/master/so2/lec3-processes.html#preempting-tasks "Permalink to this headline")
스킵

---
# 참고 자료
- https://linux-kernel-labs.github.io/refs/heads/master/so2/lec3-processes.html
- https://medium.com/@boutnaru/the-linux-kernel-data-structure-journey-struct-nsproxy-b032c71715c5

[^1]: 코루틴을 이전에 잠깐 공부했는데 `경량 스레드`라는 개념이다. 여러 개의 스레드를 만들지 않고, 한정되는 스레드를 동시에 여러 실행 흐름이 사용한다. 즉, 실행 흐름마다 스레드를 생성하지 않는다. 따라서, 코루틴을 통해 만들어진 흐름은 커널 레벨에서 컨트롤이 불가능하단 말이다. 왜냐면 해당 흐름은 실제 스레드가 아니니까.