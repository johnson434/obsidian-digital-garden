---
{"dg-publish":true,"permalink":"/06-full-notes/hacking-metasploit/","noteIcon":""}
---

[[03 - Tags/Security\|Security]]
# Metasploit

침투 테스트 및 해킹 프레임워크를 위해 사용된다.

# Metasploit 기본 동작

```Shell
# 실행
msfconsole
# 메타스플로이트 DB 설정
msfdb init
# postgres에 로그인
su postgres
# 사용자 만들기
createuser msf_user -P
```