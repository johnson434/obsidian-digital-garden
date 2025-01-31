---
{"dg-publish":true,"permalink":"/06-full-notes/mvvm-mvp-mvi-mvc/","noteIcon":""}
---

[[03 - Tags/Develop\|Develop]]  [[03 - Tags/Develop Architecture\|Develop Architecture]]

> [!info] Pluu Dev - [잡담] 관성 개발 ~ 기존에 하던 관성으로 했습니다?!  
> Feb 4, 2023.  
> [https://pluu.github.io/blog/owner/2023/02/04/inertia_developer/](https://pluu.github.io/blog/owner/2023/02/04/inertia_developer/)  

### MVVM 패턴의 장단점

장점

- View와 Model이 서로 전혀 알지 못하기에 **독립성을** 유지할 수 있다
- 독립성을 유지하기 때문에 효율적인 유닛테스트가 가능하다
- **View와 ViewModel을 바인딩하기 때문에 코드의 양이 줄어든다**
- View와 ViewModel의 관계는 N:1 관계이다
- **유닛테스트를 하기가 좋습니다. 그 이유는 ViewModel에는 UIKit 관련 코드가 없고 Controller와의 의존성도 없기 때문이다**유닛테스트(Unit Test)는 하나의 메서드의 특정 루틴을 검사한다.

단점

- 간단한 UI에서 오히려 ViewModel을 설계하는 어려움이 있을 수 있다.
- **데이터 바인딩이** 필수적으로 요구된다.
- 복잡해질수록 Controller처럼 ViewModel이 빠르게 비대해진다. 표준화된 틀이 존재하지 않아 사람마다 이해가 다르다.(**표준화 필요**)
- View가 변수와 표현식 모두에 binding 될 수 있으므로 시간이 지남에 따라 관계없는 **Presentation Logic이 늘어나고** 이를 보완하기 위해 XML에 코드를 추가하게 된다. 이때 난해하게 코드가 증가된다면 유지보수 단계에서 어려움을 겪을 수 있다.

> [!info] MVVM 개념과 장단점  
> MVVM 패턴은 MVC 패턴에서 Controller를 빼고 ViewModel을 추가한 패턴입니다.  
> [https://velog.io/@ryuhyewon/MVVM-개념과-장단점](https://velog.io/@ryuhyewon/MVVM-개념과-장단점)