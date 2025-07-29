---
{"dg-publish":true,"permalink":"/06-full-notes/aws-krug-architecture/","noteIcon":""}
---

#AWS_KRUG

## 무신사, 실시간으로 콘텐츠를 잇다 - 경험을 바꾼 통합 아키텍처 이야기

컨텐츠 별로 서비스가 분리되어 통합된 서비스 제공이 불가능했다.
Strimizi Kafka Connect: 쿠버네티스 기반이라 운영이 어렵지만 구성원들이 CKA 취득한 숙련 상태였으므로 괜찮았다.
CDC?
굳이 서로 다른 서비스에 개별로 질의하지 않고 하나에 DB로 통합할 이유가 있었을까? => 서비스 로직이나 DB 테이블 구조 등이 처리하기 편해짐?
#Algorithm 
## Aurora Database Streaming을 활용한 실시간 DB 이벤트 모니터링 및 대응
Recon Lab: 사진을 기반으로 3D 공간을 만든다.
데이터베이스의 활동을 준 실시간(Near real time)으로 모니터링이 가능하다.

Aurora - kinesis - lambda - sns

특정 테이블 / 레코드의 이상 징후 감지 => 삭제되면 되지 않을 데이터 레코드가 삭제된다거나(삘리 찾을수록 누적되는 손해를 방지할 수 있다.)
Aurora serverless엔 사용 불가. 특정 인스턴스 타입에만 사용이 가능하다.

## Lambda function with cloudfront
Lambda function url은 15분. API Gateway랑 쓸 때는 29초가 한계

Lambda 호출모드
- RESPONSE_STEAM 실시간으로 스트림을 리턴
	- 런타임 환경 nodejs만 가능.
	- 동영상 숏폼 사이트 스트리밍 사이트 만들 수 있지 않나. 15분 한정이니까.
CloudFront의 Origin으로 사용 가능.

내부적으로 OAC 지원. Cloudfront 넘어가서 호출 방지.

DMS Stream
