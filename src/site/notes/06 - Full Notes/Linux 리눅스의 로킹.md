---
{"dg-publish":true,"permalink":"/06-full-notes/linux/","noteIcon":""}
---

[[03 - Tags/Linux\|Linux]]
# rsyslog 로깅 데몬
리눅스는 syslogd라는 데몬을 통해 컴퓨터의 이벤트를 저장한다.
## rsyslog.conf
rsyslog 로깅 룰을 설정하는 환경 파일
로깅의 룰은 아래와 같은 형식으로 설정한다.
**facility**.**priority** **action** : facility 프로그램의 priority 우선도를 가진 메시지를 action에 저장한다.

**facility**
- **auth, authpriv** : 보안/인증 메시지
- **cron :** 시간 데몬
- **daemon :**기타 데몬
- **kern** : 커널 메시지
- **lpr** : 프린트 시스템
- **mail** : 메일 시스템
- **user** : 일반 사용자 수준 메시지

**priority** : 메시지의 우선 순위(debug ~ emerg)
- debug
- info
- error
- alert
## logrotate를 통한 로그 자동 정리
밑에 파일은 주마다 로그 파일을 만든다.
```Shell
# see "man logrotate" for details

# global options do not affect preceding include directives

# rotate log files weekly
# 로그 순환 단위를 설정
weekly

# use the adm group by default, since this is the owning group
# of /var/log/syslog.
su root adm

# keep 4 weeks worth of backlogs
# 로그를 위한 주기 여기선 4주가 된다.
rotate 4

# create new (empty) log files after rotating old ones
create

# use date as a suffix of the rotated file
\#dateext

# uncomment this if you want your log files compressed
\#compress

# packages drop log rotation information into this directory
include /etc/logrotate.d

# system-specific logs may also be configured here.
```

## 로그 비활성화 및 침임 흔적 제거하기

리눅스 시스템을 취한 후에 침입했던 흔적을 삭제하기 위해 사용되는 방**법**

1. **흔적 삭제**
    1. 내가 사용한 로그만 삭제하기 : 시간이 비어서 의심을 살 수 있다.
    2. 파일을 삭제 : 포렌식으로 복구 가능. 삭제된 파일은 파일 시스템이 덮어씌우기 전까지는 존재한다.
    3. 파쇄(shred) : 파일을 삭제하고 여러 번 덮어쓴다. 
        ```Shell
        shred --help
        -s [삭제할 크기]: 명시한 파일의 크기만큼만 shred 한다.
        -n [shred 반복 횟수]
        -f [권한이 필요하면 해당 권한으로 로그인]
        -z [shred가 끝날 때, 0비트로 파일을 덮어쓴다]
        
        hwang@hwang:~$ cat hello_text.txt 
        abcdefghijklmnop
				
        hwang@hwang:~$ xxd -b hello_text.txt 
        00000000: 01100001 01100010 01100011 01100100 01100101 01100110  abcdef
        00000006: 01100111 01101000 01101001 01101010 01101011 01101100  ghijkl
        0000000c: 01101101 01101110 01101111 01110000 00001010           mnop.
        
        hwang@hwang:~$ shred -f -s 5 hello_text.txt 
        
        hwang@hwang:~$ xxd -b hello_text.txt
        00000000: 10111110 00001100 01110001 01000011 01101110 01100110  ..qCnf
        00000006: 01100111 01101000 01101001 01101010 01101011 01101100  ghijkl
        0000000c: 01101101 01101110 01101111 01110000 00001010           mnop.
        
        hwang@hwang:~$ shred -f -s 5 -z hello_text.txt 
        hwang@hwang:~$ xxd -b hello_text.txt 
        00000000: 00000000 00000000 00000000 00000000 00000000 01100110  .....f
        00000006: 01100111 01101000 01101001 01101010 01101011 01101100  ghijkl
        0000000c: 01101101 01101110 01101111 01110000 00001010           mnop.
        
        hwang@hwang:~$ shred -f -s 16 -z hello_text.txt 
        hwang@hwang:~$ xxd -b hello_text.txt 
        00000000: 00000000 00000000 00000000 00000000 00000000 00000000  ......
        00000006: 00000000 00000000 00000000 00000000 00000000 00000000  ......
        0000000c: 00000000 00000000 00000000 00000000 00001010           .....
        ```
        
2. 로깅 비활성화
    1. rsyslog 데몬 중단
        1. **service [서비스명] start|stop|restart** : 서비스를 조정한다.
            
            ```Shell
            service rsyslog stop
            # systemctl : 서비스 컨트롤 및 목록 조회하는 명령어
            systemctl list-units --type=service | grep rsys
            service status rsyslog.service
            service rsyslog stop
            
            hwang@hwang:~$ systemctl stop rsyslog.service & systemctl stop syslog.socket 
            [1] 34262
            hwang@hwang:~$ systemctl status rsyslog.service 
            ○ rsyslog.service - System Logging Service
                 Loaded: loaded (/lib/systemd/system/rsyslog.service; enabled; vendor preset: enabled)
                 Active: inactive (dead) since Thu 2024-03-07 15:27:07 KST; 10s ago
            TriggeredBy: ○ syslog.socket
                   Docs: man:rsyslogd(8)
                         man:rsyslog.conf(5)
                         https://www.rsyslog.com/doc/
                Process: 34077 ExecStart=/usr/sbin/rsyslogd -n -iNONE (code=exited, status=0/SUCCESS)
               Main PID: 34077 (code=exited, status=0/SUCCESS)
                    CPU: 8mss
            
             3월 07 15:25:13 hwang systemd[1]: Starting System Logging Service...
             3월 07 15:25:13 hwang rsyslogd[34077]: imuxsock: Acquired UNIX socket '/run/systemd/journal/syslog' (fd>
             3월 07 15:25:13 hwang rsyslogd[34077]: rsyslogd's groupid changed to 111
             3월 07 15:25:13 hwang systemd[1]: Started System Logging Service.
             3월 07 15:25:13 hwang rsyslogd[34077]: rsyslogd's userid changed to 104
             3월 07 15:25:13 hwang rsyslogd[34077]: [origin software="rsyslogd" swVersion="8.2112.0" x-pid="34077" x>
             3월 07 15:27:07 hwang systemd[1]: Stopping System Logging Service...
             3월 07 15:27:07 hwang rsyslogd[34077]: [origin software="rsyslogd" swVersion="8.2112.0" x-pid="34077" x>
             3월 07 15:27:07 hwang systemd[1]: rsyslog.service: Deactivated successfully.
             3월 07 15:27:07 hwang systemd[1]: Stopped System Logging Service.
            
            ```