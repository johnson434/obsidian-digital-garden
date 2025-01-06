---
{"dg-publish":true,"permalink":"/06-full-notes/docker-volume/","dgPassFrontmatter":true}
---

[[03 - Tags/Docker Volume\|Docker Volume]]
> [!info] Volumes  
> Learn how to create, manage, and use volumes instead of bind mounts for persisting data generated and used by Docker.  
> [https://docs.docker.com/storage/volumes/](https://docs.docker.com/storage/volumes/)  

- 컨테이너는 일시적이고 상태가 없으므로 영구 데이터는 외부에 저장해야 한다.
# Volume
- 영구데이터를 저장하기 위해 사용하는 논리적 폴더
- Container와 독립된 외부에 위치한다.
![types-of-mounts-volume.webp](/img/user/image/types-of-mounts-volume.webp)

# Volume CLI
docker volume create [volume name]
docker volume ls
docker volume inspect : 정보 출력
docker volume rm [volume name]
docker volume prune : 전체 삭제
docker run -d —name devtest -v myvol:/app nginx:latest
# Volume mount vs Bind mount

**저장 위치**
- 볼륨 마운트는 Docker Engine이 관리하는 영역을 컨테이너에 할당한다.
- 바인드 마운트는 Docker Engine이 관리하는 영역이 아닌 외부 영역을 컨테이너에 Bind한다.
**제어 방법**
- 볼륨 마운트는 컨테이너를 통해서만 제어 가능 → 자주 접근 안하는 파일은 Volume으로 마운트
- 바인드 마운트는 호스트 OS에서 간단히 제어 가능 → 자주 접근 하는 파일은 Bind로 마운트

|**항목**|**볼륨 마운트**|**바인드 마운트**|**임시 메모리(tmpfs)**|
|---|---|---|---|
|**스토리지 영역**|볼륨|디렉터리 또는 파일|메모리|
|**물리적인 위치**|Docker Engine의 관리 영역|어디든지 가능|메모리|
|**마운트 절차**|볼륨을 생성한 후 마운트|기존 파일 또는 폴더를 마운트||
|**내용 편집**|컨테이너를 통해서만 편집 가능|일반적인 파일과 같이||
|**백업**|까다로움|일반적인 파일과 같이||