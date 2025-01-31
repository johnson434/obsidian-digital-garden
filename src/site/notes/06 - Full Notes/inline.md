---
{"dg-publish":true,"permalink":"/06-full-notes/inline/","noteIcon":""}
---

[[03 - Tags/C++\|C++]] 
![Pasted image 20231009085829.png](/img/user/image/Pasted%20image%2020231009085829.png)
일반적인 함수는 호출 되면 위와 같이 실행됩니다.
main 함수 실행 중에 A함수를 실행으로 표현하겠습니다.
1. main 함수에서 A함수를 호출합니다.
2. main 함수의 주소 값을 스택에 저장합니다.(A함수가 종료되면 다시 시작해야 하므로)
3. A함수를 실행합니다.
4. 함수가 종료되면 스택에서 main 함수의 주소를 pop한 후에 다음 줄부터 실행합니다.
위처럼 오버헤드가 발생하므로 이보다 효율적으로 실행을 위해 inline 함수를 사용합니다.(클래스의 멤버함수는 inline 키워드를 붙이지 않아도 inline함수로 컴파일 됩니다.)

## inline 코드 예시
``` cpp

inline int get_bigger_number(int a, int b)
{
	return a > b ? a : b;
}

int main() 
{
	int a = 10;
	int b = 20;

	int bigger_number = get_bigger_number(a, b);
}
```

위와 같은 코드는 컴파일 타임에 치환되어 코드로 변경 됩니다.

## 컴파일 이후 치환된 코드
``` cpp
inline int get_bigger_number(int a, int b)
{
	return a > b ? a : b;
}

int main() 
{
	int a = 10;
	int b = 20;

	// 아래와 같이 치환됩니다.
	int bigger_number = a > b ? a : b;
}
```