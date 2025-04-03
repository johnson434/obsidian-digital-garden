---
{"dg-publish":true,"permalink":"/06-full-notes/docker-vs-containerd/","noteIcon":""}
---

# Tags
- [[03 - Tags/Docker\|Docker]]
- [[03 - Tags/Container\|Container]]
---
Docker는 쿠버네티스가 등장하기 이전에 컨테이너 툴 중에서 유명했다.
쿠버네티스는 OCI(Open Container Interface: 이미지, 런타임) 표준을 따르면 컨테이너 런타임을 CRI(Container Runtime Interface)를 통해서 실행했다.
그런데, Docker는 OCI를 고려하고 만들지 않았다. 따라서, Docker를 CRI와 동작하기 위해선 Docker와 CRI 사이에 dockershim이 필요했다.
![Pasted image 20250310200824.png](/img/user/image/Pasted%20image%2020250310200824.png)

docker를 계속 지원하는 건 코드 보안 이슈도 있으며 유지 보수 노력이 많이 들어간다. 거기에 더해서 Docker는 쿠버네티스 내에서 동작하는 것을 고려하고 만든 솔루션이 아니다. 따라서, 쿠버네티스에선 v1.24부터 docker 지원을 중단했다.

![Pasted image 20250310200953.png](/img/user/image/Pasted%20image%2020250310200953.png)
위에서 보았듯. 쿠버네티스는 워커노드에서 컨테이너를 실행하기 위해서 CRI 표준을 따르는 컨테이너 기술만 있으면 된다. 따라서, containerd만 있으면 충분하다.

![Pasted image 20250310201418.png](/img/user/image/Pasted%20image%2020250310201418.png)

![Pasted image 20250310201933.png](/img/user/image/Pasted%20image%2020250310201933.png)