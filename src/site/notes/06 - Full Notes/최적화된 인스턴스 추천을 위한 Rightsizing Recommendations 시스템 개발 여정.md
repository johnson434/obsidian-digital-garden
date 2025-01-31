---
{"dg-publish":true,"permalink":"/06-full-notes/rightsizing-recommendations/","noteIcon":""}
---

# Tags
- [[03 - Tags/AWS\|AWS]]

---
## 리소스 절약을 위한 시작점
- 리소스 낭비 지점을 찾아야한다.
- 낭비 지점을 찾기 위해서 필요한 건 뭘까?
	- 모니터링 시스템
	- 수집된 데이터를 시각화 (GrafanaQL 등)
	- 관측된 매트릭스를 이해할 수 있는 능력
## 리소스 절약 알고리즘
- CPU 최적화 알고리즘
$$ CPU=CpuPeakUsage + (BufferPercentage * CpuPeakUsage)$$
$$ CPU = CpuPeakUsage + (1 + BufferPercentage)$$
- 메모리 최적화 알고리즘
$$ Memory = MemoryPeakUsage + (BufferPercentage * Memory PeakUsage)$$
$$ Memory = MemoryPeakUsage + (1 + BufferPercentage) $$
- 우아한에선 Headroom(버퍼)을 20% 적용한 방식을 사용했음.
- AWS Compute-Optimizer에선 아래와 같이 Headroom을 정의함
	- 향후 서비스가 성장할 것이라 생각한다면 30% 적용
	- 향후 서비스가 크게 변화가 없을 것이라 보면 20% 적용
	- 서비스가 유지하거나 감소할 것이라 본다면 20%보다 낮게
---
# 참고 자료
[최적화된 인스턴스 추천을 위한 Rightsizing Recommendations 시스템 개발 여정](https://techblog.woowahan.com/19685/)
[AWS Cloud Financial Management](https://aws.amazon.com/blogs/aws-cloud-financial-management/)
[How to take advantage of Rightsizing recommendation preferences in Compute Optimizer](https://aws.amazon.com/blogs/aws-cloud-financial-management/how-to-take-advantage-of-rightsizing-recommendation-preferences-in-compute-optimizer/)