---
{"dg-publish":true,"permalink":"/06-full-notes/terraform-functions/","noteIcon":""}
---

# Tags
- [[03 - Tags/Terraform\|Terraform]]
---
# 핵심 필기
> [!암기할 필요가 있나?]
> 함수는 필요할 때, 찾아서 쓰는 게 맞지. 굳이 이걸 정리할 필요는 없어보인다. 처음 보는 함수랑 "이건 알면 좋겠다." 싶은 함수만 적겠다.
## 네트워크 관련 함수
### `cidrhost`
- `cidrhost("IP대역", 호스트번호)`
- 해당하는 IP 대역의 n번째 호스트 번호가 가질 주소를 보여준다.
```
# 0번째 주소부터 시작함.
cidrhost("10.0.0.0/22", 1)
> 10.0.0.1 # 왜냐면 10.0.0.0은 네트워크 주소니까 다음 번호부터 시작
cidrhost("10.0.0.0/22", 256)
> 10.0.1.0
```
### `cidrsubnet`
- `cidrsubnet("IP대역", 추가로 네트워크 주소로 사용할 비트, n번째 네트워크)`
```
cidrsubnet("10.0.0.0/16", 8, 0)
> 10.0.0.0/24
cidrsubnet("10.0.0.0/16", 8, 1)
> 10.0.1.0/24
```
### cidrsubnets
- cidrsubnets("IP대역", 추가비트, 추가비트, ...)
- 현재 IP대역에 서브넷을 추가비트만큼 계속 할당해서 만든다.
```
cidrsubnets("10.0.0.0/16", 8, 8, 4)
> 10.0.0.0/24, 10.0.1.0/24, 10.0.16.0/20
```
- 위의 예시에서 3번째 4비트가 `10.0.16.0/20`이 된 이유는 `10.0.0.0/20 => 10.0.0.0 ~ 10.0.15.255` 네트워크 대역이 선점되어 다음 네트워크 대역인 `10.0.16.0/20`이 온 것이다. 
## 그 밖에 함수
- lookup
- trimspace
- startswith
- endswith
- 기타 등등
- 합집합, 교집합, 차집합
- 인코딩 디코딩, 해싱, 암호화(json, yaml, url, base64, sha256, sha512 등)
- 파일시스템(절대 경로)
- 시간
- try-catch
- can
---
# References
https://developer.hashicorp.com/terraform/language/functions