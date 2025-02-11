---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-import/","noteIcon":""}
---

# Tags
- [[03 - Tags/Terraform\|Terraform]]
---
## Import
- 테라폼 외부에서 정의한 리소스를 테라폼에 `import`할 수 있다.
- 한 번 `import`된 리소스는 `.tfstate` 파일에서 관리된다. 
``` hcl
import {
  to = aws_instance.example
  id = "i-abcd1234"
}

resource "aws_instance" "example" {
  name = "hashi"
  # (other resource arguments...)
}
```


---
# References
https://developer.hashicorp.com/terraform/language/import