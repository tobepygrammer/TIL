# 프로그래밍의 정석

> 초보자용 책으로는 문법과 노하우까지는 익힐 수 있을지언정 본질적인 부분의 프로그래밍 기술 개선에는 도달하지 못한다. 그렇다고 해서 프로그래밍 달인들의 유명한 책을 읽어보면 이번에는 문턱이 높고 너무 어려워서 좌절하거나, 지식이 머릿속에서 바로 빠져나가 결국 얻는 것 하나 없이 끝나고 만다.

> 각 정석에 관해서 그것은 무엇이고(what), 그것이 왜 그렇고 왜 필요하며(why), 그렇다면 어떻게 해야 하는지(how)를 중심으로 설명한다.

> 정석은 특정 기술에 특화되지 않은 추상도가 높은 정보이므로 언뜻 보면 이게 무슨 도움이 될까 싶은 내용도 있을 것이다. 그러나 현존하는 구체적인 기술은 사실 정석의 목적을 구현한 것이다.

> 정석을 이해하고 있다면 구체적인 기술을 배울 때 기술의 존재 이유, 즉 해당 기술이 왜 필요한지를 이해할 수 있다.

## 0.1 정석의 카테고리
> 1. 전제 - 프로그래밍 불변의 사실
> 프로그래밍의 보편적 사실에 관한 정석을 소개한다. 여기서 소개하는 정석은 '사실'이지 '테크닉'은 아니다. 이 내용은 프로그래밍을 하는 데 매우 중요한, 영원히 변하지 않는 사실이다. 앞으로 소개될 모든 정석의 전제가 되는 지식이기도 하다.

> 2. 원칙 - 프로그래밍의 가이드라인
> 대부분의 경우에 공통으로 적용되어야 할 프로그래밍의 기본 규칙. 예외를 인정하는 다소 느슨한 규칙이다.

> 3. 사상 - 프로그래밍의 이데올로기
> 6가지
> 프로그래밍 이론
> 아키텍처 기본 기법
> 아키텍처 비기능 요구사항
> 7가지 설계 원리
> UNIX 사상
> UNIX 철학

> 4. 관점 - 프로그래머가 보는 시각
> 프로그래밍의 관점과 견해에 관한 정석
> 프로그래밍에 능숙한 사람은 의식적이든 무의식적이든 장래에 개선하기 쉬운 코드를 작성한다. 그런 코드에는 '이런 관점으로 생각하고 이런 요소를 고려해 두면 나중에 좋다.' 처럼 생명력이 오래가는 코드로 만들기 위한 관점과 견해가 존재한다.

> 5. 습관 - 프로그래머의 일상
> 좋은 코드를 작성하는 습관과 행동 지침

> 6. 기법 - 프로그래머의 도구 상자
> 여기서 말하는 도구란 사고의 프레임워크이자 설계 기법 혹은 문제 해결 방식

> 7. 법칙 - 프로그래밍의 안티패턴
> 소프트웨어를 개발할 때 빠지기 쉬운 함정에 관한 정석을 소개
> 안티패턴(일반적으로 사용되지만 실제로는 비효율적이거나 비생산적인 디자인 패턴)

## 1.1 프로그래밍에 은제 탄환은 없다.
> 소프트웨어 본질에는 그런 난해서을 보여주는 4가지 성질이 있다.
> 복잡성
> 동조성: 소프트웨어는 실세계와 계속 동조해야 한다.
> 가변성: 설령 계획대로 소프트웨어가 완성되더라도 그것을 사용하는 사용자는 다른 요구사항을 떠올린다. 이는 완성된 소프트웨어가 세상을 바꾸기 때문이다. 소프트웨어가 사용자의 인식에 영향을 미치며 새로운 요구사항이 생긴다.
> 비가시성: 추상화해서 단순한 도면으로 만드는 작업은 가능한다. 다만 이때는 정보가 사상되므로 모든 정보를 표현할 수는 없다.

> 소프트웨어의 본질은 난해성이며 최대 요인은 복잡함에 있다. 소프트웨어 개발의 역사는 사실 복잡함과의 투장의 역사다. 역사를 배우고 다양한 기법이나 사고방식을 익혀서 착실하게 복잡함을 줄여 나가자.

> 우유(비본질)란 우연히 발생했다는 의미가 아니라 부차적이고 부수적이라는 의미로 그것이 없어도 대상물이라고 할 수 있는 성질이다. 소프트웨어 개발 현장에서 필요한 기술이라고 알려진 것의 대부분은 우유적인 부분이다.

> 빌드 환경, 프로그래밍 언어, 라이브러리, 프레임워크 등은 우유적이다.

> 본직절진 부분의 작업에 주력하는 데는 우유적인 부분의 작업이 큰 역할을 수행한다. 왜냐하면 우유적인 부분은 본질적인 부분과는 대조적으로 쉽게 개선할 수 있기 때문이다. 예를 들어 적잘한 프로그래밍 언어를 선택하기만 해도 생산성은 극적으로 올라간다.

> 우유적인 부분의 개선 중에서 특히 큰 성과를 낸 것이 자동화다. 테스트나 빌드, 환경 설정 등을 자동화해서 작업 효율이나 작업 품질이 크게 개선되었다. 우유적인 부분을 찾아냈다면 자동화해서 가능한 한 본질적인 부분에 시간을 할애하자

## 1.2 코드는 설계서다.
> 기본 설계부터 상세 설계, 프로그래밍, 테슽, 디버깅까지 모든 과정이 설계이며, 설계의 결과물인 설계서가 코드다. 제조에 해당하는 공정은 배포와 빌드다. 즉, 제조는 프로그래머가 아닌 컴파일러나 빌드 시스템이 수행한다.

> 기본 설계, 상세 설계, 프로그래밍, 테스트, 디버깅까지가 설계라는 한 묶음의 작업이라면, 그런 작업을 분담하는 것이 바람직한 방식은 아니라는 점을 알 수 있다. 

> 로제타 스톤: 유지보수 담당자에게 필요한 가이드북
> 여기에는 소프트웨어 개발 환경을 이해하기 위한 정보와 소프트웨어 아키텍처를 이해하기 위한 정보가 담겨 있다.
> 소프트웨어 개발 환경은 빌드와 테스트 프로세스를 수행하는 벙법을 기술한다.
> 소프트웨어 아키텍처는 전체를 파악하기 위해 필요한 그림, 즉 코드로부터 파악할 수 없는 코드 전체를 부감해서 본 그림을 기술한다.

> 코드에는 how와 what은 잘 표현되어 있지만 why, 즉 설계 이유가 없다. 설계 이유를 문서에 기술해 두면 유지보수 담당자가 수정을 판단하는 정보로 활용할 수 있어 장래 대부분의 상황에서 도움이 된다.

설계이유를 적은 적이 한번이라도 있었던가?

## 1.3 코드는 반드시 변경된다.
> 프로그래밍에서 하나하나의 판단은 해당 코드가 변경된다는 점을 전제로 수행하도록 하자. 즉, 변경에 강한 코드를 작성한다는 뜻이다.
> 그렇게 하려면 코드가 읽기 쉬워야 한다. 결국 코드라는 것은 작성하는 시간보다 읽는 시간이 훨씬 길어진다.

## 2.1 KISS
> Keep It Simple, Stupid. Keep It Short and Simple.

> 코드를 작성할 때 최우선 가치를 단순성과 간결성에 둔다.

> 장래에 필요해질지도 모르니, 그렇다면 지금 작성하는 것이 최선이라고 생각하면서 코드를 넘치게 작성할 때가 있다. 그러나 지금 필요하지 않다면 지금 작성할 것이 아니다. 대부분 그렇게 작성한 코드는 필요해지지 않는다.

> 기능만 많고 복잡한 소프트웨어는 결국 사용하지 않는다. 기능과 인터페이스를 단순하게 유지하면 사용하기 쉽고 많이 사용하는 소프트웨어가 된다.

> 오컴의 면도날
> 어떤 사항을 설명하는 데 필요 이상으로 많은 전제를 가정해서는 안된다.
> 뭔가에 관해 여러 가지 설명이 가능하다면 가장 단순한 방식이 옳다.

## 2.2 DRY
> Don't Repeat Yourself

> 같은 조건을 다루는 제어문 블록이 여러 군데 중복해서 나타날 때도 있다. 이것도 중복이다.
> 정수 리터럴을 직접 코드에 내장하는 것도 코드 중복이다.
> 중복 코드는 대부분 해당 부분에 대한 테스트가 없다.

> 코드를 추상화하여 중복을 제거하자. 코드 로직을 추상화하려면 처리하는 코드를 묶고 이름을 붙여 함수화, 모듈화한다. 데이터라면 이름을 붙여 상수를 정의한다. 이렇게 이름을 붙인 코드를 반복해서 나오는 부분에 치환한다.

> 추상화하면 다음과 같은 장점이 있다.
> 코드의 양이 줄어 읽는 양을 줄일 수 있다.
> 로직이나 데이터에 이름이 붙었으므로 코드가 읽기 쉬워진다.
> 똑같은 코드가 한 군데에 집약되어 있어서 한 군데만 수정하면 되므로 코드를 수정하기 쉽고, 품질을 담보하기 쉬워진다.
> 재사용이 쉬워진다.

> DRY의 적용 범위는 코드에만 한정되지 않는다. 자동화 대상으로 여겨지는 대포적인 작업으로는 '지속적인 통합'이라고 불리는 스프트웨어의 테스트, 빌드, 배포 작업이 있다.

> 불가피한 중복도 존재한다.
> 임피던스 불일치. 프로그래밍과 프로그래밍이 사용하는 서비스 사이의 틈을 메우는 정보 부분에서 불가피하게 중복이 발생한다.
> 추상화 스타일이 서로 다른 경계선.

> WET: DRY가 되어 있지 않은 코드에 대해 비꼬는 표현으로 사용한다.

> OFOP One Fact in One Place
> 한 곳에는 하나의 사실. 데이터베이스 논리 설계에서 테이블 설계의 핵심이 되는 원칙.

> 레거시 코드. 최근에는 테스트에 의한 품질 보호를 중요시할 목적으로 테스트가 없는 코드라고 재정의 되었다.

## 2.3 YAGNI
> You Aren't Going to Need it

> 미리 여러 가지 사태에 대비해 코드를 넣어 두더라도 결국 이용되지 않는 것이 대부분이다.
> 오히려 시간이 지날수록 어째서 이렇게 사용하지도 않는 코드가 있는 것인지 영문을 알 수 없다.

> 범용성보다 단순성을 선택하자. 단순한 방식이 사실 범용성이 더 높을 때가 많다.

> DTSTTCPW Do The Simplest Thing That Could Possiby Work
> 효과가 있는 방법 중에서 가장 간단한 방법으로 하라.

## 2.4 PIE
> Program Intently and Expressively

> 코드를 작성할 때 의도를 명확하게 표현해야 한다. 코드는 컴파일러가 아닌 사람이 읽기 위한 것이다.

> 코드는 작성하는 경우보다 읽히는 경우가 훨씬 많다. 코드는 실행 효율보다 읽는 효율이 우선시 된다. 읽기 쉽다면 나중에 실행 효율을 높이기는 간단하다.

> 자기 코드든 남의 코드는 읽었을 때 곧장 의도하는 것이 무엇인지 이해할 수 없다면 즉각 이해하기 쉽도록 다시 작성하자.

> 주석 없이도 읽을 수 있을 법한 이해하기 쉬운 코드를 작성하는 것이 이상적이다. 다만 코드는 어디까지나 what과 how, 즉 '무엇을 하는지'와 '어떻게 하는지'밖에 표현하지 못한다. why, 즉 '어째서 그것을 하는지'를 표현하려면 주석을 사용할 필요가 있다.

> 문학적 프로그래밍에는 다음과 같은 이점이 있다.
> * 문서를 별도로 작성하지 않기 때무에 코드 설명이나 정당성의 근거를 기술하면서 프로그래밍을 진행할 수 있고, 프로그래머가 코드에 관해 다른 관점에서 생각해 볼 수 있다.
> * 코드와 설명이 가까운 위치에 기술되므로 수정 작업이 쉽고, 코드를 변경할 때 설명이 적절하게 갱신된다.
> * 전체적으로 코드 기반 문서가 단 하나만 존재하는 것이 보증된다.
> * 일반적인 주석에는 기술되지 않는 종류의 정보(알고리즘의 설명, 정당성을 입증한 증명, 설계상의 결정 근거 등)도 코드에 포함된다.

## 2.5 SLAP
> Single Level of Abstraction Principle

> 코드를 작성할 때 높은 수준의 추상화 개념과 낮은 수준의 추상화 개념을 분리한다.
> 코드의 추상화 수준을 일치시키면 훌륭한 책처럼 읽을 수 있다.

> 코드사 수준이 일치된 함수로 분할되어 있으면 요약성과 열람성을 동시에 충족한다. 함수 일람은 목차가 되고 요약성을 지닌다. 분할된 함수는 작은 코드 묶음이 되어 열람성이 좋아진다. 코드를 읽다가 갑자기 추상도가 달라지면 흐름이 도중에 끊긴다.