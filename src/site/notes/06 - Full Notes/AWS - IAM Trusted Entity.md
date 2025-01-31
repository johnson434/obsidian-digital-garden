---
{"dg-publish":true,"permalink":"/06-full-notes/aws-iam-trusted-entity/","noteIcon":""}
---


[[03 - Tags/AWS IAM\|AWS IAM]]

IAM에서 계속 헷갈리던 내용이 Trusted Entities다.
IAM은 상세히 보지 않으면 역할, 정책, 인라인 정책 등 설정이 한 두개가 아니므로 무작정 따라하기만 하면 이게 뭘하는지도 모르고 설정하게 된다.
이 중에 Trusted Entity 이게 어디에 쓰이는 건지 헷갈렸는데 이건 역할의 설정이다.
Role은 누가 수행하기 위한 말 그대로 역할이다. 그런데, 역할을 아무나 수행할 수 있다면 문제가 되지 않을까? 
예를 들어, Admin 정책을 가지고 있는 Role이 있는데 아무 유저나 해당 역할을 수행할 수 있다면 모든 사람이 Admin 노릇을 할 수 있다.
이를 방지하기 위해서 Trusted Entity. 신뢰받는 엔티티를 설정한다.
이는 해당 역할을 사용할 수 있는 주체를 명시하는 것으로 이를 통해 특정 사용자에게만 해당 역할을 사용 가능하게 만든다.

\[Trusted Entity\]
``` json
// 아래와 같은 식으로 설정.
// Principal: Pod Identity가 주체가 된다.

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "pods.eks.amazonaws.com"  // EKS Pod Identity 사용 시
        // 또는 IRSA 사용 시: "Federated": "arn:aws:iam::<ACCOUNT_ID>:oidc-provider/<EKS_OIDC_PROVIDER>"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          // IRSA 사용 시: "<EKS_OIDC_PROVIDER>:sub": "system:serviceaccount:<NAMESPACE>:<SERVICE_ACCOUNT>"
          "pods.eks.amazonaws.com:sub": "system:serviceaccount:<NAMESPACE>:<SERVICE_ACCOUNT>"
        }
      }
    }
  ]
}
```