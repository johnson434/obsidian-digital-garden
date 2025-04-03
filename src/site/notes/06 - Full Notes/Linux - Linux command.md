---
{"dg-publish":true,"permalink":"/06-full-notes/linux-linux-command/","noteIcon":""}
---

# Tags
- [[03 - Tags/Linux command\|Linux command]]
# 검색
**locate** : 전체 파일 시스템을 살펴보고 해당 단어의 모든 항목을 찾는다.
- sudo updatedb를 통해 db를 업데이트를 한 후에 검색해야함.
- 하루에 한 번만 데이터베이스가 업데이트된다.
**whereis** : 바이너리 파일을 찾는 중일 때 사용
- 바이너리의 위치뿐 아니라 이용할 수 있는 소스와 메뉴얼 페이지도 반환한다.
```Bash
whereis ls
ls: /usr/bin/ls /usr/share/man/man1/ls.1.gz
```

**which** : PATH 변수를 통해 바이너리 찾기
```Shell
which ls
/usr/bin/ls
```

**find** : 검색 디렉터리 지정 가능하고 옵션을 통해 검색 가능하다. 지정 범위와 하위 디렉터리만 검색한다.
- 단, 정확하게 이름이 일치해야 찾을 수 있다. 이를 해결하기 위해 와일드 카드를 사용한다.
- -type : 파일 유형
- -name : 파일 이름

```Bash
find /usr -type f -name ls
/usr/bin/ls
/usr/lib/klibc/bin/ls
```

**grep** : 필터링하는 역할
- 파이프라인을 통해서 이전 명령어의 출력을 입력으로 받고, 거기서 grep을 통해 필터링한다.
```Bash
ps aux | grep apache
```
# 문서 조작
**head** : 맨 앞줄 10개 출력
**tail** : 맨 뒷줄 10개 출력
```Bash
# hi.txt의 20번째줄부터 마지막줄까지 출력
tail -n+20 hi.txt
# 마지막 3줄을 출력
tail -3 hi.txt
```

**nl(number line)** : 내용과 줄 번호를 출력
**sed(stream editor)** : 특정 패턴을 찾고, 특정 행위를 한다.
```Bash
sed 명령종류/검색패턴/수정될패턴/플래그값

# s : subtitution 대체
# mysql : 검색할 내용
# MySQL : 대체될 내용
# g : 파일 전체에서 실행하라는 플래그 값
sed s/mysql/MySQL/g ./hi.txt > hi.txt
```

**more** : 한 번에 한 페이지만 출력한다.
**less** : more과 비슷한데 추가적인 기능이 있다.
- 슬래시(/) 키를 누르면 특정 용어 검색이 가능하다.
```Bash
less hi
```

# 저장 장치의 비트 단위 또는 물리적 복사본 생성
**dd** : 파일, 파일 시스템, 저장 창치의 비트 단위 복사가 가능하다. 심지어 삭제된 파일도 복사한다.
```Shell
dd if=[입력파일] of=[출력파일]
# 블록 사이즈 : 4096
# noerror : 오류가 발생해도 복사를 이어서 한다.
dd if=/dev/media of=/root/flashcopy bs=4096 conv:noerror
```