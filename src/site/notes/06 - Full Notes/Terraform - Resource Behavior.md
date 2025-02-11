---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-resource-behavior/","noteIcon":""}
---

[[03 - Tags/Terraform\|Terraform]]

# 테라폼이 어떻게 Configuration을 적용하나?
- Configuration을 적용하면 해당하는 리소스를 생성한다. (state에 없다는 전제 하에)
- `state`엔 존재하지만 `configuration`엔 존재하지 않는다면 제거한다.
- `state`의 값과 `configuration` 설정 값이 다르면 업데이트한다. (업데이트가 불가능한 설정 값이면 제거하고 새로 만듬)
https://developer.hashicorp.com/terraform/language/resources/behavior
