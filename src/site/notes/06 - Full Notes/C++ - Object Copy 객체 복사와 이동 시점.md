---
{"dg-publish":true,"permalink":"/06-full-notes/c-object-copy/","noteIcon":""}
---

[[03 - Tags/C++\|C++]] 
# 대입 연산의 원본으로서
``` cpp
Class a = Class();
```

# 객체 초깃값으로서

# 함수 인자로서
``` cpp
void make_operation(Class obj) {
	
}

int main() {
	Class original;
	// obj = original이라는 대입 연산이 실행되면서 복사나 이동이 발생한다.
	make_operation(original);
}
```

# 함수 반환값으로서
``` cpp
int operation() {
	return 1;  // rvalue로 1을 복사
}

int main() {
	// operation()을 실행하는 시점에서 1을 리턴하는 시점에서 1회
	// num = 1;로 함수 결과값은 대입하는 시점에서 1회
	// 총 2회에 복사 or 이동 연산이 발생한다.
	int num = operation();  // num에 대입하며 복사 연산 실행.
}
```

# 예외로서