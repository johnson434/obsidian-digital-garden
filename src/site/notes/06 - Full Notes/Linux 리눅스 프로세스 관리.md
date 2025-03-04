---
{"dg-publish":true,"permalink":"/06-full-notes/linux/","noteIcon":""}
---

[[03 - Tags/Linux\|Linux]] [[03 - Tags/Linux CLI\|Linux CLI]]
# 프로세스 확인
```Bash
# 현재 사용자가 실행한 프로세스와 현재 터미널에서 실행 중인 프로세스를 출력
$ ps
	 PID TTY          TIME CMD
   6807 pts/0    00:00:00 bash
   6827 pts/0    00:00:00 ps

# 모든 사용자에 대해 실행 중인 프로세스를 보여준다.
$ ps aux | head -n 3
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.0 167080  8832 ?        Ss   11:36   0:01 /sbin/init splash
root           2  0.0  0.0      0     0 ?        S    11:36   0:00 [kthreadd]

# User: 프로세스를 호출한 사람
# CPU : 프로세스가 사용 중인 CPU의 백분율
# MEM : 프로세스가 사용 중인 메모리의 백분율
# COMMAND : 프로세스를 시작한 명령의 이름

# 리소스 사용량이 많은 프로세스 순으로 정렬
# 3초마다 목록이 새로고침된다.
top
```

# 프로세스 관리

**nice** : 프로세스에게 얼마나 친절한지 설정. 우선 순위를 설정한 후에 프로세스를 실행한다.(스케쥴링 우선도)
**renice** : 현재 **실행 중인 프로세스**의 우선 순위를 변경
```Bash
# 프로세스 시작 시, 우선 순위 변경
# -20 <= 우선 순위 <= 19 default : 0
# 우선 순위가 낮을수록 우선 순위가 높다.
nice -n [우선 순위] [프로세스]
nice -n 10 /usr/bin/bash
ps ecl

F   UID     PID    PPID PRI  NI    VSZ   RSS WCHAN  STAT TTY        TIME COMMAND
4  1000    2698    2578  20   0 163700  4608 do_pol Ssl+ tty2       0:00 gdm-way
0  1000    2705    2698  20   0 224352 12544 do_pol Sl+  tty2       0:00 gnome-s
0  1000    6195    5378  20   0  12724  4608 do_wai Ss   pts/0      0:00 bash
0  1000    6226    6195  30  10  12592  4352 do_wai SN   pts/0      0:00 bash
4  1000    6232    6226  30  10  13984  2304 -      RN+  pts/0      0:00 ps

# 기본 우선 순위가 20인데 거기에 +10을 더한 30짜리 bash가 실행됨.

# 실행 중인 프로세스의 우선 순위 변경
renice [우선 순위] [PID]
renice -19 6226
6226 (process ID) old priority 10, new priority -19
hwang@hwang:~$ ps ecl
F   UID     PID    PPID PRI  NI    VSZ   RSS WCHAN  STAT TTY        TIME COMMAND
4  1000    2698    2578  20   0 163700  4608 do_pol Ssl+ tty2       0:00 gdm-way
0  1000    2705    2698  20   0 224352 12544 do_pol Sl+  tty2       0:00 gnome-s
0  1000    6195    5378  20   0  12724  4608 do_wai Ss   pts/0      0:00 bash
0  1000    6226    6195   1 -19  12592  4352 do_wai S<   pts/0      0:00 bash
4  1000    6257    6226   1 -19  13984  2304 -      R<+  pts/0      0:00 ps
```
# 프로세스 종료
비정상적인 동작을 하거나 멈추는 프로세스를 **로그(rogue) 프로세스(돚거 프로세스)**라고 한다.
이를 `kill` 또는 `killall` 커맨드로 종료가 가능하다.
```Bash
kill -[시그널 번호|시그널 명] [프로세스 아이디] # 시그널 번호를 제공하지 않으면 SIGTERM이 기본 시그널
# 시그널 종류/번호/설명
SIGHUP/1/멈춰있는(HUP:hangup) 시그널이라 한다. 지정된 프로세스를 중단하고 동일한 PID로 재시작한다.
SIGINT/2/인터럽트(INT) 시그널이다. 작업이 보장되지 않는 약한 시그널이지만 대부분 작동한다.
SIGQUIT/3/코어 덤프라고 한다. 프로세스 종료 후 작업 내용을 메모리에 저장한 후에 작업 디렉터리의 core라는 파일에 저장한다.
SIGTERM/15/종료 시그널이다. kill 명령의 기본 시그널
SIGKILL/9/절대적인 종료 시그널이다. 프로세스의 리소스를 특수 장치인 /dev/null로 보내 프로세스를 강제로 중지한다.

kill -1 5362
kill -SIGHUP 5362
```
# 백그라운드에서 프로세스 실행
기본적으로 프로세스는 셸 내에서 작업한다.
```Bash
command &
# ex
vim hi &
```
# 포그라운드로 프로세스 이동
```Bash
fg [pid]
```
# 프로세스 예약
```JavaScript
at [시간대] # 한번만 실행
crond # 주기적으로 실행
```