---
{"dg-publish":true,"permalink":"/06-full-notes/network/","noteIcon":""}
---

[[03 - Tags/Network\|03 - Tags/Network]]
# ifconfig
**ifconfig** : 활성 네트워크 연결을 조회
![Untitled 3.png](/img/user/image/Untitled%203.png)
**inet** : 해당 네트워크 인터페이스가 사용하는 IP 주소
**broadcast** : 해당 네트워크 인터페이스의 서브넷의 모든 IP로 데이터를 전달하기 위해 사용되는 주소.
**eth(Ethernet)** : 첫 유선 네트워크 연결을 의미한다.
**lo(loopback address, localhost)** : 컴퓨터가 자기 자신과 네트워킹하기 위해 사용되는 주소.
**wlan(Wireless Local Area Network)** : 무선 네트워크 인터페이스
**wlo** : 리눅스 네이밍룰을 따른 wlan 네트워크 인터페이스

## IP 주소 변경
```Bash
ifconfig wlo1 172.30.1.6
```
## 네트워크 마스크와 브로드캐스트 주소 변경
```Bash
ifconfig wlo1 172.30.1.6 netmask 255.255.0.0 broadcast 192.168.1.255
```
## MAC 주소 변경
```Bash
ifconfig 인터페이스명 down
ifconfig 인터페이스명 hw ether '바꿀 주소'
ifconfig 인터페이스명 up
```
> [!important]  
> MAC : 네트워크 인터페이스에 할당된 물리적 고유 식별 주소이다.  
# iwconfig
어댑터의 IP 주소, MAC 주소, 모드 등의 정보를 얻을 수 있다.
```Bash
iwconfig
```

![Untitled 1 2.png](/img/user/image/Untitled%201%202.png)
**무선 표준** : IEEE 802.11
**무선 확장 모드** : Managed | Monitor | Promiscuous(무차별) → 무선 비밀번호를 크래킹하기 위해서는 Promiscuous가 필요하다.
**AP** : 라우터 장치의 MAC 주소
**TX/RX** : 송신, 수신