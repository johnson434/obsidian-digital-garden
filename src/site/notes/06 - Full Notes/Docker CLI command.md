---
{"dg-publish":true,"permalink":"/06-full-notes/docker-cli-command/","dgPassFrontmatter":true}
---

[[03 - Tags/Docker\|Docker]] 
# Management
**docker info** : docker 시스템 정보를 출력
**docker version** : docker 버전 확인
**docker login** : docker registry에 로그인

# Running and stopping

**docker pull [image name]** : 레지스트리에서 이미지 가져오기

**docker run [image name]** : 컨테이너를 실행
- **Flag Option**
    - **-p [HOST IP:PORT] : [Container Port] [container]** : 호스트와 컨테이너의 포트를 연결
        ```Bash
        # 호스트 80포트와 컨테이너 80포트를 연결하고 nginx를 container_name 이름으로 생성한다.
        docker run --publish 80:80 container_name nginx
        ```
        
    - **—name [container name]** : 컨테이너 이름 명명하기
    - **-d [image name]** : detached 모드로 실행(백그라운드에서 실행)
    - **—memory=”256m” [image name]** : 메모리 제한
    - **—cpus=”.5” [image name]** : CPU 사용 제한
    - **-it** : 쉘 붙이기
        ```Bash
docker run -it nginx -- /bin/bah  해당 프로그램 실행 가능
        ```
        

**docker ps** : 실행 중(running)인 컨테이너를 출력

- **Flag Option**
    - **-a** : 모든 컨테이너를 출력
    - **-q** : 컨테이너의 아이디만 보여준다.
    - **-f(—filtter)** : 조건에 맞는 결과를 출력ㅇ

**docker stop [container name]** : 컨테이너 정지

**docker kill [container name]** : 컨테이너 종료

**docker images** : 모든 이미지 출력

**docker image inspect [image name]** : 이미지 정보 출력

# Attach Shell
docker run -it [image name] /bin/bash
docker container exec -it [container name] bash
# Building
**docker build -t [name:tag] .** : 현재 있는 폴더에 있는 Dockefile로 이미지 빌드하기
**docker build -t [name:tag] -f [fileName]** : 해당하는 Dockerfile로 이미지를 빌드하기
**docker tag [imageName] [name:tag]** : 이미지에 태그 붙이기

# 유용한 명령어

```Bash
# 해당하는 status인 컨테이너를 전부 삭제
docker ps -a -f status=exited -q | xargs docker rm
# 멈춘 컨테이너 전체 삭제
docker rm $(docker ps -a -q)
# 컨테이너가 삭제하지 않는 이미지 모두 삭제
docker system prune -a
# 컨테이너에서 bash 실행하기
hwang@hwang:~$ docker exec -it webserver bash
root@9e785b22fdff:/# ls
bin   docker-entrypoint.d   home   media  proc	sbin  tmp
boot  docker-entrypoint.sh  lib    mnt	  root	srv   usr
dev   etc		    lib64  opt	  run	sys   var
```