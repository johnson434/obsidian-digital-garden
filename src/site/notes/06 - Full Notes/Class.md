---
{"dg-publish":true,"permalink":"/06-full-notes/class/","noteIcon":""}
---

[[03 - Tags/C++\|C++]] 
# const 함수
const 함수는 객체를 수정하지 않는다는 의미로 사용된다.
즉, const로 선언된 객체는 const 함수만 부를 수 있다.(당연하다. const로 선언된 객체는 내부를 수정하지 않는다는 의미이므로 내부 수정이 가능한 const로 선언되지 않은 함수는 실행할 수 없어야한다.)
``` cpp
#include <iostream>  
#include <map>  
  
using namespace std;  
  
class SomeObject  
{  
public:  
    SomeObject(): number(0) {}  
    int get_number() const  
    {  
        return this->number;  
    }  
  
    void set_number(int number)  
    {  
        this->number = number;  
    }  
private:  
    int number;  
};  
  
int main()  
{  
	// const 함수, 일반 함수 둘 다 사용 가능
    SomeObject s1;  
    s1.get_number();  
    s1.set_number(3);  

	// const 함수만 사용 가능.(const로 선언했으니 수정이 불가능한 const 함수만 사용 가능)
    const SomeObject s2;  
    s2.get_number();  
    // Error  
    s2.set_number(5);  
  
    return 0;  
}
```

## 추상 클래스
뼈대를 제공하는 클래스.
자체는 인스턴스화가 불가능하지만 상속을 통해 해당 클래스의 메소드를 구현한다.
``` cpp
class Container {
public:
	//  = 0을 통해 순수 가상 함수로 만들 수 있다.
	// 순수 가상 함수는 override를 강제한다.
	virtual int GetSize() = 0;
}


```