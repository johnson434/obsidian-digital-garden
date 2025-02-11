---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-test-directory/","noteIcon":""}
---

# Tags
- [[03 - Tags/Terraform\|Terraform]]
- [[06 - Full Notes/Terraform - test\|Terraform - test]]
---
## Terraform 테스트 코드를 어디에 둘까
테스트 코드를 작성하면서 디렉터리에 대한 고민을 해봤다.
첫번째는 최상위 `tests` 폴더에 모든 테스트 코드를 작성하는 방법.
두번째는 각 모듈의 `tests` 폴더를 만드는 방법이다.

---
### 방법 1. 최상위 `tests` 폴더 활용
- 최상위 `tests` 폴더에 테스트 코드를 작성한다.
``` json
.
├── modules
│   ├── network
│   │   └── security_group
│   ├── s3
│   └── template
└── tests # 테스트 코드가 작성될 디렉터리
```
#### 장점
- CI/CD를 통한 테스트 자동화가 간편하다.
	- 최상위 디렉터리에서 `terraform test` 명령어를 실행하면 끝.
#### 단점
- 테스트 코드가 번잡해진다. (예를 들어, module을 테스트 하기 위해선 모듈을 실행하는 `setup` run 블록과  검증을 위한 `assert` run 블록이 필요하다.)
``` hcl
# setup 블록
run "setting_security_group" {  
  module {
    source = "./modules/network/security_group"
  }
}

# assert 블록
run "is_security_group_name_equal_to_variable" {
  command = plan

  assert {
		# run.setting_security_group.security_group_name은
		# security_group 모듈의 outputs를 참조한다.
    condition = run.setting_security_group.security_group_name == "blahblah"
    error_message = "security_group is not blahblah name: ${run.setting_security_group.security_group_name}"
  }
}
```
- 모든 테스트 코드가 /tests 폴더에 모이므로 디렉터리가 복잡해진다. 
```
.
└── tests # 테스트 코드가 작성될 디렉터리
    ├── s3 # s3 모듈 테스트를 위한 디렉터리
    ├── s3_put_object # s3 오브젝트 생성 테스트
    ├── s3_delete_object # s3 오브젝트 삭제 테스트
    ├── network # 네트워크 리소스 테스트
    ├── rds # rds 테스트
    ├── etc # 기타 등등...
    └── intergration
```
- 모듈 테스트 시에 상태 관리가 복잡해진다.
	- module을 사용하는 `run` block끼리 상태 파일을 공유하고 모듈을 사용하지 않는 `run` block끼리 상태 파일을 공유한다.
``` hcl
# security_group 모듈을 사용하는 run 블록끼리 상태 파일을 공유한다.
run "setting_security_group" {  
  module {
    source = "./modules/network/security_group"
  }
}

# 위와 동일한 모듈을 사용하므로 위 run 블록과 상태 파일을 공유한다.
run "test1" {
	module {
    source = "./modules/network/security_group"
	}
}

# 모듈을 사용하지 않는 블록이므로 main 상태 파일을 공유한다.
run "is_security_group_name_equal_to_variable" {
}
```
---
### 방법 2. 모듈별로 테스트 디렉터리 생성
``` json
.
├── modules
│   ├── network
│   │   └── security_group
|   |       └── tests
│   └── s3
|       └── tests
└── tests
```
- 각 모듈 폴더에 `tests` 폴더를 만든다.
#### 장점
- 테스트 코드가 간단해진다. (리소스를 바로 참조가능하므로 이전 방법처럼 `setup` 블록과 `assert` 블록을 나눌 필요가 없다.)
``` hcl
mock_provider "aws" {}

# 이전과 달리 하나의 run 블록으로 처리 가능
run "is_security_group_name_equal_to_variable" {
  command = plan
  variables {
    vpc_id = "vpc_id"
    security_group_info = {
      "Name": "blahblah",
      "description": "description value"
    }
  }

  assert {
    condition = aws_security_group.default.name == "blahblah"
    error_message = "security_group is not blahblah name: ${aws_security_group.default.name}"
  }
}
```
- 모듈의 결합도가 낮다. 예를 들어, 특정 모듈을 분리하거나 따로 배포할 필요가 생길 수 있다. 만약에 1번 방법을 사용했다면 모든 테스트 코드가 최상위 `/tests` 폴더에 있다. 수많은 테스트케이스 중에서 이전할 모듈의 테스트 코드를 찾기도 번거롭고 테스트 코드 수정도 발생한다.
``` hcl
# 다음은 가상의 /tests 폴더에 존재하는 한 테스트 파일이다.
# 아래 코드에서 security_group 모듈만 제거한다고 생각해보자.
# 테스트 코드를 제거한다고 끝이 아니다.
# 제거한 테스트 코드를 분리될 모듈에 옮겨야 하고 
# 옮기는 과정에서 테스트 코드 수정도 필요하다.
run "setting_security_group" {  
  module {
    source = "./modules/network/security_group"
  }
}

run "is_rds_created" {
  module {
    source = "./modules/rds"
  }  
}

run "is_security_group_name_equal_to_variable" {
  module {
    source = "./mo# setup 블록dules/security_group"
  }  
}
```
#### 단점
- 테스트 자동화가 복잡해진다.
	- 예: ec2, rds, security group 모듈이 있다면 각 모듈에서 테스트를 별도 실행이 필요하다.
## 결론
어느 방법이 정답이라고 확언하진 못하겠다. 상황에 따라 다를 것이다.
내가 생각하는 가장 좋은 방법은 최상위 `/tests` 폴더에선 **모듈 테스트를 최대한 피하고** 전체적인 **통합 테스트 위주로 진행**한다. 
추후에 **분리될 가능성이 있는 모듈**, 혹은 **개별 관리가 필요한 모듈**은 해당 모듈에 `/tests` 폴더를 만드는 게 좋을 것 같다.
```
├── main.tf
├── modules
│   ├── ec2  # eks 클러스터로 이전할 경우, 제거될 가능성 있음.
│   │   └── tests
│   ├── rds
│   └── vpc
├── outputs.tf
├── terraform.tf
├── tests
│   └── intergration_test01.tftest.hcl
└── variable.tf
```