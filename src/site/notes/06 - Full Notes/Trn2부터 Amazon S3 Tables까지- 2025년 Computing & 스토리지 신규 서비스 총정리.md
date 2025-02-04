---
{"dg-publish":true,"permalink":"/06-full-notes/trn2-amazon-s3-tables-2025-computing-and/","noteIcon":""}
---

[[03 - Tags/AWS reCap reinvent 2024\|AWS reCap reinvent 2024]]
# EKS
## Amazon EKS 하이브리드 노드
- 기존엔 EKS와 온프레미스 노드들을 혼용하기 위해서 Outposts나 EKS Anywhere을 사용했다.
- 클러스터 구성 후에 설정하는 거임
- 각 서비스의 한계점
	- EKS Anywhere은 별도로 클러스터를 구성해야함.
	- Outposts는 Control Plane을 직접 구성함.
## EKS Auto Mode
- AWS는 클러스터 구성 자동화가 비전인듯
- 동적 확장, 클러스터 관리 등, 파드 네트워킹, 로드밸런싱, 오토스케일링도 자동으로 처리함
- 사용자가 Kubernetes에 대한 깊은 이해가 없어도 클러스터를 운영 가능하게 만드는 것이 목표인듯
# Storage
## S3 Tables가 나오기 전엔
- 분석 워크로드를 위한 표 형식의 데이터를 위해서 S3를 사용하는 경우가 증가하고 있다.(Parquet을 사용함[^1])
- S3의 버켓은 prefix로 초당 5,500의 GET 요청을 제공함. (스케일링 일어나긴함)
## S3 Tables
- 테이블 형식 데이터를 잘 다루기 위한 서비스인듯?
- 읽기 기준 최대 55,000을 제공. (S3 Bucket은 5,500)
- 서울 리전에 없다함
## S3 Metadata
- 기존의 S3의 메타데이터는 검색이 어려움.
- S3 Inventory가 있지만 하루 전이나 일주일 전에 생성해야함.
- 이를 해결하기 위해서 S3 Metadata 기능이 만들어짐.
## Data Integrity
- 기존엔 S3와 Storage의 checksum을 통해서 정합성을 맞춰왔다.
- 최신 SDK/CLI에선 클라이언트와 S3 API 사이의 데이터 무결성도 검증한다.
## Amazon EFS cross-account replication
- 서로 다른 계정의 EFS에서도 Replication이 가능해짐.
---
[^1]: Parquest은 컬럼형 데이터베이스의 저장 방식 포맷이다.