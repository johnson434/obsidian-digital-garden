---
{"dg-publish":true,"permalink":"/06-full-notes/kubernetes-external-traffic-policy/","noteIcon":""}
---


[[03 - Tags/Kubernetes Service\|Kubernetes Service]]
## External Traffic Policy란?
- External Traffic Policy란 말 그대로 외부에서 들어오는 트래픽에 대한 정책
- 외부에서 노드로 트래픽이 들어오면 트래픽을 어떻게 처리할지 정한다.
### External Traffic Policy가 cluster면
- external traffic policy를 cluster로 설정하면 IpTable의 클러스터 노드들의 주소가 업데이트된다.
- 따라서, 해당하는 Pod를 가진 모든 클러스터 중에 하나로 라우팅된다.
- 서비스가 요청을 공평하게 분배하는 역할을 하므로 어떻게 보면 당연한 일
### External Traffic Policy가 local이면
- IpTable의 해당하는 노드에 속한 Pod의 IP 주소만 업데이트된다.
- 따라서, 로컬 통신을 통해서만 트래픽을 처리한다.
- 만약, 로컬호스트에 해당 Pod가 존재하지 않는다면 대상이 없어 Connection Timeout이 난다.
## EKS VPC CNI의 경우
- ENI를 통해서 Pod가 존재하는 노드로 바로 라우팅된다.
- ![Pasted image 20250124161737.png](/img/user/image/Pasted%20image%2020250124161737.png)
- 위 그림처럼 EKS VPC CNI Add On을 키게 되면 Pod와 연결되어 있는 ENI로 트래픽이 바로 전송된다.
- 따라서, 노드포트를 통한 오버레이 네트워크가 발생하지 않으며 ExternalTrafficPolicy로 들어온 요청이 해당 노드의 Pod가 없어서 클러스터로 재전송 될 일이 없다.