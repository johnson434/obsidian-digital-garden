---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-test-mocks/","noteIcon":""}
---

# Tags
- [[06 - Full Notes/Terraform - test\|Terraform - test]]
---
# 핵심 필기
- 대부분의 테스트처럼 테라폼도 Mocking 기능을 제공한다.
- Mocking을 통해 만들어진 디폴트 값을 가지며 별개로 사용자가 설정할 수도 있다.
- 서로 다른 테스트끼리 `.tfmock.hcl` 파일을 통해서 데이터를 공유할 수 있다.
``` hcl
# ./testing/aws/data.tfmock.hcl
# mock_provider를 사용하면
# aws_s3_bucket으로 접근하면 아래 resource 값으로 접근이 된다.
# 예: aws_s3_bucket.[모든이름].arn = "arn:aws:s3:::name"
mock_resource "aws_s3_bucket" {
  defaults = {
    arn = "arn:aws:s3:::name"
  }
}

mock_data "aws_s3_bucket" {
  defaults = {
    arn = "arn:aws:s3:::name"
  }
}
```

``` hcl
mock_provider "aws" {
  source = "./testing/aws" 
	# 해당 디렉터리에 있는 리소스들이 mock_resource나 mock_data의 값들을 사용
	alias = "fake" # 별칭을 줘서 run block마다 서로 다른 프로바이더 사용 가능
}
```

---
# References
- https://developer.hashicorp.com/terraform/language/tests/mocking