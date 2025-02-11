---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-set/","noteIcon":""}
---

[[03 - Tags/Terraform\|Terraform]]

---
# Set
- Terraform은 Set을 Syntax로 지원하지 않는다.
- 따라서, 배열로 선언 후에 `toset` 함수 써서 만든다.
- 당연히 set이므로 중복을 허용하지 않는다.
``` hcl
set = toset(["abc", "def"])
```