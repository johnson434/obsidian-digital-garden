---
{"dg-publish":true,"permalink":"/06-full-notes/dev-ops-code-coverage/","noteIcon":""}
---

 [[03 - Tags/Code Coverage\|Code Coverage]]
- **핵심 내용 요약**
    - 코드 커버리지는 테스트 케이스를 통해 실행되는 코드의 비율.
    - Code Coverage 방법은 코드의 줄수를 기점으로 계산한다.
    - Branch Coverage 방법은 Branch를 기준으로 계산한다. 예를 들면, if문과 함수 선언문, for문 등이 포함된다.
# Code Coverage란?
테스트 코드가 종합적인 코드의 얼마나 많은 부분을 포함하는지 체크한다.
**로직 코드**
```Python
def some_function(n): # 1
    result = [] # 2
    for i in range(n): # 3
        result.append(i) # 4
        if i == 10: # 5
            result.append(f"10th line : {i}") # 6
    # 7
    return result # 8
```

위의 코드에서 Syntax line(문법 라인)은 1,7이다.
로직 라인은 2,4,6,8이다.
브랜치 라인은 3,5이다.
결과적으로 Syntax Line = [1,7], Non Syntax Line = [2,3,4,5,6,8]이 된다.

**코드 커버리지를 구하는 공식**은 (Non Syntax Lines which executed by test) / (Non Syntax Lines)

아래엔 테스트 코드가 주어진다.
**예시 :**

```Python
import unittest

class TestSomeFunction(unittest.TestCase):
    def test_some_function(self):
        n = 5
        result = some_function(5)
        self.assertEqual([0,1,2,3,4], result)

    def test_some_function_when_n_is_bigger_than_10(self):
        n = 11
        result = some_function(n)
        print(result)
        self.assertEqual([0,1,2,3,4,5,6,7,8,9], result)

```

**test_some_function** 테스트 함수에서 코드 커버리지를 구해보자.
n은 5이므로 5번째 줄 if 제어문에 조건을 만족하지 못한다. **따라서,** **some_function 함수의 6번째 줄은 실행되지 않는다.**
따라서, test_some_function의 코드 커버리지는 (6 - 1) / 6 = 0.83333..이 된다.
**test_some_function_when_n_is_bigger_than_10** 테스트 함수에서 코드 커버리지를 구해보자.
이 테스트 함수에서 n은 10보다 큰 값이므로 실행되지 않는 줄이 없다.
따라서, 커버리지는 6 / 6 = 1 → 100%가 된다.
이렇듯 위처럼 코드 커버리지를 생각하고 테스트 코드를 작성하면 예상하지 못한 버그에 발생을 현저히 줄일 수 있다.
# Branch Coverage란?
여기서 나아가서 코드의 라인을 기준으로 Coverage를 구분하는 것이 아닌 branch를 기준으로 나누는 방법도 있다.
위의 코드에선 **some_function()의 바디**, **for 루프의 바디**, **if문의 바디**까지 해서 총 3개의 브랜치가 나온다.
첫번째 테스트 코드는 위에서 if문의 바디를 실행하지 않으므로 (3 - 1) / 3 == 2/3 == 0.666…이 된다.
두번째 테스트 코드는 모든 branch를 실행하므로 3 / 3 == 1 == 100%가 된다.

---