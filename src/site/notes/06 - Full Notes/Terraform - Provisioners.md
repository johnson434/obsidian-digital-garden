---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-provisioners/","noteIcon":""}
---

# Tags
- [[03 - Tags/Terraform\|Terraform]]
- [[06 - Full Notes/Terraform - resource\|Terraform - resource]]
---
> **Note:** Provisioners should only be used as a last resort. For most common situations there are better alternatives. For more information, see the sections above.
# Provisioners
- 로컬이나 리모트에서 특정 액션을 실행하도록 설정 가능.
- Provisioner를 쓰지 말고 다른 해결 방법이 있다면 그걸 쓰라고 적어놨음[^1]
- 예를 들어, 설정 관리(configuration management)를 provisioner로 할 수 있지만 `packer`[^2] 사용을 더 권장한다.
- 지금 딱히 크게 공부가 필요한 파트는 아닌 느낌이라 넘어간다. (Ansible이나 Packer가 더 나아보인다. Ansible은 멱등성도 제공하므로)
---
# References
https://developer.hashicorp.com/terraform/language/resources/provisioners/syntax

[^1]: Even if your specific use-case is not described in the following sections, we still recommend attempting to solve it using other techniques first, and use provisioners only if there is no other option.
[^2]: https://developer.hashicorp.com/terraform/tutorials/provision/packer?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS
