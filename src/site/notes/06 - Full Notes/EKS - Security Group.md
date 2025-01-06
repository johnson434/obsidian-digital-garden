---
{"dg-publish":true,"permalink":"/06-full-notes/eks-security-group/","dgPassFrontmatter":true}
---

[[03 - Tags/EKS\|EKS]] 
# 핵심 요약
- EKS를 만들면 Cluster Security Group이 만들어집니다.
- Additional Security Group은 명시하는 경우에만 만들어집니다.
- Cluster Security Group은 Control Plane과 클러스터의 컴퓨팅 리소스(Data Plane, Fargate 등)가 통신하기 위해 사용됩니다.
- Additional Security Group은 Control Plane과 계정에 존재하는 컴퓨팅 리소스와 통신하기 위해 사용됩니다. (ex. EKS의 Node Group이 아닌 EC2, RDS, 기타 등등 모든 컴퓨팅 리소스)
- Additional Security Group은 또한, Control Plane과 온프로미스의 노드가 통신하기 위해서도 사용됩니다.

# 핵심 필기
## Cluster Security Group
- Control Plane과 클러스터의 컴퓨팅 리소스 간의 통신을 위해 사용된다.
![Pasted image 20241130233702.png](/img/user/image/Pasted%20image%2020241130233702.png)
## Additional Cluster Security Group
- Control Plane과 계정에 존재하는 컴퓨팅 리소스와 통신하는 데 사용됩니다. 
- EKS 엔드포인트 접근을 제한이 주 목적
![Pasted image 20241130233755.png](/img/user/image/Pasted%20image%2020241130233755.png)

## Security Group 설정
- cluster security group과 worker node security group이 **chain**이어야 합니다. 
- Control Plane의 API서버와 worker node간 통신이 필요하기 때문입니다.
- Managed node group으로 생성된 worker node는 기본적으로 Cluster security group을 사용합니다.
![](https://blog.kakaocdn.net/dn/3T9Qy/btsFCB8mcvS/WozXfEkrxmOluKVt3IQBkK/img.png)
- cluster security group규칙을 보면 **self chain설정**이 되어 있습니다.
![](https://blog.kakaocdn.net/dn/kzsR4/btsFF0eKhhC/mOdO3sGsWWgYoGncnQYk30/img.png)

## EKS 생성 과정
1. Control Plane 관련 리소스들이 AWS 소유 VPC에서 생성
2. 사용자의 VPC와 통신하기 위해서 사용자의 서브넷에 ENI를 2~4개 생성
3. 해당 ENI들은 Cluster Security Group과 Additional Security Group을 사용
4. 반면에, EKS 컴퓨팅 리소스는 Cluster Security Group만 사용 (단, Launch Template을 사용 시엔 명시한 Security Group을 사용)
# 참고자료
https://malwareanalysis.tistory.com/709




