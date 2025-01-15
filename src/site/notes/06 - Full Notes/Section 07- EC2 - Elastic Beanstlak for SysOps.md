---
{"dg-publish":true,"permalink":"/06-full-notes/section-07-ec-2-elastic-beanstlak-for-sys-ops/","dgPassFrontmatter":true}
---

# Tags
[[03 - Tags/Ultimate AWS Certified SysOps Administrator Associate 2024\|Ultimate AWS Certified SysOps Administrator Associate 2024]]

---
# 단서 질문
- Elastic Beanstalk란?
    개발자 관점에서 AWS 어플리케이션 배포의 자동화를 진행
- Elastic Beanstalk 비용은?
    사용한 리소스에 대한 금액만 낼뿐. Beanstalk 자체는 무료.
---
# 핵심 요약
- Elastic Beanstalk은 AWS에서 애플리케이션 배포를 자동화하는 개발자 친화적인 서비스입니다.
- 사용된 AWS 리소스에 대해서만 비용이 청구되며, Elastic Beanstalk 자체는 무료입니다.
- Web Server Tier와 Worker Tier 두 가지 환경 유형을 제공합니다.
- 다양한 프로그래밍 언어와 플랫폼을 지원합니다.
- 애플리케이션, 버전, 환경의 개념으로 구성되어 있습니다.
- 개발, 테스트, 프로덕션 등 여러 환경을 생성할 수 있습니다.
- 인프라 관리, 코드 배포, 로드 밸런서 설정 등을 자동화하여 개발자의 부담을 줄입니다.
---
# 핵심 필기
## [SAA/DVA] Beanstalk Overview
### Typical architecture: Web App 3-tier
![Pasted image 20250115152509.png](/img/user/image/Pasted%20image%2020250115152509.png)
	
## Developer problems on AWS
개발자 입장에서 AWS 사용 시 문제점
- 인프라 관리
- 코드 배포
- DB, LB를 일일이 설정
- 스케일링 관심사들

- 대부분의 웹 서비스는 동일한 아키텍처를 가진다 (ALB + ASG)
- 개발자가 원하는 건 코드가 동작하는 것
### Elastic Beanstalk - Overview
- Elastic Beanstalk는 개발자 관점에서 어플리케이션을 AWS 서비스로 배포하는 것이다.
- 매니지드 서비스
	- 자동으로 capacity 프로비저닝, 로드밸런싱, 스케일링, 어플리케이션 모니터링, 인스턴스 설정을 핸들링합니다.
	- 개발자는 코드만 작성하면 됩니다.
- 무료다. 사용한 인스턴스 만큼만 비용을 내면 된다.
### Elastic Beanstalk - Components
- Application: Elastic Beanstalk 컴포넌트의 집합 (환경, 버전, 설정, ...)
- Application 버전: Application 코드 버전
- 환경
	- Application version을 실행하는 AWS 리소스 집합 (동시에 단 하나의 application version만 가능하다)
	- 티어: Web Server Tier, Worker Tier
	- 여러 환경을 만들 수 있다. (dev, test, prod 등)
![Pasted image 20250115153329.png](/img/user/image/Pasted%20image%2020250115153329.png)
### Elastic Beanstalk - Supported Platforms
- Go, Java, Ruby, Node.js, PHP, Python, 컨테이너 ...
### Web Server Tier vs Worker Tier
![Pasted image 20250115153547.png](/img/user/image/Pasted%20image%2020250115153547.png)
![Pasted image 20250115153620.png](/img/user/image/Pasted%20image%2020250115153620.png)
- Worker Environment로 Elastic Beanstalk를 생성하면 SQS Queue가 같이 만들어지고 해당 큐로 메시지가 전송되어 트래픽을 처리합니다.
### Elastic Beanstalk Deployment Modes
![Pasted image 20250115153648.png](/img/user/image/Pasted%20image%2020250115153648.png)

---
# 참고 자료
