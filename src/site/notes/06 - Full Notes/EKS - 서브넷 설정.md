---
{"dg-publish":true,"permalink":"/06-full-notes/eks/","noteIcon":""}
---


# Tags
- [[03 - Tags/EKS\|EKS]]

EKS 클러스터를 구성할 때, 서브넷을 설정하는 부분이 나온다. 
서브넷을 설정하는 이유는 아래와 같다.
> [!subnet_ids란?]
> (Required) List of subnet IDs. Must be in at least two different availability zones. Amazon EKS creates cross-account elastic network interfaces in these subnets to allow communication between your worker nodes and the Kubernetes control plane.

대충 해석해보자면 `최소 2개의 서브넷을 서로 다른 AZ에 만든다. 해당 서브넷에 cross-account ENI를 생성하여 쿠버네티스의 control plane와 사용자의 워커노드가 통신을 가능하도록 만든다.`는 내용이다. 간단히 말해서 `control plane`과 `data plane`이 통신이 가능하게 만들어준다는 뜻이다. 그렇다면 EKS는 해당 서브넷을 어떻게 활용하여 통신이 가능하게 만들어줄까?

  EKS는 `control plane`의 서브넷을 AWS에서 직접 관리한다. 따라서, 사용자는 해당 서브넷을 참조할 수 없다. 그러므로, `control plane`의 서브넷과 직접적인 통신이 불가능하다. (`control plane`이 어떤 서브넷을 쓰는지 모르니까)
`control plane`과 `data plane`이 통신을 하지 못한다면 Pod 스케쥴링부터 kube-api 서버와 통신 등 아무런 동작을 할 수 없다. 애초에 클러스터에 `join`도 할 수 없다.
따라서, Control Plane과 Data Plane의 통신을 위한 연결점으로 ENI를 사용자의 서브넷에 생성하는 것이다. 
이를 통해서 `AWS Managed VPC` <-> `eks subnet` <-> `Data Plane의 서브넷`으로 통신이 가능한 것이다.
![Pasted image 20250130155051.png](/img/user/image/Pasted%20image%2020250130155051.png)


# 출처
[terraform eks cluster](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/eks_cluster#subnet_ids-1)
