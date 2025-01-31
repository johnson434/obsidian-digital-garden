---
{"dg-publish":true,"permalink":"/06-full-notes/bastion-host-nginx/","dgPassFrontmatter":true,"noteIcon":""}
---

[[03 - Tags/작성 중인 문서\|작성 중인 문서]]
/etc/nginx/conf.d/argocd.conf
``` json
server {
    listen 443 ssl; // nginx가 요청을 받을 인바운드 포트
	server_name 13.125.130.82;  // 서버의 도메인 주소
    ssl_certificate /etc/nginx/ssl/nginx.crt; // openssl로 만든 인증서
    ssl_certificate_key /etc/nginx/ssl/nginx.key;

    location / {
	    // 서버가 요청을 리다이렉트할 주소
	    // 현재 포트포워딩을 통해 클러스터의 argoCD 주소를 가리키고 있다.
	    // 클라이언트 -> 443 -> |nginx -> localhost| -> 클러스터 -> argocd-server
        proxy_pass https://localhost:9090;  
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Cookie $http_cookie;
    }
}
```

```
nginx -t // 이 커맨드로 nginx 설정이 제대로 되어 있는지 확인 가능하다. 통과하면 nginx 재시작
sudo systemctl restart nginx
```
