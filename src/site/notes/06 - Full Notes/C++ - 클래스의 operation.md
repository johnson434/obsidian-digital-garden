---
{"dg-publish":true,"permalink":"/06-full-notes/c-operation/","noteIcon":""}
---

[[03 - Tags/C++\|C++]] 
``` cpp
class X {
public:
	// 일반적인 생성자
	X(Sometype);
	// 기본 생성자
	X();
	// 복사 생성자
	X(const X&);
	// 이동 생성자
	X(X&&);
	// 복사 대입 : 대상을 정리하고 복사
	X& operator=(const X&);
	// 이동 대입
	X& operator=(X&&);
	// 소멸자
	~X();
}
```

만약, 얕은 복사(Shallow Copy)를 사용할 경우 default를 통해 명시할 수 있다.
default를 사용하면 다른 개발자가 봤을 때, 이 객체는 컴파일러에서 생성해주는 생성자를 사용(컴파일러에 기본 생성자는 얕은 복사이므로 얕은 복사를 사용한다는 것을 명시한다고 보면 된다)함을 알 수 있다.
[예시]
``` cpp
class Y {
public:
	Y() = default;
	// 기본 복사 생성자를 사용한다는 뜻.
	Y(const Y&) = default;
	// 기본 이동 생성자를 사용한다는 뜻.
	Y(Y&&) = default;
}
```

기본적으로 이동 생성자와 복사 생성자는 클래스가 포인터를 가질 때 사용하는 것이 좋다.
why? : A객체가 a메모리를 향한 포인터를 가지고 있다가 소멸 시점에서 해당 메모리를 해제하는 경우를 생각해보자. 만약, B객체가 A복체에 얕은 복사를 통해 만들어졌다면 B객체는 동일하게 a메모리를 향한 포인터를 가지고 있을 것이다. 그러나, A객체가 소멸되는 시점에서 해당하는 메모리를 해제될 것이고 B는 이러한 해제된 메모리를 참조하게 될 것이다.

# delete
delete를 사용하면 해당 하는 함수나 생성자의 코드를 컴파일러가 생성하지 못하도록 막는 기능이다.
'X() = delete'는 기본 생성자를 생성하지 않는다.
``` cpp
class X {  
public:  
    X() = delete;  
    X(const X&) = default;  
};  
  
int main() {  
	// delete로 마크된 생성자를 호출했다며 컴파일 시점에서 에러가 발생한다.
    X x;  
}
```