---
{"dg-publish":true,"permalink":"/06-full-notes/section-15-cloud-front/","noteIcon":""}
---

# Tags
- [[03 - Tags/Ultimate AWS Certified SysOps Administrator Associate 2024\|Ultimate AWS Certified SysOps Administrator Associate 2024]]
---
# 단서 질문
- CloudFront란?
	- Content Delivery Network로 자주 사용되는 데이터들을 엣지 단에서 캐싱하여 사용자가 더 빨리 응답을 받을 수 있도록 합니다.
- CloudFront의 Origin
    - S3
    - ALB
    - EC2 등 HTTP 기반 모든 origin
- CloudFront의 트러블 슈팅
    - 403 에러
	    - 유저가 해당 자원에 접근할 수 있는 권한이 없다.
    - 404 에러
        - 해당 자원이 존재하지 않는다.
    - 500 에러
        - 게이트웨이 이슈
- CloudFront의 Geo Restriction이란?
    - 클라우드의 접근 가능한 IP 대역을 설정을 하는 기능입니다.
    - 화이트리스트를 통해서 특정 지역 접근이 가능하게 하거나 블랙리스트를 통해서 특정 지역 접근이 불가능하게 설정할 수 있습니다.
    - 지역별 IP 대역은 3rd Party Geo Database를 사용합니다.
---
# 핵심 요약

---
# 핵심 필기

---
# 참고 자료

### 핵심 필기

- CloudFront Overview
    - Amazon CloudFront
        
        ![[AWS_Certified_SysOps_Slides_v31_page-0391.jpg\|AWS_Certified_SysOps_Slides_v31_page-0391.jpg]]
        
          
        
    - CloudFront - Origins
        
        ![[AWS_Certified_SysOps_Slides_v31_page-0392.jpg\|AWS_Certified_SysOps_Slides_v31_page-0392.jpg]]
        
        - S3 Bucket
            - 엣지 로케이션에 파일 분산을 하기 위해서 사용
            - OAC를 통해서 CloudFront를 통한 요청만 S3에 접근 가능하게 설정 가능
            - OAC는 OAI의 신버전
            - CloudFront에 파일을 업로드함으로써 S3에 파일 업로드 가능. (인그레스라고 함)
        - Custom Origin (HTTP)
            - ALB
            - EC2
            - S3 Website
            - Any HTTP backed you want
    - CloudFront at a high level
        
        ![[AWS_Certified_SysOps_Slides_v31_page-0393.jpg\|AWS_Certified_SysOps_Slides_v31_page-0393.jpg]]
        
          
        
    - CloudFront - S3 as an Origin
        
        ![[AWS_Certified_SysOps_Slides_v31_page-0394.jpg\|AWS_Certified_SysOps_Slides_v31_page-0394.jpg]]
        
          
        
    - CloudFront vs S3 Cross Region Replication
        
        ![[AWS_Certified_SysOps_Slides_v31_page-0395.jpg\|AWS_Certified_SysOps_Slides_v31_page-0395.jpg]]
        
          
        
- CloudFront - ALB as an Origin
    
    ![[AWS_Certified_SysOps_Slides_v31_page-0396.jpg\|AWS_Certified_SysOps_Slides_v31_page-0396.jpg]]
    
    - CloudFront로 연결할 리소스는 CloudFront Edge Location IP를 허용해야 한다.
- CloudFront - Geo Restriction
    
    ![[AWS_Certified_SysOps_Slides_v31_page-0397.jpg\|AWS_Certified_SysOps_Slides_v31_page-0397.jpg]]
    
      
    
- CloudFront Reports, Logs and Troubleshooting
    
    ![[AWS_Certified_SysOps_Slides_v31_page-0398.jpg\|AWS_Certified_SysOps_Slides_v31_page-0398.jpg]]
    
    - CloudFront로 가는 요청들은 S3 Bucket에 로그로 기록된다.
    
    ![[AWS_Certified_SysOps_Slides_v31_page-0399.jpg\|AWS_Certified_SysOps_Slides_v31_page-0399.jpg]]
    
    - Cache 통계
    - Popular Objects
    - Top Referrers (가장 많이 인용하는 사이트)
    - Usage Reports
    - Viewers Report
    
    ![[AWS_Certified_SysOps_Slides_v31_page-0400.jpg\|AWS_Certified_SysOps_Slides_v31_page-0400.jpg]]
    
      
    
      
    
- CloudFront Caching - Deep Dive
    - CloudFront Caching
        
        ![[AWS_Certified_SysOps_Slides_v31_page-0401.jpg\|AWS_Certified_SysOps_Slides_v31_page-0401.jpg]]
        
          
        
    - CloudFront Cache Behavior for Headers
        
        ![[AWS_Certified_SysOps_Slides_v31_page-0402.jpg\|AWS_Certified_SysOps_Slides_v31_page-0402.jpg]]
        
          
        
    - CloudFront Caching - Whitelist Headers
        
        ![[AWS_Certified_SysOps_Slides_v31_page-0403.jpg\|AWS_Certified_SysOps_Slides_v31_page-0403.jpg]]
        
          
        
          
        
    - **CloudFront Origin Headers vs Cache Behavior(시험 중요 포인트)**
        
        ![[AWS_Certified_SysOps_Slides_v31_page-0404.jpg\|AWS_Certified_SysOps_Slides_v31_page-0404.jpg]]
        
          
        
    - CloudFront Caching TTL
        
        ![[AWS_Certified_SysOps_Slides_v31_page-0405.jpg\|AWS_Certified_SysOps_Slides_v31_page-0405.jpg]]
        
          
        
    - CloudFront Cache Behavior for Cookies
        
        ![[AWS_Certified_SysOps_Slides_v31_page-0406.jpg\|AWS_Certified_SysOps_Slides_v31_page-0406.jpg]]
        
          
        
          
        
    - CloudFront Caching - Cookies
        
        ![[AWS_Certified_SysOps_Slides_v31_page-0407.jpg\|AWS_Certified_SysOps_Slides_v31_page-0407.jpg]]
        
          
        
    - CloudFront Cache Behavior for Query Strings
        
        ![[AWS_Certified_SysOps_Slides_v31_page-0408.jpg\|AWS_Certified_SysOps_Slides_v31_page-0408.jpg]]
        
          
        
    - CloudFront Caching - Query String Parameters
        
        ![[AWS_Certified_SysOps_Slides_v31_page-0409.jpg\|AWS_Certified_SysOps_Slides_v31_page-0409.jpg]]
        
          
        
          
        
    - CloudFront - Maximize cache hits by separating static and dynamic distributions
        
        ![[AWS_Certified_SysOps_Slides_v31_page-0410.jpg\|AWS_Certified_SysOps_Slides_v31_page-0410.jpg]]
        
          
        
    - CloudFront - Increasing Cache Ratio
        
        ![[AWS_Certified_SysOps_Slides_v31_page-0411.jpg\|AWS_Certified_SysOps_Slides_v31_page-0411.jpg]]
        
          
    
- CloudFront with ALB Sticky Sessions
	- CloudFront with ALB Sticky Sessions