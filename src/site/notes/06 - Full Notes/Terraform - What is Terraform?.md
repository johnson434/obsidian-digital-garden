---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-what-is-terraform/","noteIcon":""}
---

[[03 - Tags/Terraform\|Terraform]]
Terraform은 `HarshiCorp`에서 만든 IaC 소프트웨어다.
코드를 통해서 인프라를 구성할 수 있으며, provider를 통해서 여러 CSP를 이용할 수 있습니다.
MPL 2.0 라이센스에서 BSL 라이센스로 변경됨에 따라 이용에 제한이 생겼습니다. 이로 인해 차후에 Terraform의 시장 점유율이 줄어들지 관찰할 필요가 있음.
선언형으로 리소스를 정의하므로 인프라 객체가 어느 순서대로 생성될지 신경 쓰지 않아도 된다.(`resource` block의 참조 순서를 통해서 알아서 순서대로 인프라를 구성해준다.)
단, 몇몇 인프라는 명시적으로 `depends_on`이라는  meta-arguments를 사용해야 한다.****