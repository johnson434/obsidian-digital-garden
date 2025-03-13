---
{"dg-publish":true,"permalink":"/06-full-notes/linux-address-space/","noteIcon":""}
---

# Tags
- [[03 - Tags/Linux Concept\|Linux Concept]]
---
# 핵심 요약
## AI 요약
### ✅ **최종 정리 (수정 후 버전)**

### **1️⃣ 물리적 주소 공간 (Physical Address Space)**

- 물리적 주소 공간은 **RAM, GPU 메모리, I/O 장치 등이 실제 물리 메모리 주소에서 어떻게 배치되는지를 의미**한다.
- 32비트 시스템에서는 **최대 4GB (2³²)**의 물리 주소 공간을 가질 수 있으며, RAM이 낮은 주소부터 할당되고, 그래픽 카드 메모리(GART)나 기타 장치는 높은 주소를 차지한다.
- 64비트 시스템에서는 물리적 주소 공간이 더 넓어지고, 주소 매핑 방식이 달라질 수 있다.

---

### **2️⃣ 가상 주소 공간 (Virtual Address Space)**

- 가상 주소 공간은 CPU가 페이징을 통해 메모리를 논리적으로 바라보는 방식이다.
- 보통 **Address Space**라고 하면 **가상 주소 공간을 의미**한다.
- **운영체제 커널이 관리하며, 각 프로세스마다 독립적으로 할당된다.**

---

### **3️⃣ 프로세스 주소 공간 (Process Address Space) vs 커널 주소 공간 (Kernel Address Space)**

- **Process Address Space**:
    - 각 프로세스가 가지는 독립적인 가상 주소 공간.
    - 0부터 시작하며 연속적인 주소를 가짐.
- **Kernel Address Space**:
    - 운영체제 커널이 실행되는 공간.
    - 모든 프로세스가 공유하지만, 유저 모드에서는 접근 불가능.

---

### **4️⃣ User와 Kernel이 Virtual Address Space를 공유하는 방식**

- 가상 주소 공간에서는 **각 프로세스마다 User Space가 개별적으로 존재하며, Kernel Space는 모든 프로세스가 공유한다.**
- **32비트 시스템에서는 3:1 비율로 분할됨.**
    - **User Space: 0x00000000 ~ 0xBFFFFFFF (3GB) → 개별적**
    - **Kernel Space: 0xC0000000 ~ 0xFFFFFFFF (1GB) → 모든 프로세스 공유**
- **64비트 시스템에서는 비율이 다를 수 있으며, 가상 주소 공간이 훨씬 넓어짐.**
# 핵심 필기
## Address space
물리적 주소 공간은 RAM, GPU 메모리, 기타 I/O 장치 등이 물리적인 메모리 주소 공간에서 어떻게 배치되는지를 의미한다.
예를 들어, 32비트 인텔 아키텍처에선 RAM이 낮은 물리적 주소 공간을 차지하고 그래픽 카드 메모리는 높은 메모리 주소를 차지합니다.
![Pasted image 20250213125635.png](/img/user/image/Pasted%20image%2020250213125635.png)
![gart.webp](/img/user/image/gart.webp)
- **가상 주소 공간(Virtual Address Space)**은 CPU가 페이징(Paging)과 같은 메모리 관리 기법을 통해 논리적으로 메모리를 바라보는 방식을 의미한다.
- 보통 address space를 말하면 이 가상 메모리 공간을 말한다.
- 이 가상 주소 공간은 커널이 책임진다.

- 가상 주소 공간과 관련해서 **process(address) space**와 **kernel (address) space**라는 용어도 자주 쓰인다.

- **프로세스 공간(Process Address Space)**은 **각 프로세스에 할당된 독립적인 가상 주소 공간을 의미한다.**
- 0부터 시작하며 연속적인 주소를 가진다. 
![Pasted image 20250213130505.png](/img/user/image/Pasted%20image%2020250213130505.png)

**커널 공간(Kernel Space)**은 **운영체제 커널이 실행되는 가상 주소 공간으로, 모든 프로세스가 공유한다.**

## User and kernel sharing the virtual address space
![Pasted image 20250213135016.png](/img/user/image/Pasted%20image%2020250213135016.png)
가상 주소 공간에서는 프로세스마다 User Space가 개별적으로 존재하고, Kernel Space는 모든 프로세스가 공유한다.
Kernel space는 모든 프로세스의 가상 주소 공간이 공유하지만 User space는 각 프로세스 별로 존재한다. 

![Pasted image 20250213133854.png](/img/user/image/Pasted%20image%2020250213133854.png)
위 그림은 32bit 4GB 가상 주소 공간을 가정한 것이다. 전통적으로 3:1의 비율(유저공간:커널공간)로 분할
# 참고 자료
https://linux-kernel-labs.github.io/refs/heads/master/so2/lec1-intro.html#typical-operating-system-architecture
https://brewagebear.github.io/linux-kernel-internal-2/