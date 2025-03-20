---
{"dg-publish":true,"permalink":"/linux-virt-res-memory-commit/","noteIcon":""}
---


# Tags
- [[Linux Process\|Linux Process]]

## VIRT, RES, Memory Commit
### VIRT
- 프로세스가 할당 받는 가상 메모리 영역으로 `memory commit`을 통해서 주소를 받지만 모든 가상 메모리가 물리적 메모리에 할당되진 않은 상태다.
### RES
- 실제 메모리에 올라간 메모리에 총량이다.
- 운영체제에서 커널은 프로세스의 메모리를 바로 물리적 메모리에 올리지 않고 사용되는 메모리만 물리적 메모리에 할당한다.
- 이후에 물리적 메모리에 올라가지 않은 가상 메모리 주소가 호출되면 페이지 폴트가 일어나고, 보조기억장치로부터 해당 메모리를 호출한다.
### Memory commit
- 가상 메모리 할당을 `Memory commit`이라고 말한다.
- 프로세스가 실행되면 가상 메모리 주소를 커밋(memory commit)한다.
- vm.overcommit_memory 정책 설정에 따라 가상 메모리가 실제 메모리 영역을 얼만큼 초과할 수 있을지 설정할 수 있다.
	- 예:
		- 0(기본값): 0으로 설정하면 swap 영역 + page cache + slab reclaimable 영역의 총합보다 작은 값만큼 가상 메모리 영역으로 할당 가능하다.
		- 1: 무조건 가상메모리를 할당한다.
		- 2: 제한적으로 할당한다. swap 영역의 크기를 토대로 계산한다.
#### 프로세스의 상태
top의 man 페이지
![Pasted image 20250313140841.png](/img/user/image/Pasted%20image%2020250313140841.png)
- D: uninterruptible sleep 상태로, 디스크나 네트워크 I/O를 대기하고 있는 프로세스다. Wait Queue에서 대기하다가 Run Queue로 이동한다.
- R: 실제로 CPU 자원을 소모하는 중인 프로세스다.
- S: interruptible sleeping 상태의 프로세스로, D와 달리 시그널을 받으면 처리할 수 있다.
- T: traced or stopped 상태의 프로세스로, strace 등으로 프로세스의 시스템 콜을 추적하는 상태를 보여준다.
- Z: zombie 상태로 부모 프로세스가 죽은 경우를 말한다. 실제로 시스템의 자원을 점유하진 않는다. 그러나, PID는 점유하므로 너무 많아진다면 PID 고갈로 이어질 수 있다.