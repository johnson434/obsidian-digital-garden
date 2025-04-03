---
{"dg-publish":true,"permalink":"/06-full-notes/linux-rsyslog-logging/","noteIcon":""}
---

# Tags
- [[03 - Tags/Linux logging\|Linux logging]]
---
# 단서 질문 및 답변

### **rsyslog란?**  
- 리눅스에서 시스템 이벤트 및 애플리케이션 로그를 관리하는 **로깅 데몬**.  
- 기본적으로 `/var/log/` 디렉터리에 로그를 저장하며, **rsyslog.conf**를 통해 설정 가능.  

### **logrotate란?**  
- 로그 파일의 크기 증가를 방지하고, 일정 기간마다 자동으로 로그를 순환(삭제/압축)하는 관리 도구.  
- `/etc/logrotate.conf` 및 `/etc/logrotate.d/` 디렉터리에서 설정 가능.  

### **로깅을 비활성화하고 로그를 삭제하는 방법은?**  
- **rsyslog 서비스 중단**: `systemctl stop rsyslog` 또는 `service rsyslog stop`  
- **로그 파일 삭제**: `rm -rf /var/log/*`  
- **파일 완전 삭제(복구 방지)**: `shred -u /var/log/auth.log`  

---
# 핵심 요약
- **rsyslog**는 리눅스의 **로깅 시스템**으로, `/var/log/` 디렉터리에 이벤트 및 애플리케이션 로그 저장.  
- **rsyslog.conf**에서 로깅 규칙(facility, priority, action)을 설정 가능.  
- **logrotate**를 통해 로그 파일을 일정 주기로 자동 삭제 및 압축 가능.  
- **shred 명령어**를 사용하여 로그 파일을 복구 불가능하게 삭제 가능.  
- **rsyslog 서비스를 중지하면 로그 기록이 비활성화되지만, 기존 로그는 수동으로 삭제해야 함**.  

---
# 핵심 필기

## **1. rsyslog 개요**  
### **syslog vs rsyslog**  
- **syslog**: 리눅스의 기본 로깅 시스템.  
- **rsyslog**: **syslog의 확장 버전**으로, 네트워크 로그 전송, 필터링 및 DB 저장 기능을 추가로 지원.  

### **rsyslog.conf 설정 구조**  
- `/etc/rsyslog.conf` 파일에서 로깅 규칙을 설정.  
- 기본 형식:  
  ```
  facility.priority action
  ```
  - **facility**: 로그를 생성하는 프로그램 또는 서비스.  
  - **priority**: 로그 메시지의 중요도(긴급 → 일반).  
  - **action**: 로그를 저장할 경로 또는 전송할 대상.  

### **facility 유형**  
| Facility  | 설명 |
|-----------|------|
| **auth, authpriv** | 인증/보안 관련 로그 |
| **cron** | 크론 작업 관련 로그 |
| **daemon** | 시스템 데몬 관련 로그 |
| **kern** | 커널 로그 |
| **lpr** | 프린터 시스템 로그 |
| **mail** | 메일 서비스 로그 |
| **user** | 일반 사용자 프로세스 로그 |

### **priority 우선순위**  
| Priority | 설명 |
|----------|------|
| **debug** | 디버깅 정보 |
| **info** | 일반 정보 |
| **notice** | 중요하지만 경고는 아님 |
| **warning** | 경고 메시지 |
| **error** | 오류 발생 |
| **critical** | 시스템 심각한 문제 |
| **alert** | 즉시 조치 필요 |
| **emerg** | 시스템이 사용할 수 없는 상태 |

---

## **2. logrotate를 통한 로그 자동 정리**  
- `/etc/logrotate.conf` 또는 `/etc/logrotate.d/`에서 개별 서비스 로그 관리 가능. 
### **logrotate 설정 예시**
```shell
# 로그 파일을 주 단위로 순환
weekly

# 로그 파일을 압축하지 않음
#compress

# 4주 동안 보관 후 삭제
rotate 4

# 새로운 로그 파일 자동 생성
create

# 로그 순환 시 날짜를 파일명에 포함
#dateext

# 특정 서비스의 로그 관리
include /etc/logrotate.d
```

---

## **3. 로그 비활성화 및 삭제 방법**  
### **rsyslog 서비스 중지**
```shell
# rsyslog 중지
systemctl stop rsyslog
service rsyslog stop

# rsyslog 상태 확인
systemctl status rsyslog

# rsyslog 자동 시작 방지
systemctl disable rsyslog
```

---

## **4. 로그 파일 삭제 및 복구 방지**  
### **일반적인 로그 삭제 방법**
```shell
# 특정 로그 파일 삭제
rm -rf /var/log/auth.log

# 모든 로그 삭제
rm -rf /var/log/*
```

### **shred 명령어로 로그 영구 삭제**
- 단순 삭제는 복구가 가능하므로, **shred를 사용해 데이터를 덮어쓰기 후 삭제**.  
- **shred 명령어 옵션**

| 옵션   | 설명            |
| ---- | ------------- |
| `-s` | 삭제할 크기 지정     |
| `-n` | 덮어쓰기 횟수 지정    |
| `-f` | 권한 강제 변경 후 삭제 |
| `-z` | 마지막에 0으로 덮어쓰기 |

```shell
# auth.log 파일을 3회 덮어쓴 후 삭제
shred -n 3 -z /var/log/auth.log
```

### **shred 사용 예시**
```shell
# 파일 내용 확인
cat hello_text.txt

# 파일 바이너리 확인
xxd -b hello_text.txt

# shred로 부분 덮어쓰기
shred -f -s 5 hello_text.txt
xxd -b hello_text.txt

# 전체 덮어쓰기 후 삭제
shred -f -s 16 -z hello_text.txt
xxd -b hello_text.txt
```

---

## **5. rsyslog 완전 비활성화 (침입 흔적 제거 포함)**
### **서비스 중단**
```shell
systemctl stop rsyslog
systemctl disable rsyslog
```
### **syslog.socket 중지**
```shell
systemctl stop syslog.socket
```
### **로그 삭제 및 복구 방지**
```shell
shred -u /var/log/*.log
rm -rf /var/log/*
```

### **rsyslog 서비스 상태 확인**
```shell
systemctl list-units --type=service | grep rsys
```

---

# 참고 자료
- [rsyslog 공식 문서](https://www.rsyslog.com/doc/)
- [logrotate 공식 문서](https://linux.die.net/man/8/logrotate)
- [systemd 서비스 관리](https://www.freedesktop.org/wiki/Software/systemd/)

---
# 주석
