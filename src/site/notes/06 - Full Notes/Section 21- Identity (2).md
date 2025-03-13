---
{"dg-publish":true,"permalink":"/06-full-notes/section-21-identity-2/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ultimate AWS Certified SysOps Administrator Associate 2024\|Ultimate AWS Certified SysOps Administrator Associate 2024]]

# 단서 질문 및 답변

**질문:**  
AWS STS란 무엇인가요?

**답변:**
- AWS STS(Security Token Service)는 임시 보안 토큰을 발급해주는 서비스로, 이 토큰을 사용하여 AWS 리소스에 접근할 수 있습니다.
- 토큰의 유효기간은 최대 1시간이며, 자격 증명을 받은 후 일정 시간이 지나면 새로 받아야 합니다.
- `AssumeRole`, `AssumeRoleWithSaml`, `AssumeRoleWithWebIdentity`, `GetSessionToken` 등의 API를 통해 사용자나 애플리케이션이 임시 자격 증명을 받을 수 있습니다.

---

# 핵심 요약

- **AWS STS 역할**: 임시 보안 자격 증명(토큰) 발급 → AWS 리소스 접근 권한 부여
- **주요 API**:
    - **AssumeRole**: 자기 계정 내 혹은 교차 계정(Cross Account) 접근 권한을 임시로 획득
    - **AssumeRoleWithSaml**: SAML 방식으로 인증받은 사용자에게 임시 자격 증명 발급
    - **AssumeRoleWithWebIdentity**: Facebook, Google, OIDC 등 IdP 로그인을 통해 임시 자격 증명 발급
    - **GetSessionToken**: MFA로 로그인한 일반 사용자/루트 사용자에게 임시 자격 증명 발급
- **제한사항**: 임시 자격 증명은 기본적으로 15분 ~ 1시간 동안 유효
- **Cross-Account Access**:
    1. 대상 계정에 IAM Role 생성
    2. 해당 Role에 접근할 주체(Principal) 정의
    3. STS API로 임시 자격 증명 요청 및 발급
    4. 발급받은 자격 증명으로 대상 계정의 AWS 리소스에 접근

---

# 핵심 필기

## AWS STS (Security Token Service)

- AWS 리소스에 접근하기 위한 임시 권한(토큰)을 발급하는 서비스
- 보안 강화를 위해 유효 시간(15분~1시간)이 지나면 새로 받아야 함

### STS 주요 API
1. **AssumeRole**
    - 자신의 계정에 Role을 적용하여 보안을 강화
    - Cross-Account Access(교차 계정 접근)를 통해 타겟 계정의 특정 액션 수행 권한
2. **AssumeRoleWithSaml**
    - SAML 인증을 통해 로그인한 사용자에게 임시 자격 증명 발급
3. **AssumeRoleWithWebIdentity**
    - Facebook, Google, OIDC 등 외부 IdP로 인증한 사용자에게 임시 자격 증명 발급
    - AWS는 이 방법 대신 **Amazon Cognito** 사용을 권장
4. **GetSessionToken**
    - MFA 인증을 거친 일반 사용자나 루트 계정 사용자에게 임시 자격 증명을 발급
### 1. IAM Role 신뢰 정책(Trust Policy) 예시

다음은 교차 계정 간 접근을 위해 Role을 생성할 때 사용할 수 있는 신뢰 정책 예시입니다. 

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCrossAccountAssumeRole",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:root"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

- **Principal**: Role을 사용할(AssumeRole할) 수 있는 주체를 명시합니다. 여기서는 `123456789012`가 다른 AWS 계정 번호라고 가정합니다.  
- **Action**: `sts:AssumeRole`을 허용해야 Role Assume이 가능합니다.  

### 같은 계정 내에서 사용하기
만약 같은 계정 내의 사용자/그룹/서비스가 Role을 Assume해야 한다면, `Principal`에 해당 사용자나 서비스의 ARN을 넣어주면 됩니다:
```json
"Principal": {
  "AWS": "arn:aws:iam::111122223333:user/yourUserName"
}
```

### 2. IAM Policy (권한 정책) 예시

IAM Role이 실제로 사용할 권한 정책은 아래와 같이 작성할 수 있습니다:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowS3ReadWrite",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": [
        "arn:aws:s3:::example-bucket/*"
      ]
    }
  ]
}
```

- 해당 정책을 Role에 연결(IAM 콘솔 또는 CLI에서 Role 생성 시 첨부)하면, 이 Role을 Assume한 사용자는 `example-bucket` 버킷에 대한 Get/Put 액션을 수행할 수 있게 됩니다.

### 3. STS AssumeRole API 요청

역할과 신뢰 정책, 권한 정책을 설정한 뒤, `AssumeRole` API를 호출하여 임시 자격 증명을 얻을 수 있습니다. 예를 들어, AWS CLI에서 다음 명령어를 사용할 수 있습니다:

```bash
aws sts assume-role \
  --role-arn "arn:aws:iam::123456789012:role/CrossAccountS3AccessRole" \
  --role-session-name "ExampleSession"
```

출력 예시(간략화):
```json
{
    "Credentials": {
        "AccessKeyId": "ASIAxxxxxxxxxxxx",
        "SecretAccessKey": "xxxxxx...",
        "SessionToken": "xxxxxx...",
        "Expiration": "2025-04-20T00:00:00Z"
    },
    "AssumedRoleUser": {
        "AssumedRoleId": "AROAxxxxxxx:ExampleSession",
        "Arn": "arn:aws:iam::123456789012:role/CrossAccountS3AccessRole"
    }
}
```

- **Credentials**: 임시 자격 증명 (유효 시간: 기본 1시간, 정책에 따라 설정 가능)  
- **AssumedRoleUser**: 현재 Assume한 Role 정보  
## Using STS to Assume a Role
![Pasted image 20241029105811.png](/img/user/image/Pasted%20image%2020241029105811.png)
1. **IAM Role 정의**
    - 자기 계정 또는 교차 계정(cross-account)에 접근 가능한 IAM Role을 생성
2. **Access Definition**
    - 해당 Role에 누가 접근할 수 있는지(Principal) 명시
3. **STS 호출**
    - `AssumeRole` 등의 API를 통해 임시 자격 증명을 획득
4. **임시 자격 증명 사용**
    - 발급받은 자격 증명(Access Key, Secret Key, Session Token)을 이용해 지정된 시간 동안 AWS 리소스에 접근

### Cross-Account with STS
![Pasted image 20241029110019.png](/img/user/image/Pasted%20image%2020241029110019.png)
- 교차 계정 간 접근에 유용
- Role과 STS를 함께 사용하여 보안성과 관리 편의성을 높일 수 있음

---

# 참고 자료

- [AWS STS 공식 문서](https://docs.aws.amazon.com/STS/latest/APIReference/Welcome.html)
- [IAM & STS Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)

---
# 주석

- 본 문서는 AWS STS(Security Token Service)에 대한 간단한 요약과 사용 방법을 정리하였습니다.
- 첨부된 이미지는 STS를 통한 IAM Role 가정(AssumeRole)과 Cross-Account Access 흐름을 시각화한 예시입니다.
- 실무 적용 시, IAM 정책 및 보안 설정을 꼼꼼히 점검해야 합니다.