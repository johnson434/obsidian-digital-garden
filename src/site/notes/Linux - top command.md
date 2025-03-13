---
{"dg-publish":true,"permalink":"/linux-top-command/","noteIcon":""}
---

# Tags
- [[Linux command\|Linux command]]
- [[Linux Process\|Linux Process]]
---
![Pasted image 20250313113122.png](/img/user/Pasted%20image%2020250313113122.png)
- `up 2:27`: 서버가 구동된 시간으로 현재 2시간 27분
- `3 users`: 현재 로그인한 유저
- `load average`: 현 시점 코어에서 실행 중인 프로세스 개수
- `Tasks: `: 전체 프로세스 개수와 상태
- `PR`: 우선순위
- `NI`: NICE 값으로 우선순위 가중치
- `VIRT`: 가상 메모리 할당 총량
- `RES`: 물리 메모리 할당 총량
- `SHR`: 공유 메모리[^1]
	- 예: 라이브러리[^2]
- `S`: 프로세스 상태

[^1]: 다른 프로세스와 공유하는 shared memory의 양

[^2]: 대부분의 리눅스 프로세스는 glibc를 사용. 이를 공유 메모리에 올려서 사용한다.
