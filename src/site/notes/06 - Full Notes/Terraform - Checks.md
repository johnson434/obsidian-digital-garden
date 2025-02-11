---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-checks/","noteIcon":""}
---

# Tags
- [[03 - Tags/Terraform\|Terraform]]
---
- `plan` 혹은 `apply`의 phase에서 `check`를 통해 외부 리소스 등의 검증이 가능하다.
``` hcl
check "health_check" {
  data "http" "terraform_io" {
    url = "https://www.terraform.io"
  }

  assert {
    condition = data.http.terraform_io.status_code == 200
    error_message = "${data.http.terraform_io.url} returned an unhealthy status code"
  }
}
```
---
# References
https://developer.hashicorp.com/terraform/language/checks