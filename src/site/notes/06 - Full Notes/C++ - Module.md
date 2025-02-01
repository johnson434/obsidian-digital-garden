---
{"dg-publish":true,"permalink":"/06-full-notes/c-module/","noteIcon":""}
---

[[03 - Tags/C++\|C++]] 
`#include`를 통하여 프로그램을 부분적으로 구성하는 방법은 비용이 크고 위험성이 큽니다.
예를 들어, \#include를 101번 사용하면 해당 코드를 101번 복사합니다.

이를 대체하는 방법이 module입니다.
**단, 모듈은 아직 상용화된 방식은 아님.**

module 예시
``` cpp
// Vector.cpp
module;

export module Vector;

export class Vector {
public:
	Vector(int s);
	double& operator[](int i);
	int size();
private:
	double* elem;
	int sz;
};

Vector::Vector(int s): elem{new double[s]}, sz{s} 
{
}

double Vecotr::operator[](int i) 
{
	return elem[i];
}

int Vector::size()
{
	return sz;
}

export int size(const Vector& v)
{
	return v.size();
}
```

``` cpp
// user.cpp
import Vector;
```