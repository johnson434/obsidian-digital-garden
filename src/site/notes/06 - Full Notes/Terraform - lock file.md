---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-lock-file/","noteIcon":""}
---

[[03 - Tags/Terraform\|Terraform]]
## Lock file이란?
`.terraform` 서브 디렉터리를 위한 lock 파일로 Terraform 전체의 설정을 위한 버전을 명시한 파일이다. 특정 모듈을 위한 파일이 아니다.
Terraform init을 통해 생성되며 파일의 prefix는 `.tf.hcl`이다.
Source Code Repository에 올려서 해당 버전이 업데이트 되는지를 체크하자.
> [!.terraform 디렉터리는 뭔데]
> `provider`나 외부 `module`을 저장하는 공간으로 라이브러리 저장소 역할을 한다.
``` bash
john@john:~/Projects/Playground/IaC$ cat .terraform.lock.hcl 
# This file is maintained automatically by "terraform init".
# Manual edits may be lost in future updates.

provider "registry.terraform.io/hashicorp/aws" {
  version     = "5.84.0"
  constraints = "~> 5.75"
  hashes = [
    ...
  ]
}
```

## Dependency Installation
`terraform init`은 외부 의존성을 설치하는데 이 과정에서 Configuration으로 설정한 값과 `.terraform.lock.hcl`에 명시된 버전(`recorded selection`이라고 표현)을 참조하여 설치한다.
만약, 이전에 설정된 `recorded selection`이 있으면 무조건 해당 버전을 사용한다. 단, `terraform init` 과정에서 `-upgrade` 플래그 값을 사용하면 최신 버전을 사용한다.