---
{"dg-publish":true,"permalink":"/06-full-notes/no-sql-modeling-1/","noteIcon":""}
---

# Tags
- [[03 - Tags/NoSQL\|NoSQL]]
---
# 핵심 요약
**NoSQL 데이터 모델링 패턴**
NoSQL의 데이터 모델링은 크게 3가지 구조로 저장 된다.
1. Key/Value Store: NoSQL의 핵심으로 모든 NoSQL은 Key/Value 데이터모델링을 제공한다. 다른 모델링은 Key Value 모델링에 추가되는 형식
2. Ordered Key/Value Store: 데이터를 특정 Key를 기반으로 순서대로 저장.
3. Document Key/Value Store: Key/Value에서 Value가 Column Family를 통한 Primitive 타입이 아니라, YAML이나 JSON 같은 문서형이다.

**NoSQL과 RDBMS의 데이터 모델링 차이**
개체 모델 지향 vs 쿼리 결과 지향 모델링(설계가 역순): NoSQL은 복잡한 쿼리문을 제공하지 않으므로 결과 값을 위한 조회 쿼리를 먼저 생각하고 이후에 테이블을 설계한다.
정규화 vs 비정규화: RDBMS는 정규화를 위해 기본적으로 값을 중복되지 않게 저장한다.[^1] NoSQL에선 쿼리 효율성을 위해서 의도적으로 중복된 데이터를 저장하기도 한다.

###### 데이터 모델링 절차
1. 도메인 모델 파악: 대략적인 개체의 속성과 개체 간의 관계를 ERD로 도식화
2. 쿼리 결과 디자인: 어플리케이션에 의해서 쿼리가 되는 결과값을 정한다.
3. 패턴을 이용한 데이터 모델링
4. 최적화를 위한 필요한 기능들을 리스팅
5. 후보 NoSQL을 선정 및 테스트
6. 데이터 모델을 선정된 NoSQL에 최적화 및 하드웨어 디자인


---
# 참고 자료
- http://bcho.tistory.com/665
# 주석


[^1]: RDBMS는 기본적으로 쿼리문을 통해 대부분의 문제를 해결할 수 있으므로 중복되지 않게 저장해도 Join을 통해서 쉽게 데이터를 가져올 수 있다.
