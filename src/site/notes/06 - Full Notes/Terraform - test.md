---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-test/","noteIcon":""}
---

# Tags
- [[03 - Tags/Terraform\|Terraform]]
---
# 핵심 필기
## 통합 or 단위 테스트(Integration or Unit testing)
- `terraform test`는 실제로 인프라를 만들고 이를 통해 검증을 합니다.
- 이는 전형적인 통합테스트와 유사한 역할입니다. 왜냐면 테라폼의 중요 기능을 테라폼이 만들 인프라를 통해서 실행하고 검증하기 때문입니다.
## tftest.hcl
- 테라폼의 테스트 파일이다.
- 1개 이상의 `run` 블록
- `variables`는 1개거나 없거나
- `provider`는 1개거나 없거나
- 블록의 순서는 중요하지 않음.
## 테스트 실행
`terraform test`로 테스트를 실행할 수 있지만, 현재 `working directory`에 있는 테스트나 `working_directory/tests` 폴더에 있는 테스트만 실행합니다.
모듈에 따로 테스트를 만들어로 실행하지 않습니다. (아무래도 Terraform이 디렉터리 자체를 모듈로 판단하다 보니까 생기는 일인듯)
따라서, `--test-directory=path` 플래그를 통해서 추가해줘야 합니다.
## `run` block
- 테스트를 정의하는 블록이다.
- 기본적으로 command 속성의 값은 apply입니다.
	- command가 apply인 경우엔 테스트를 위해 apply 작업을 실행합니다. (통합테스트와 유사)
	- command가 plan인 경우엔 테스트를 위해 paln 작업을 실행합니다. 따라서, 실제로 인프라가 만들어지진 않습니다. (단위테스트와 유사)
- `run` 블록은 순서대로 실행됩니다.
- `run` 블록은 현재 블록이나 이전의 `run` 블록들의 outputs를 참조할 수 있습니다.
## Variables
- `run` 블록 내에 `variables` 블록으로 테스트에 사용할 변수를 지정할 수 있습니다.
- 제일 우선 순위가 높다.(인라인이니까 당연히 우선 순위가 제일 높지)
``` hcl
variables {
  bucket_prefix = "test"
}

run "valid_string_concat" {
  command = plan

  variables {
    web_server_name = "test"    
  }

  assert {
    condition     = resource.aws_instance.web.tags.Name == "test"
    error_message = "S3 bucket name did not match expected"
  }
}

run "check_server_name" {
  command = plan

  variables {
    web_server_name = "test2"    
  }

  assert {
    condition     = resource.aws_instance.web.tags.Name == "test2"
    error_message = "S3 bucket name did not match expected"
  }
}
```
- 서로 다른 모듈을 테스트할 때, `run` 블록의 outputs를 참조하면 효과적이다.
![Pasted image 20250207202350.png](/img/user/image/Pasted%20image%2020250207202350.png)
## Modules
- `run` 블록에서 모듈을 수정하여 테스트를 실행할 수 있다.
``` hcl
run "setup" {
  # Create the S3 bucket we will use later.

  module {
    source = "./testing/setup"
  }
}

run "execute" {
  # This is empty, we just run the configuration under test using all the default settings.
}

run "verify" {
  # Load and count the objects created in the "execute" run block.

  module {
    source = "./testing/loader"
  }

  assert {
    condition = length(data.aws_s3_objects.objects.keys) == 2
    error_message = "created the wrong number of s3 objects"
  }
}

```

## Module State
- `terraform test` 명령어를 실행하면 메모리에 최소 1개 이상의 `state` 파일이 저장된다. 
- `module` 블록을 명시하여 **대체  모듈(alternate module)**을 로드하지 않는 모든 `run` 블록들은 main `state` 파일을 공유한다.
- 추가적으로 **대체 모듈**별로 하나의 `state` 파일을 가진다.
- **대체 모듈**의 상태 파일은 해당 모듈을 사용하는 모든 `run` 블록이 공유한다.
- 아래 예제엔 총 3개의 `state` 파일이 만들어진다.
``` hcl
run "setup" {
	# setup 대체 모듈을 처음으로 호출했으므로
	# setup 모듈을 사용하는 run 블록이 공유하는 state 파일을 만든다.

  module {
    source = "./testing/setup"
  }
}

run "init" {
	# 대체 모듈을 사용하지 않는다.
	# 따라서, main state file을 사용한다.
}

run "update_setup" {
	# 이전에 setup 모듈을 대체모듈로 사용한 run 블록이 잇다.
	# 현재 run 블록은 해당 state 파일을 공유한다.

  module {
    source = "./testing/setup"
  }
}

run "update" {
	# 대체 모듈 명시하지 않았으므로 main 상태 파일사용
}

run "loader" {
	# 이전에 호출되지 않은 loader 모듈을 호출
	# 따라서, 새로운 state 파일을 만든다.	
  module {
    source = "./testing/loader"
  }
}

# 최종적으로 setup 모듈을 참조하여 만든 state 파일
# 모듈을 명시하지 않아 만든 main state 파일
# 마지막으로 loader 모듈을 참조하여 만든 state 팡리
# 총3개의 state 파일이 만들어진다.
```
## expect_failures
- 실패를 가정하는 테스트 케이스도 존재한다.
	- 예: application 개발 시에 잘못된 이메일로 회원가입
- 테라폼도 실패를 가정하는 테스트 케이스를 만들 수 있다.
``` hcl
run "one" {
  # This time we set the variable is odd, so we expect the validation to fail.

  command = plan

  variables {
    input = 1
  }

  expect_failures = [
    var.input,
  ]
}
```

---
# References
[Files and Directories - test files](https://developer.hashicorp.com/terraform/language/files/tests)
https://developer.hashicorp.com/terraform/language/tests
https://developer.hashicorp.com/terraform/tutorials/configuration-language/test