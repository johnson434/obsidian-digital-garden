---
{"dg-publish":true,"permalink":"/06-full-notes/aws-krug-aws-serverless/","noteIcon":""}
---

# Tags
- [[03 - Tags/AWS KRUG\|AWS KRUG]]
---
# 후기
## 기억에 남는 내용
- DynamoDB와 Lambda 서버리스 서비스끼리 조합이 좋다.
- DynamoDB는 connection에 제한이 없다. 
- 예를 들어, Lambda가 0.1초 만에 대량의 요청이 들어 왔다. 이 때, RDBMS는 커넥션 풀이 모자라서 connection 초과 오류 가능성이 있다. 반면에, DynamoDB는 connection 제한이 없으므로 이럴 일 없다.[^1]
	- 내 생각: 
		- RDS도 Proxy 서버로 connection 스케일링이 가능하므로 Lambda와 같이 조합해도 괜찮지 않을까?
		- RDS Aurora Serverless도 서버리스이므로 NoSQL보다 SQL 구조가 적합한 데이터셋이 있다면 RDS Aurora Serverless가 더 나은 선택지가 아닐까? -> 단점 프록시 비용 추가. 
		- DynamoDB는 어떻게 Connection 제한 없이 요청을 처리할까? 예를 들어, Lambda warm start처럼 프로비저닝을 해놓는다면 그만큼 컴퓨팅 비용이 들 것이다. 그런데, DynamoDB는 시간 당 요금도 없다. -> HTTP API로 요청을 처리하며  클라이언트와 서버 간에 Connection을 유지 하지 않음. 따라서, connection 제한이 존재하지 않는다.
- Serverless 프레임워크라는 AWS의 `SAM`과 비슷한 서버리스 개발 프레임워크가 있다.
- WebSocket API Gateway는 내부적으로 ConnectionID를 저장한다. 이를 기반으로 클라이언트로부터 요청을 받거나 응답을 한다.
- CloudFront는 안쓰는 이유를 찾아야 할 정도로 거의 필수적인 선택이다.
- 서버리스로 아키텍처를 구성하면 엄\~\~\~청 싸다. 


![Pasted image 20250320224253.png](/img/user/image/Pasted%20image%2020250320224253.png)

# 참고 자료
- [Understanding DynamoDB's HTTP Connection Model And It's Benefits](https://www.linkedin.com/pulse/understanding-dynamodbs-http-connection-model-its-benefits-bitton-wpdle)

[^1]: Connection이 없다는 건, HTTP API를 통해서 요청을 하고 응답을 받는다는 뜻이다. 일반적인 DBMS가 connection을 유지하면서 데이터를 주고 받는다. 이는 요청할 때마다 connection을 맺을 경우 생기는 오버헤드를 줄이기 위해서다. 
	DynamoDB는 connection을 유지하고 요청을 하고 응답을 받는다. DynamoDB와 클라이언트 사이엔 어떤 연결도 유지되지 않는다.
