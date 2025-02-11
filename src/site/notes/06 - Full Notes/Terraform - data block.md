---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-data-block/","noteIcon":""}
---

# Tags
- [[03 - Tags/Terraform\|Terraform]]
---
# 핵심 내용
## Data Resource Behavior
- `plan` phase에서 데이터를 조회한다.
- 단, `apply` phase까지 조회를 미뤄야 하는 경우엔 `plan`에서 알려준다.
- `data` 조회를 `plan`이 아닌 `apply` phase까지 미루는 경우
	- `apply` 직전까진 예측이 불가능한 값인 경우
	- 현재 `plan`에 의해 변경되는 리소스가 있으며, `data` 리소스가 해당 리소스에 의존하는 경우
- 만약, managed resource를 직접 참조한다면 `depends_on`으로 참조한 것과 동일하게 `apply` phase까지 조회를 기다렸다가 값을 반영한다. 따라서, 항상 최신 데이터를 조회한다.
- `depends_on`은 항상 data의 조회를 `apply` phase까지 미루므로 사용을 권장하지 않는다.[^1]
- 리소스의 부분집합이므로 리소스와 동일한 `meta-arguments`를 지원한다. 단, `lifecycle config` 제외
# References
https://developer.hashicorp.com/terraform/language/data-sources
https://developer.hashicorp.com/terraform/language/data-sources#data-resource-dependencies

[^1]: NOTE: In Terraform 0.12 and earlier, due to the data resource behavior of deferring the read until the apply phase when depending on values that are not yet known, using depends_on with data resources will force the read to always be deferred to the apply phase, and therefore a configuration that uses depends_on with a data resource can never converge. Due to this behavior, we do not recommend using depends_on with data resources.
