---
{"dg-publish":true,"permalink":"/06-full-notes/linux-sda-vs-hda-vs-vda/","noteIcon":""}
---

# Tags
- [[03 - Tags/Linux\|Linux]]
- [[03 - Tags/DevOps\|DevOps]]
---
# 핵심 필기

### ✅ **1. 저장 장치 명칭이 결정되는 방식**

Linux에서 디스크 이름(`sda`, `hda`, `nvme0n1`)은 **컨트롤러 타입에 따라 자동으로 지정**됩니다.

| 장치 이름            | 인터페이스(Controller)            | 설명                    |
| ---------------- | ---------------------------- | --------------------- |
| **`hda`, `hdb`** | **IDE (PATA, Parallel ATA)** | 오래된 HDD에서 사용됨 (구형)    |
| **`sda`, `sdb`** | **SCSI / SATA / SAS / USB**  | 현대적인 HDD, SSD, 외장 디스크 |
| **`nvme0n1`**    | **NVMe (PCIe)**              | 최신 NVMe SSD에서 사용됨     |
| `vda`            | **VirtIO 가상 디스크**            | 가상 머신에서 사용            |
