---
{"dg-publish":true,"permalink":"/06-full-notes/caching-write-through-vs-write-back/","noteIcon":""}
---

# Tags
[[03 - Tags/Caching\|Caching]]

---
# 단서 질문
- 빈번한 쓰기 작업이 일어난다면 어떤 캐싱 전략이 좋을까?
	- write-back
---
# 핵심 요약
- 캐싱 서버를 통해 데이터 조회 Latency를 줄일 수 있다.
- 캐싱 전략으론 write-through, write-back, write-around가 있다.
- write-through는 쓰기를 할 때, 캐시와 DB에 데이터를 작성한다.
- write-back은 쓰기를 할 때, 캐시에만 데이터를 작성하고 주기적으로 캐시를 갱신한다.
- write-around는 쓰기를 할 때, DB에만 데이터를 작성한다.
---
# 핵심 필기
## 캐싱
컴퓨터 구조에선 CPU와 메모리(주기억장치) 사이에 Caching 장치가 있고, Caching에선 Buffer를 통해 메모리의 데이터를 업데이트한다.
일반적으로 인프라 구성도에서 캐싱은 컴퓨터 내부의 하드웨어를 의미하지 않고 DB 서버와 클라이언트 사이에 존재하는 캐싱 서버를 의미한다.
데이터베이스 서버의 조회 속도는 보조 기억장치이기 때문에 상당히 느리다.
이러한 DB 계층에서 발생하는 Latency를 완화하기 위한 계층이 Caching 계층이다.
자주 조회되는 데이터를 캐싱 서버(In-Memory)에 저장하여 DB 계층에 직접 질의하지 않아 더 신속한 조회 속도를 제공한다.
### 캐싱 전략
#### 1. write-through
쓰기 작업이 요청되면 Caching 서버와 DB 모두에 쓰기 작업을 진행한다.
AWS의 공식 문서인 Memcached를 보면 DB에 업데이트 이후에 캐시 서버에 데이터를 저장한다.
```
save_customer(customer_id, values) 
	# DB에서 데이터를 업데이트한다.
	customer_record = db.query(
		"UPDATE Customers WHERE id = {0}", 
		customer_id, 
		values
	) 
	# 캐싱 서버에 데이터를 업데이트한다.
	cache.set(customer_id, customer_record) 
	
	return success
```
**장점**
- 캐싱 서버와 DB 서버의 데이터가 무조건 동일하다.
**단점**
- 쓰기 작업을 하면 무조건 두 개의 쓰기(Caching 서버 쓰기 + DB 서버 쓰기)가 들어간다.
#### 2. write-around
쓰기 작업이 요청되면 DB에만 쓰기 작업을 진행합니다.
데이터가 조회되면 그 때, Caching 서버에 데이터를 최신화합니다.

**장점**
- write-through는 모든 작업이 두 개의 쓰기(Caching 서버 쓰기 + DB 서버 쓰기)가 들어간다. write-through는 이에 비하면 가볍다.
**단점**
- 캐시 활용도가 낮음
#### 3. write-back
자주 사용되지 않는 방법이다.
쓰기 작업이 요청되면 Caching 서버에 데이터를 쓰고, DB엔 쓰지 않는다.
캐시는 캐시가 추출(eviction)되거나 특정 주기를 기준으로 갱신됩니다.
**장점**
- 쓰기 작업이 빈번할 경우에 i/o 병목 현상을 줄일 수 있다. (매 번 디스크에 작성하지 않으니까)
**단점**
- 캐싱 서버 날아가면 데이터 손실
---
# 참고 자료
Best practices for Memcached
https://docs.aws.amazon.com/AmazonElastiCache/latest/dg/Strategies.html#Strategies.WriteThrough