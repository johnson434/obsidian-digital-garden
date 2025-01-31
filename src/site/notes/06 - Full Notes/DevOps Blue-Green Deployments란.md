---
{"dg-publish":true,"permalink":"/06-full-notes/dev-ops-blue-green-deployments/","noteIcon":""}
---

 [[03 - Tags/배포 전략\|배포 전략]]
# Shared resources and cluster resources
Blue/Green 배포를 위해서는 먼저 공유자원을 나눌 필요가 있다.
공유자원이란 지속적으로 배포되고 여러 Application의 버전에서 공통적으로 사용되는 버전이다. 여기엔 Database가 포함된다.
# “Blue” and “Green”
Blue는 이전 버전에 어플리케이션이 돌아가는 환경이다.
Green은 새로운 버전에 어플리케이션이 돌아가는 환경이다. Staging 환경으로 실제로 배포되는 서비스와 가장 흡사한 배포 환경이다.
새로운 앱이 Green 영역에 배포되면 트래픽을 Blue에서 Green으로 옮긴다.
모든 트래픽이 이전되면 Blue 영역에 서비스는 모두 종료한다. 이후에 해당 영역을 재사용하거나 Green 영역에 문제가 발생할 시에 롤백한다.
# 장점과 단점
**장점**
- 모든 사용자가 동일한 버전의 어플리케이션을 사용한다.
- 작업이 끊기지 않는다.
**단점**
- Hot fix 배포가 오래 걸린다. : Blue 영역에서 오래 걸리는 작업을 실행하면 Green 영역으로 교체하는데 오래 걸림.
- Blue 영역은 항상 대기 중이므로 최소 2배 리소스가 필요하다.
## Rainbow 배포
Blue/Green 배포에서 더 나아가 영역을 추가한 배포 방식이다.(빨주노초파남보)
클러스터 사이에서 전송이 어려운 작업(비디오 인코딩, 웹 스크래핑 등)들이 실행될 때 사용된다.
여러 버전의 서비스를 배포한다. 따라서, 새로운 버전이 업데이트 되더라도 트래픽을 새로운 버전의 서비스로 할당하지 않는다.
## Canary 배포
새로운 버전을 일부 유저에게만 배포하여 피드백을 보고 결과에 따라 전체 유저로 넓힐지 유지할지를 정한다.

---

> [!info] What is a blue green deployment?  
> Blue/green deployments are a strategy to deploy a new version of an application.  
> [https://webapp.io/blog/what-is-a-blue-green-deployment/](https://webapp.io/blog/what-is-a-blue-green-deployment/)