---
{"dg-publish":true,"permalink":"/06-full-notes/linux-chown-chgrp/","noteIcon":""}
---

[[03 - Tags/Linux\|Linux]] [[03 - Tags/Linux CLI\|Linux CLI]]
# 권한
**r(read)** : 열거나 보기만 가능한 권한
**w(write)** : 수정 가능한 권한
**x(execute)** : 실행 가능한 권한(스크립트나 프로그램)

# 소유권
파일을 생성한 사람이 `owner`가 되고, 생성한 사람의 그룹이 소유 그룹이 된다.
## 사용자에게 소유권 승인
**chown(change owner)** : 사용자의 소유권을 승인
## 그룹에 소유권 승인
**chgrp(change group)** : 그룹의 소유권을 승인

# 권한 확인하기
```Bash
total 48
맨 앞글자는 파일의 종류(directory = d, file = -)
다음부터는 소유권자(user 사용자), 그룹(group), other(기타)
숫자는 링크 개수
kali는 파일 소유자
byte 크기
파일 생성 혹은 수정 시기
파일 이름
-rw-r--r-- 1 kali kali   49 Feb 29 17:28 :
drwxr-xr-x 2 kali kali 4096 Feb 26 04:54 Desktop
drwxr-xr-x 2 kali kali 4096 Feb 26 04:54 Documents
drwxr-xr-x 2 kali kali 4096 Feb 26 04:54 Downloads
-rw-r--r-- 1 kali kali   19 Feb 29 17:30 ifconfig_result
drwxr-xr-x 2 kali kali 4096 Feb 26 04:54 Music
drwxr-xr-x 2 kali kali 4096 Feb 26 04:54 Pictures
drwxr-xr-x 2 kali kali 4096 Feb 26 04:54 Public
drwxr-xr-x 2 kali kali 4096 Feb 26 04:54 Templates
drwxr-xr-x 2 kali kali 4096 Feb 26 04:54 Videos
```
# 권한 변경
**chmod(Change Mode)** : 권한 변경
```Bash
# 숫자로 권한 표현하기
# rwx를 각각 1비트 총 3비트로 표현한다. 권한이 부여되면 1 부여되지 않으면 0이다.
chmod 774 hi.txt
# 위에 774는 소유자, 그룹, 사용자의 권한을 3개 십진수로 표현한 것이다.
7(4 + 2 + 1) = 0b111 = 소유자 권한 rwx
7(4 + 2 + 1) = 0b111 = 그룹 권한 rwx
4(4 + 0 + 0) = 0b100 = 사용자 권한 r

# UGO(User Group Other) 단순하게 권한을 기호로 표현하는 방법
- : 권한 제거
+ : 권한 추가
= : 권한 설정
chmod u-w hi.txt # 사용자의 쓰기 권한 제거
# 동시에 여러 권한 수정하기
chmod u-w, g+w hi.txt
```
# umask(사용자 마스크)를 통한 권한 제거
기본적으로 리눅스는 파일에 666(읽기, 쓰기), 디렉터리에 777(읽기, 쓰기, 실행) 권한을 부여한다.
여기서 umask를 통해 기본적으로 권한을 제거할 수 있다.
만약, umask의 값이 022면 666-022 = 644 권한이 부여된다.
umask는 각 사용자별로 설정이 가능하다.
```Shell
cat /home/hwang/.profile |grep umask

# the default umask is set in /etc/profile; for setting the umask
# for ssh logins, install and configure the libpam-umask package.
#umask 022

# 디렉터리의 생성했을 때, 기본 권한은 777
# 여기서, umask가 적용 -> 기본 권한 - umask -> 755 -> drwxr-xr-x

# 파일 생성 시, 기본 권한은 666
# 여기서, umask가 적용 -> 기본 권한 - umask -> 644 -> -rw-r--r--
```
# 특수 권한
## SUID
SUID를 부여하면 해당 파일을 소유자 권한으로 실행이 가능하다.
```Bash
chmod u+s [파일이름]
chmod 4777 [파일이름] # suid + 777권한이란 뜻

ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 72344 Oct 15 13:10 /usr/bin/passwd
# passwd를 실행하면 root 권한으로 실행된다.
```
## SGID
SGID를 부여하면 해당 파일의 그룹 권한으로 실행이 가능하다.
```Bash
chmod g+s [파일이름]
chmod 2777 [파일이름] # sgid + 777 권한이란 뜻
ls -l /usr/bin/passwd
-rwsr-xr-x 1 root 소유그룹 72344 Oct 15 13:10 /usr/bin/passwd
# 소유 그룹으로 실행된다.
```
## Sticky Bit
디렉터리에 설정할 수 있는 권한 비트로 해당 디렉터리 내의 파일을 삭제하거나 이름을 바꿀 수 있게 허용한다.
이는 오래된 유닉스 시스템에서 사용하는 것으로 현대의 시스템(리눅스 같은)에선 사용 안한다.
# SUID 설정된 파일 찾기
```Bash
find / -user root -perm -4000
cd /usr/bin & ls -l | grep su
-rwsr-xr-x 1 root root         76096 Nov 13 06:48 su
# sudo를 사용하면 root 권한으로 실행된다.
-rwsr-xr-x 1 root root        281912 Jul 19  2023 sudo
```