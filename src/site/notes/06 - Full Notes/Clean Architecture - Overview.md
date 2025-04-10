---
{"dg-publish":true,"permalink":"/06-full-notes/clean-architecture-overview/","noteIcon":""}
---

#Architecture #Develop
[[03 - Tags/Clean Architecture\|Clean Architecture]] [[03 - Tags/개발 방법론\|개발 방법론]] 
# 클린 아키텍처
![Pasted image 20231205193549.png](/img/user/image/Pasted%20image%2020231205193549.png)
클린 아키텍처에 핵심은 코드 재사용성 증가, 관심사 분리(비즈니스 로직 분리)와 테스트 용이 등이 목적입니다.

## 클린 아키텍처 읽고 느낀 점
제가 읽은 책은 FIVE에서 집필한 Android Architecture입니다. [해당 책 링크](https://five.agency/android-architecture-part-1-every-new-beginning-is-hard/)
글을 읽고 느낀 점은 '이 기술을 엄격하게 지킬 필요는 없겠지만, 관심사 분리는 맘에 든다'라고 느꼈습니다.
사실 클린 아키텍처가 핫하지만, 이에 대해서 쓸 데 없는 보일러 플레이트가 많다거나 오버 엔지니어링이라고 말하는 사람들이 있다는 걸 먼저 접하고 편견을 가지고 글을 읽게 됐습니다.
결과적으로 저는 클린 아키텍처가 기존 프로젝트를 아예 전환하기엔 비용이 커서 부담되겠지만, 처음 시작하는 프로젝트엔 충분히 적용할 만한 아키텍처라는 생각이 들었습니다.

---
## 왜 쓸까?
- 관심사 분리
- 모듈 재사용 가능
- 테스트 용이
---

## 클린 아키텍처 모듈 나누는 법
![Pasted image 20231205203036.png](/img/user/image/Pasted%20image%2020231205203036.png)
- Domain
- Data
- UI
- Device
---
### 1. Domain
안드로이드 기준 순수 Java나 Kotlin 코드로 이루어져 비즈니스 로직을 다루는 부분입니다.
해당 책에서는 도메인 Module을 만위의 코드에선 들 때, Java 프로젝트로 만들어서 아예 Android Framework 코드 사용이 불가하게 만드는 걸 권장했습니다.

**해당하는 클래스들**
1. Entity
2. Repository의 인터페이스
3. Service의 인터페이스
4. UseCase
5. Device 종속 기능 사용을 위한 인터페이스

1. Entity : DB에 테이블과 매핑 되는 클래스
``` kotlin 
data class User(val id: Int, val name: String, val age: Int?)
```

2. Repository의 인터페이스 : 비즈니스 로직에 사용될 메서드들을 정의한 인터페이스. 실제로  DB에 접근하는 구현 부분은 DB 종류에 종속될 수 있으므로(원의 가장 외부 부분) 인터페이스로 정의한다.
``` kotlin
// 1. 아키텍처를 적용하지 않은 경우
// 만약, Repository에서 Firebase를 바로 썼다면?
// UseCase는 Repository를 참조할 것이고, Repository는 Firebase 이는 UseCase가 Firebase에 종속됩니다.
class Repository {
	val db = 파이어베이스
	
	fun fetchUser(): User {
		// 파이어베이스의 fetch 로직이 들어간다
	}
}
```

``` kotlin 
// 2. 아키텍처를 적용한 경우
// Domain Layer의 Repository
interface Repository {
	fun fetchUser(): User
}

// Data Layer의 RepositoryImpl
// UseCase는 Repository 인터페이스를 참조하고 해당 인터페이스는 Firebase를 참조하지 않으므로 UseCase는 Firebase에 종속되지 않습니다.
class RepositoryImpl: Repository {
	override fun fetchUser(): User {
		// 파이어베이스 로직 작성
	}
}
```

3. Service : API 인터페이스를 정의
``` kotlin
interface CatService {
	fun getCat(): Cat
}
```

4. UseCase :  서비스를 사용하고 있는 사용자(User)가 해당 서비스를 통해 하고자 하는 것을 의미합니다. 좋은 UseCase는 한 문장으로 설명이 가능합니다.(예시 : 유저 정보 빼와 FetchUserUseCase). 장점은 UseCase를 통해 직관적으로 무슨 행위를 하는 지 파악이 가능하다는 점입니다. 다른 장점으로는 Repository가 수정이 일어났을 때, UseCase를 사용하는 부분만 수정하면 된다는 점입니다.
``` kotlin 
class FetchUserUseCase(private val repository: Repository) {
	fun execute(query: String): User {
		return repository.fetchUser(query)
	}
}
```

5. Device 종속 기능 사용을 위한 인터페이스
``` kotlin
// Notification : Domain 영역에서 아래 인터페이스를 통해 Notification을 만든다.
// 따라서, Domain 영역에서 Android Framework에 종속되지 않는다.
interface Notification {
	fun createUserNotification()
}

// UserNotification : Device 모듈에 속하는 부분으로 
class UserNotification: Notification {
	override fun createUserNotification() {
		// Android Framework에 종속된 코드 작성
	}
} 
```
### 2. Data
- Repository 구현체(DB 접근 로직 작성)
- Service 구현체(Retrofit 등)
### 3. UI
- Activity, Fragment, Adapter
- ViewModel
### 4. Device
- 비즈니스 로직에서 사용할 Device 종속 기능을 구현하는 클래스