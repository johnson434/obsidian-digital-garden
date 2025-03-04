---
{"dg-publish":true,"permalink":"/06-full-notes/linux-kernel-architecture/","noteIcon":""}
---

# Tags
- [[03 - Tags/Linux\|Linux]]
---
# 핵심 요약
- 리눅스의 전반적인 아키텍처를 다룬다.
- 커널에서 arch는 device specific code가 작성되어 있다.
- CPU가 어떤 ISA를 사용하냐에 따라 사용할 코드가 달라진다.
- 하드웨어 -> 드라이버 장치 -> 커널 -> 시스템호출 인터페이스 -> 유저 모드 프로세스
- VFS는 Virtual Filesystem Switch로 추상화된 File과 실제 기억장치를 매핑하는 역할을 한다.
- VFS에서 File -> Dentry(Directory entry) -> INode로 이어진다.
- File은 파일의 정보를 담고 있다.
- INode는 실제 블록의 주소를 담고 있다.
---
# 핵심 필기
### Linux Kernel architecture
![Pasted image 20250217082136.png](/img/user/image/Pasted%20image%2020250217082136.png)
### arch
- 특정 아키텍처 코드(specific architecture code)
- 기계 특정 코드(device specific code)로 더 세분화 될 수 있다.
- 부트로더와 아키텍처별 초기화의 인터페이스
- 인터럽트 컨트롤러, SMP 컨트롤러, BUS 컨트롤러, 예외 및 인터럽트 설정, 가상 메모리 처리와 같은 아키텍처 또는 머신 특정의 다양한 하드웨어 비트에 대한 접근한다.
- 아키텍처에 최적화된 함수들 (e.g. memcpy, string operations, etc)
리눅스 커널에서 특정 아키텍처를 타겟하는 코드가 작성되어 있으며 특정 머신 기반 코드나 아키텍처 기반 코드로 나뉠 수 있음. (e.g. arm)
![Pasted image 20250217083311.png](/img/user/image/Pasted%20image%2020250217083311.png)
### Device drivers
시스템의 내부 데이터 구조와 상태를 반영하기 위해서 리눅스 커널은 **통합된 장치 모델(unified device model)**을 사용한다.
내부 데이터엔 어떤 데이터가 존재하며 어떤 상태인지 그리고 어떤 버스(bus)나 드라이버가 attached 상태인지 등이 있다. 
이러한 정보들은 시스템 전원 관리(system wide poser management)에 필수적이며 디바이스 디스커버리(device discovery)나 동적 장치 제거(dynamic device removal) 구현에도 필요합니다.
각, 서브시스템엔 특화된 드라이버 인터페이스가 존재하며 특정 하드웨어와 동작하도록 만들어졌습니다. 
해당 드라이버 인터페이스를 통해서 쉽게 드라이버를 작성할 수 있습니다. (인터페이스에서 정의가 되어 있으므로 인터페이스를 호출하면 해당 하드웨어 특화된 코드가 동작한다. 따라서, 더욱 쉽게 개발이 가능하단 뜻)
리눅스는 다양한 종류의 장치 드라이버 유형을 지원합니다. 예로는 TTY, 직렬, SCSI, 파일 시스템, 이더넷, USB, 프레임버퍼, 입력, 사운드 등이 있습니다.
### Process Management
리눅스 메모리 서브시스템은 다음을 처리합니다.
1. 물리적 메모리 관리: 실제 하드웨어에 메모리를 할당하거나 해제하는 것. (allocating and freeing)
2. 가상 메모리 관리: 페이징, 스와핑, 페이징 요청, copy on write
3. 유저 서비스: 유저 주소 공간 관리 (사용자의 프로세스가 사용하는 공간 관리하는 기능. e.g. mmap(), brk(), 공유 메모리)
4. 커널 서비스: SL\*B allocators, vmalloc
### Block I/O Management
리눅스 Block I/O 서브시스템은 block 장치(SSD, HDD)에 읽기/쓰기를 처리합니다.
block I/O 요청을 만들거나, block I/O 요청을 변환(e.g. 소프트웨어 RAID나 LVM), 요청을 합치거나 정렬하거나 I/O 스케쥴러로 요청을 보내서 block device 드라이버가 이를 처리하도록 합니다.
![Pasted image 20250217085150.png](/img/user/image/Pasted%20image%2020250217085150.png)
### Virtual Filesystem Switch(VFS)
리눅스 가상 파일시스템 스위치는 파일시스템 코드 중복을 피하기 위해서 일반적인 파일시스템 코드를 구현합니다. 
리눅스 가상 파일시스템 스위치는 다음과 같은 추상화를 지원합니다.
- inode: 디스크에  위치한 파일을 설명합니다. (attributes, location of data blocks on disk)
- dentry: inode와 이름을 링크합니다.
- file: 열려있는 파일의 속성을 설명합니다. (e.g. file pointer)
- superblock: 형식화된 파일시스템의 속성을 설명합니다. (e.g. 블록 개수, 블록 크기, 디스크에 루트 디렉터리 위치, 암호화, etc)
![Pasted image 20250217085645.png](/img/user/image/Pasted%20image%2020250217085645.png)
리눅스 VFS는 또한 다음과 같은 캐싱 기법을 구현합니다.
- the inode cache: 파일 속성과 메타데이터를 캐싱
- the dentry cache: 파일시스템의 디렉토리 계층을 캐싱
- the page cache: 파일데이터의 블록을 메모리에 캐싱
### 네트워크 스택
![Pasted image 20250217085914.png](/img/user/image/Pasted%20image%2020250217085914.png)
### 리눅스 보안 모듈
- 리눅스 기본 보안 모듈을 확장하기 위한 훅들(Hooks)
- 여러 리눅스 보안 익스텐션에서 사용된다.
	- Security Enhanced Linux
	- AppArmor
	- Tomoyo
	- Smack
---
# 참고 자료
https://linux-kernel-labs.github.io/refs/heads/master/so2/lec1-intro.html#linux-kernel-architecture