---
{"dg-publish":true,"permalink":"/06-full-notes/s3-static-web-hosting/","dgPassFrontmatter":true}
---

[[03 - Tags/AWS S3\|AWS S3]]
# 문제 상황
1. 크롬 웹브라우저로 Hosting 주소에 접속 시도
2. 웹브라우저에서 index.html 파일을 다운로드 
# 정상 동작 시 결과
1. index.html을 파싱하여 웹브라우저에서 화면을 보여줌.
# 문제 찾는 과정
1. curl을 통해서 응답 상세히 확인
``` bash
$ curl -v http://web-site-hosting-test1234.s3-website.ap-northeast-2.amazonaws.com

> GET / HTTP/1.1
< Content-Type: binary/octet-stream
< Content-Length: 216
< Server: AmazonS3

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h1>HI</h1>
</body>
* Connection #0 to host web-site-hosting-test1234.s3-website.ap-northeast-2.amazonaws.com left intact
```
응답의 Content-type이 text/html이 아닌 binary/octet-stream으로 설정 되어 있음.
따라서, 웹브라우저에서 다운로드로 파악하여 파일을 다운로드함.
![Pasted image 20250121094446.png](/img/user/image/Pasted%20image%2020250121094446.png)
 S3의 index.html 오브젝트의 메타데이터를 확인하면 지금은 `text/html`이지만, `binary/octet-stream`으로 설정되어 있었음.
# 문제 해결
- Metadata를 수정하면 된다. 
- Object에서 바로 수정이 안되고 Copy를 통해서 수정이 가능
# 문제 원인
- AWS `s3api` 옵션으로 `--content-type`을 명시하지 않아서 생긴 문제다.
- `--content-type`을 명시하지 않으면 binary-octet으로 컨텐츠 타입이 자동으로 설정된다.
- 브라우저는 요청의 응답이 binary-octet으로 오니 다운로드로 처리.
``` bash
aws s3api put-object --bucket 버켓명 --key 키 --body 파일 --content-type text/html
# 위처럼 text/html을 content-type으로 설정하지 않을 시에 default 값으로 binary/octet이 들어간다.
# 브라우저는 이진파일인줄 알고 다운로드
```