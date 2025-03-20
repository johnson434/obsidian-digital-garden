---
{"dg-publish":true,"permalink":"/kubernetes-namespace/","noteIcon":""}
---


Policy
리소스 제한
동일 네임스페이스에선 호스트 네임만 명시해도 되지만 다른 네임스페이스면 풀네임 명시
"db-service"
"db-service.dev.svc.cluster.local"
"호스트명.네임스페이스명.svc.cluster.local"
- db-service: 서비스명
- dev: 네임스페이스
- svc: 서비스
- cluster.local: dns 서버에 위치로 클러스터 내에 로컬 DNS 서버를 사용한다는 뜻