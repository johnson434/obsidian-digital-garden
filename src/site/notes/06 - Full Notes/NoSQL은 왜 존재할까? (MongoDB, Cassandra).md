---
{"dg-publish":true,"permalink":"/06-full-notes/no-sql-mongo-db-cassandra/","noteIcon":""}
---

#NoSQL 

https://www.youtube.com/watch?v=YDR3D2bsv9Y&ab_channel=InterviewPen

## 예제 스키마 구조
![Pasted image 20241030190305.png](/img/user/image/Pasted%20image%2020241030190305.png)
사진과 같은 스키마를 가진 데이터베이스가 있다고 생각해보자.
Order와 Product는 Many to Many 관계로 한 개의 Order는 여러 개 Product를 가질 수 있고 한 개 Product는 여러 개 Order를 가질 수 있다.

## 예제 관계형 모델
![Pasted image 20241030190506.png](/img/user/image/Pasted%20image%2020241030190506.png)
order_product 테이블을 통해서 관계를 효과적으로 관리가 가능합니다.
1. order_product 테이블을 통해서 다대다 관계를 일대다로 분리할 수 있습니다.
2. order_product 테이블을 통해서 주문에 포함된 제품의 수량이나 개별 가격 등을 추가적으로 저장하여 주문의 상세 정보를 관리하기 편리합니다.
3. 중복 최소화: order_product 테이블을 사용하면 동일한 order에 대해서 여러 product가 들어 갔을 때 중복으로 order를 선언하지 않아도 됩니다. (예: order_product 테이블이 없으면 Order 테이블에 컬럼으로 product를 위한 배열을 넣는 식으로 진행해야함.)

## Single Database Node
![Pasted image 20241030191107.png](/img/user/image/Pasted%20image%2020241030191107.png)
데이터베이스 노드가 한 개인 경우 위처럼 모든 테이블이 하나의 데이터베이스에 들어있으므로 조인이 간단합니다.
그러나, 위와 같은 데이터베이스 형식은 수직 스케일링만 가능하며 수직 스케일링은 기하급수적으로 가격이 상승합니다. 따라서, 수직 스케일링에 부담이 커지며 이는 확장성(Scalability)에 문제로 작용합니다. 

## Database sharding
데이터베이스 샤딩은 데이터베이스의 서브셋을 여러 개의 노드에 나눠서 저장하는 방식입니다.
![Pasted image 20241030191437.png](/img/user/image/Pasted%20image%2020241030191437.png)
데이터베이스 샤딩을 통해서 저장하면 데이터가 추가되더라도 노드의 개수를 늘리는 것으로 간단하게 확장이 가능합니다. 그러나, 위의 Single Node Database와는 달리 테이블 조인 등을 하려면 서로 다른 데이터베이스 노드에 접근해야 하므로 효율성(Efficiency)이 떨어집니다.

## Database sharding의 개선안
자주 사용되는 쿼리는 모든 로우를 조회하는 경우가 많습니다. 위의 예제의 경우 order에 포함되는 모든 product를 조회하는 경우가 많을 것입니다. 이를 해결하기 위해서 데이터베이스 샤딩을 진행할 때 product 레코드들을 모든 샤딩 노드에 중복 저장하는 방법이 있습니다. 이는 중복된 데이터를 저장하게 되지만 서로 다른 데이터베이스 노드의 접근할 필요가 없으므로 효율성은 좋습니다.(trade-off)
![Pasted image 20241030192011.png](/img/user/image/Pasted%20image%2020241030192011.png)
- Get products in order: 동일한 노드에서 모든 데이터 조회가 가능합니다.
- Count orders for product: order_id로 접근해서 해당 노드에서 product를 조회하므로 단일 노드에서 모든 데이터가 조회 가능합니다.

## NoSQL 구조 데이터베이스
![Pasted image 20241030193509.png](/img/user/image/Pasted%20image%2020241030193509.png)
위처럼 product에 추가적으로 product의 order개수를 count에 저장하면 단순하게 product 한 개 조회만으로 order 전체 개수를 얻을 수 있습니다. (예: product에 count 속성이 추가되고 거기에 )

