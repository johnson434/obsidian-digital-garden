---
{"dg-publish":true,"permalink":"/06-full-notes/kubernetes-service/","noteIcon":""}
---

# Tags
- [[03 - Tags/Kubernetes\|Kubernetes]]
---
Pod가 Pod CIDR Block이라는 유동적인 네트워크 대역에서 생성되었다가 삭제되므로 이를 고정적인 진입점은 만들어줄 필요가 있다.
이를 위해 사용되는 것이 Kubernetes Service다.
서비스의 종류로는 3가지가 있다.
1. NodePort
2. ClusterIP
3. Loadbalancer

## NodePort
- 노드의 Port를 통해서 요청을 보낸다.
- Round Robin 방식으로 로드밸런싱한다.
- 노드포트 -> 서비스 포트 -> Pod의 포트로 이동

## ClusterIP
- 클러스터 내부 통신을 위해 사용된다.
- Round Robin 방식으로 로드밸런싱한다.
- 서비스 포트 -> Pod의 포트로 이동
## Loadbalancer
- Nginx, HAProxy 등 다양한 로드밸런서 서버를 지원한다.
- CSP를 사용하면 클러스터 외부에 로드밸런서를 만들지만(예: AWS의 ALB, NLB 등) 아니라면 직접 Pod를 만들어서 로드밸런싱한다.