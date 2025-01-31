---
{"dg-publish":true,"permalink":"/06-full-notes/dev-ops-linting/","noteIcon":""}
---

 [[03 - Tags/Linting\|Linting]]
# Linting이란?
프로그램 소스 코드에서 문제점을 찾아주는 프로그램이다.
# DevOps가 Linting을 어떻게 처리할까?
쉬운 활용 : CI 과정에서 스타일 가이드와 다르면 이를 merge 불가능하게 만들기.
고급 활용 : 언어별 Linting tool을 활용하여 자동으로 규칙에 맞게 고치기 (ex. Python의 black)