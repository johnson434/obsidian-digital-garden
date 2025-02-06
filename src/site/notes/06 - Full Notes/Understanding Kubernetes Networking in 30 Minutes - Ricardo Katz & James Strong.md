---
{"dg-publish":true,"permalink":"/06-full-notes/understanding-kubernetes-networking-in-30-minutes-ricardo-katz-and-james-strong/","noteIcon":""}
---

# Tags
- [[03 - Tags/Kubernetes Networking\|Kubernetes Networking]] 
---
# 단서 질문
- 리눅스 네트워크 스택이란?
	- 리눅스 네트워킹을 위한 스택으로 4가지가 존재한다.
		1. Ethernet: 이더넷으로 MAC Address 통신이 가능하다.
		2. Routes: 패킷에 대한 도착지를 지정하는 라우팅 역할
		3. iptable: 들어오는 트래픽에 대해서 ruleset을 통해 Allow/Deny 가능하다.
		4. conntrack: 커널 내에 네트워크 트래픽을 추적한다.
- br이란?
	- 리눅스 브릿지로 소프트웨어적 스위치 역할을 한다.
- 서로 다른 네트워크 네임스페이스끼리 통신하기 위한 기능은?
	- veth(가상 이더넷)으로 서로 다른 네트워크 네임스페이스를 연결할 수 있다.
- 클러스터 내에서 서비스명을 통해서 통신이 가능한 이유는?
	- coreDNS로 클러스터 내에서 사용되는 DNS가 있다. API 서버로 요청을 보내면 coreDNS가 요청하는 도메인과 일치하는 IP 주소를 반환한다.
---
# 핵심 요약
- Kubernetes도 단순히 Linux에 불과하다.
- Pod는 내부적으로 Linux Network Stack을 사용하여 동작한다.
- Linux Network Stack엔 ethernet, routes, iptable, conntrack이 있다.
- ethernet은 Mac Address를 통해 통신하는 진입점이다.
- routes는 패킷의 경로를 지정한다.
- iptable/nftables은 들어오는 트래픽에 대해 ruleset을 지정하여 Allow/Deny가 가능하다.
- conntrack은 커널 내에서 들어오는 트래픽을 모두 추적한다.
- Pod 내엔 `pause container`가 존재하며 이를 기반 인프라 컨테이너라 부른다.
- `pause container`는 네트워크 네임스페이스를 가지며, Pod 내 다른 컨테이너가 이를 공유한다.
- Pod와 Host OS는 veth를 통해 연결되며, CNI 플러그인이 네트워크 브리지를 관리한다.
- 서비스는 노드와 Pod의 IP 주소 변경이 잦으므로 만들어진 고정 엔드포인트다.
- 서비스는 프로세스가 아니라 논리적인 엔티티다.
- 서비스가 생성되면 `kube-proxy`가 해당하는 규칙에 맞춰서 Pod의 IP를 iptables/nftables에 등록한다.
- Pod가 죽거나 재생성되어 IP가 변경되면 `kube-proxy`는 이를 반영하여 iptables/nftables을 업데이트한다.
- 때문에 서비스와 매핑되는 Pod로 계속 트래픽이 라우팅되는 것이다.
- 워커 노드엔 `kube-proxy`와 CNI 플러그인이 설정되어 있다.
- `kube-proxy`는 네트워크 설정을 감지하고 이를 반영한다.
- `kubelet`은 CNI 플러그인을 호출하여 Pod의 네트워크 설정을 요청하고, CNI 플러그인이 CIDR을 할당한다.
---
# 핵심 필기
## Kubernetes is just Linux!!
![Pasted image 20250201122323.png](/img/user/image/Pasted%20image%2020250201122323.png)
## Linux Networking
![Pasted image 20250201123831.png](/img/user/image/Pasted%20image%2020250201123831.png)
- 리눅스의 네트워크 스택은 다음 4가지로 이뤄진다.
	1. Ethernet
		- 이더넷 통신(Layer2)을 위해서 사용
	2. Routing
		- 트래픽을 라우팅하는 역할
	3. iptable
		- iptable rule에 따라 트래픽을 Allow/Deny할 수 있다.
	4. conntrack
		- 커널 내에 통신을 추적하는 역할
## Pods
### Pod Networkstack
![Pasted image 20250201125258.png](/img/user/image/Pasted%20image%2020250201125258.png)
- Pod를 실행하면 side car로 Pause Container가 만들어진다.
- Pause Container는 단순히 네트워크 네임스페이스를 가질 뿐, 다른 역할은 하지 않는다.
- Pod 내에 컨테이너가 Pause Container가 만든 네임스페이스를 통해 통신하는 것이다.
- Pod는 자체적으로 Linux Network Stack을 가진다.
- 따라서, 이더넷 또한 가진다.
- Pod가 Host OS와 통신하는 방식은 Docker의 컨테이너가 Host OS와 통신하는 방식과 동일한다.
- Host OS의 루트 네트워크 네임스페이스와 통신하기 위해 veth(가상 이더넷)을 만들고 Pod의 이더넷과 연결한다.
- Docker와 Kubernetes 통신이 다른 점은 veth와 2계층 통신하기 위한 가상 스위치 소프트웨어인 `Linux Bridge`를 Docker에선 `br`라고 표현하고 Kubernres에선 `cbr(custom bridge)`라고 표현하는 정도
### Pod Internal Networking
![Pasted image 20250201155057.png](/img/user/image/Pasted%20image%2020250201155057.png)
- Pod 내에 컨테이너 간에 통신은 Linux Network Stack을 통해 이뤄지는 `localhost` 통신이 된다.
### Pod to Pod Networking
![Pasted image 20250201155957.png](/img/user/image/Pasted%20image%2020250201155957.png)
- Pod와 Pod 간에 통신은 Pod 내부에 컨테이너 간에 통신과 다를 게 없다.
- 동일하게 Pod의 Pause Container가 만든 네트워크 네임스페이스와 연결하기 위한 veth(가상 이더넷)을 Host OS의 네임스페이스인 Root NS에 생성한다.
- 이를 통해 Pod와 Host OS는 통신이 가능해진다.
- 또, 다른 Pod에도 위처럼 동일하게 veth를 통해서 Host OS와 연결한다.
- 이제 서로 다른 두 Pod가 Host OS의 리눅스 네트워크 스택을 통해서 통신이 가능해진다.
- Network Policy 설정을 통해 `cbr`에서 트래픽을 차단 가능하다.
### 클러스터의 전체적인 통신 구조
![Pasted image 20250201161307.png](/img/user/image/Pasted%20image%2020250201161307.png)
## Pod CIDR
- Pod CIDR은 노드 별로 할당 받는다.
	- 예: node-01 10.0.1.0/24
```
k get nodes -o custom-columns=NAME:.metadata.name,POD_CIDR:.spec.podCIDR
# 모든 노드들의 pod cidr이 나온다.
```

```
# 해당 Pod가 속한 네트워크 네임스페이스의 route를 보여준다.
k exec $pod_name -- ip route
```
## Container Network Interface (CNI)
노드 자체가 라우트로 동작한다. `ip route` 커맨드를 입력하면 클러스터의 External IP가 출력된다.
Worker Node의 터미널에 접속한 이후에 `ip route` 커맨드를 입력하면 다른 클러스터 노드의 라우트가 입력된 것을 볼 수 있다.
아래는 Minikube로 클러스터를 구성한 경우라서 minikube로 만들어진 워커노드 컨테이너에 접속하여 `ip route` 커맨드를 실행했다.
![Pasted image 20250201170755.png](/img/user/image/Pasted%20image%2020250201170755.png)
- 맨 처음 `docker ps` 커맨드로 minikube로 실행된 워커 노드를 찾는다.
- `docker exec` 커맨드로 워커노드에서 라우트 정보를 확인한다.
	- 워커 노드들의 pod CIDR과 동일한 주소가 라우트로 입력된 걸 확인할 수 있다.
CNI 설정은 모든 노드 마다 존재한다. `kubelet`은 Pod를 할당하기 전에 CNI 설정으로 IP 대역을 확인하고 Pod를 생성한다.
![Pasted image 20250201171237.png](/img/user/image/Pasted%20image%2020250201171237.png)
![Pasted image 20250201171936.png](/img/user/image/Pasted%20image%2020250201171936.png)
## Service
- 중요한 사실. 서비스는 소프트웨어 같은 게 아니다.
- `Service`라는 프로세스가 돌면서 라우팅해주는 게 아니라 `Service`를 생성하면 해당 규칙에 알맞게 `kube-proxy`에서 iptable이나 nftable 등이 설정된다. 따로 `Service`라는 프로세스가 실행되고 있는 게 아니다.
- 예를 들어서, `Service`를 만들면 해당 `kube-proxy`에서 해당 서비스와 매핑되는 Pod들의 IP를 워커 노드의 iptable이나 nftable에 규칙으로 적용한다.
- `Pod`가 중단되어 IP가 변경된다면 `kube-proxy`가 변경 사항을 알아채고 변경된 IP를 적용한 ruleset을 워커노드에 적용한다. (`kube-proxy`는 모든 노드에 존재하므로 모든 노드에 매핑 `Pod`가 자동으로 수정됨.)
### Services CIDR
- Pod와 Node는 언제든지 죽을 수 있다.
- 클라이언트가 언제든지 죽을 수 있는 Pod의 주소를 외우는 건 말이 안된다.
- 따라서, 단일 엔드포인트를 제공한다.
![Pasted image 20250201172208.png](/img/user/image/Pasted%20image%2020250201172208.png)
- 그림처럼 172.21.2.25 IP로 접속하면 내부에선 리눅스의 기술인 iptable rule을 통해여 10.0.0.11:8080이라는 파드의 주소로 매핑이 된다.
### Services - NodePort
- 서비스를 생성하면 matching 설정한 Pod들이 엔드포인트에 등록된다.
- 이후에 서비스로 들어오는 트래픽은 엔드포인트로 트래픽을 라우팅한다.
- 이 때, `kube-proxy`도 iptable의 rule을 Inject한다. 
**Pod의 IP가 변경된다면?** 
- Pod를 재생성하거나 종료되어 새로 생성된다면 IP가 변경된다.
- `kube-proxy`는 이를 감지하여 iptable의 rule을 변경한다.
- 워커 노드에 접속해서 `nft list ruleset` 명령어를 사용하면 노드의 라우트 설정이 계속 변경되는 걸 볼 수 있다.
``` bash
nft list ruleset
# 아래가 결과물이다.
```
![Pasted image 20250201175542.png](/img/user/image/Pasted%20image%2020250201175542.png)

### Kube Proxy
![Pasted image 20250201182341.png](/img/user/image/Pasted%20image%2020250201182341.png)
- `kube-proxy`는 모든 노드에서 돌아가는 프로세스다.
- 네트워크 설정이 변경되면 해당 rule을 적용하여 nftable이나 iptable에 적용한다.
- 서비스와 Pod를 매핑하는 건 `kube-proxy`의 역할이다.
### DNS
- CoreDNS는 클러스터 상에서 DNS 역할을 한다.
### Network Policy
- 방화벽 규칙(firewall rules)으로 `cbr`에서 네트워크를 차단 가능한 기능.
- 즉, Pod와 Pod 간에 통신을 차단할 수 있다.
### Ingress Controllers and Gateway
- 둘 다 더 복잡한 Ingress 트래픽을 처리하는 데 사용된다.
---
# 참고 자료
https://www.youtube.com/watch?v=Mj04QOqAaJ8
[Kubernetes Networking Deep Dive](https://medium.com/google-cloud/understanding-kubernetes-networking-pods-7117dd28727)


