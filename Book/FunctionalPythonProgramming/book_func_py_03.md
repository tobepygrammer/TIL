# 3. 함수, 반복자, 제네레이터

> 함수형 프로그래밍의 핵심은 순수 함수를 사용해 입력 정의역(domain)의 값을 출력 치역(range)의 값으로 바꾸는 것이다.

> 함수적인 시각에서 다음과 같은 파이썬의 기능을 몇 가지 살펴본다.
> * 부수 효과가 없는 순수 함수
> * 인자로 넘길 수 있거나 함수의 결과로 반환할 수 있는 객체인 함수
> * 객체지향적인 후위 표기법이나 전위 표기법으로 파이썬 문자열 사용
> * tuple이나 namedtuple을 사용해 상태가 없는 객체 생성
> * 반복 가능한 컬렉션을 함수형 프로그래밍을 위한 주 설계 도구로 활용

> 함수형 파이선을 작성하려면 튜플과 변경할 수 없는 컬렉션에 초점을 맞출 필요가 있다.

## 순수 함수 작성하기
> 자유 변수(free variable)를 사용해 파이썬의 전역에 있는 값을 참조하면 매개변수를 적절히 사용해 처리할 수 있다.

```py
def some_function(a, b, t):
    return a + b * t + global_adjustment
```
> 이 함수를 리팩토링하여 global_adjustment 변수를 적절한 매개변수로 바꿀 수 있다. 다만, 이 함수에 대한 모든 참조를 바꿀 필요가 있다. 그러한 작업은 복잡한 애플리케이션에서 많은 파장이 있을 수 있다. 전역 참조는 함수의 본문에서 자유 변수처럼 보일 것이다. 그러한 자유 변수에 대한 대입문도 없고, 그에 해당하는 매개변수도 없다면, 이를 전역 변수로 생각할 만한 여지가 충분하다.

의미를 명확히 잘 모르겠다. 책은 한번 다 보고 다시 보면 좀 더 이해가 될까?

> 상태가 있는 파이썬 내부 객체는 많다. file 클래스의 인스턴스나 모든 파일의 유사한 객체들은 자주 사용되는 상태가 있는 객체의 예다. 우리는 파이썬에서 상태가 있는 객체를 사용하는 대부분의 경우는 일반적으로 컨택스트 관리자(context manager)처럼 동작한다는 사실을 알고 있다. 모든 개발자들이 이러한 컨택스트 관리자를 활용하지는 않지만, 수많은 객체가 컨택스트 관리자에 필요한 인터페이스를 구현한다. 상태가 있는 객체가 컨택스트 관리자 인터페이스를 완전히 구현하지 않은 경우도 약간은 존재한다. 그러한 경우에는 보통 close() 메서드가 존재한다. 이러한 객체를 contextlib.closing() 함수에 사용하여 적절한 컨텍스트 관리자 인터페이스를 제공하도록 만들 수도 있다.

> 상태가 있는 파일 객체 사용을 잘 정의된 영역 안으로 제한할 수 있는 with문을 사용해야 한다.

```py
def open(iname, oname):
    global ifile, ofile
    ifile = open(iname, "r")
    ofile = open(oname, "w")
```

> 이는 그리 좋은 설계가 아니며, 그러한 방식을 피해야만 한다. 파일은 함수에 제공되는 매개변수여야 하며, 열린 파일은 with문으로 감싸서 상태에 따른 동작을 제대로 처리하도록 해야 한다.

## 일급 계층 객체인 함수
> 호출 가능한 객체를 사용해 함수를 만들 수도 있다. 호출 가능한 객체도 일급 계층 객체다. 심지어 호출 가능 클래스 정의를 고차 함수라고 생각할 수도 있다. 호출 가능 객체에서 `__init__()` 메서드를 사용하는 방식을 신중하게 생각할 필요가 있다. 즉, 상태가 있는 클래스 변수를 피해야 한다. 한 가지 일반적인 응용방법은 `__init__()` 메서드를 사용해 전략 디자인 패턴(Strategy design pattern)에 부합하는 객체를 만드는 것이다.

> 전략 디자인 패턴을 따르는 클래스는 알고리즘이나 알고리즘의 일부를 제공하는 다른 객체에 의존한다. 이 패턴은 알고리즘의 자세한 부분을 클래스 안에 컴파일 해 넣는 대신, 실행 시점에 알고리즘의 세부 사항을 주입할 수 있게 해준다.

```py
import collections
class Mersenne1(collections.Callable):
    def __init__(self, algorithm):
        self.pow2 = algorithm
    def __call__(self, arg):
        return self.pow2(arg)-1
```

> 이 클래스는 `__init__()`를 사용해 다른 함수에 대한 참조를 저장한다. 하지만 아무런 상태가 있는 인스턴스 변수를 만들지 않는다.

> 다음의 함수는 주어진 인자만큼 2를 거듭제곱한다.
```py
def shifty(b):
    return 1 << b

def multy(b):
    if b == 0:
        return 1
    return 2*multy(b-1)

def faster(b):
    if b == 0:
        return 1
    if b%2 == 1:
        return 2*faster(b-1)
    t = faster(b//2)
    return t*t
```
multy함수를 제외하고는 하나도 이해 못하겠다 ㅠㅠ

> shift() 한수는 비트 왼쪽 시프트 연산을 사용해 2의 거듭제곱을 계산한다. multy() 함수는 단순한 재귀 곱셈을 사용한다. faster() 함수는 분할 정복 전략을 사용해 b번이 아니라 log2(b)번의 곱셈을 수행한다.

> 알고리즘 전략을 내장한 Mersenne1 클래스의 인스턴스는 다음과 같이 만들 수 있다.
```py
m1s = Mersenne1(shifty)
m1m = Mersenne1(multy)
m1f = Mersenne1(faster)
```

> 이는 결과는 같지만, 서로 다른 알고리즘을 사용하는 다양한 함수를 만드는 방법을 보여준다.

전략 디자인 패턴도 잘 사용한다면 활용법이 많을 것 같다. 어떤 일련의 과정이 있고 이 과정에 특정 함수를 포함시켜야 한다면 좋은 방법이 될 것 같다.

예를들어 데이터를 전송하고 복잡한 로그를 남겨야 하는 작업이 있다. 로그를 남기는 작업은 class에 포함시키고 전송방식을 각 함수로 만들어 전략으로 포함시킨다. 그것들을 각각 ftp_send(), mail_send() 등으로 이름을 붙여 함수로 만들어 놓고 매개변수를 이용하여 전달하면 ftp와 mail을 구분하기 위핸 매개변수 하나를 줄일 수 있고 함수이름으로 더 명확하게 코드를 작성할 수 있다고 생각된다.

## 문자열 사용하기
> 파이선 문자열은 변경 불가능하기 때문에 함수형 프로그래밍 객체의 좋은 예라고 할 수 있다. 파이썬의 string 모듈에는 많은 메서드가 들어 있고, 그들 모두는 결과로써 새로운 문자열을 내놓는다. 이러한 메서드는 부수 효과가 없는 순수 함수다.

> 전위 방식인 대두분의 함수와 달리 string 메서드 함수들을 사용하는 구문은 후위 방식이다. 이는 복잡한 문자열 연산이 일반적인 함수와 혼합되는 경우, 가독성이 떨어진다는 의미다.

```py
from decimal import *
def clean_decimal(text):
    if text is None:
        return text
    try:
        return Decimal(text.replace('$', '').replace(',', ''))
    except InvalidOperation:
        return text
```
> 이 함수는 $와 , 문자열을 제거하기 위해 replace를 두 번 호출한다. 그 결과로 얻은 문자열을 Decimal 클래스 생성자의 인자로 전달하고, 그 결과로 생긴 객체를 반환한다.

> 이를 더욱 일관성 있게 만들려면 다음과 같이 string의 메서드 함수를 처리하는 전위 연산자 함수를 정의해야 한다.
```py
def replace(data, a, b):
    return data.replace(a, b)
```

> 이러한 함수가 있으면 좀 더 일관성 있는 전위 연산자 형태의 `Decimal(replace(replace(text, '$', ''), ',', ''))` 이라는 구문을 사용할 수 있다.

> 매우 단순한 경우에는 다음과 같이 같은 목적을 쉽게 달성할 수 있다.
```py
replace = str.replace
replace('$12.45', '$', '')
# 12.45
```

> 이러한 일관성이 전위와 후위 연산을 섞어 사용하는 것에 비해 얼마나 큰 개선인지는 분명하지 않아 보인다. 인자가 많은 함수의 문제는 인자들이 식의 여기 저기에 나타날 수 있다는 점이다.

> 좀 더 나은 접근 방식은 다음과 비슷하게 구분 기호를 정리해주는 전위 연산자 방식의 함수를 정의하는 것이다.

```py
def remove(str, chars):
    if chars:
        return remove(str.replace(chars[0], ''), chars[1:])
    return str
```

> 이 함수는 재귀적으로 char 변수에 있는 글자들을 str에서 제거한다. 이를 `Decimal(remove(text, '$,'))`와 같이 사용하면 문자열을 정리하는 목적을 더욱 잘 드러낼 수 있다.

## tuple과 namedtuple 사용하기
> 파이썬의 tuple은 변경 불가능한 객체다. 이 또한 함수형 프로그래밍에 적합한 객체에 대한 훌륭한 예다. 파이썬의 tuple은 소수의 메서드 함수만 제공하기 때문에 거의 대부분의 작업은 전위 문법을 사용한 함수를 통해 이뤄진다. 튜플을 사용하는 예는 다양하지만, 특히 튜플의 리스트, 튜플의 튜플, 튜플들을 만들어내는 제네레이터 등을 자주 사용한다.

> 3-튜플의 시퀀스를 사용하면서, 3-튜플의 각 원소가 적색, 녹색, 청색을 표현한다고 가정할 수도 있다. 3-튜플에서 값을 가져오기 위해 다음과 같은 함수를 사용할 수 있다.

```py
red = lambda color: color[0]
green = lambda color: color[1]
blue = lambda color: color[2]
```

> 다음과 같이 이름 있는 튜플을 사용할 수도 있다.

```py
Color = namedtuple("Color", ('red', 'green', 'blue', 'name'))
```
> 이렇게 하면 red(item) 대신 item.red를 사용할 수 있다.

```py
from collections import namedtuple
Color = namedtuple('Color', ('red', 'green', 'blue', 'name'))
red = Color(255, 0, 0, 'myred')
print(red.red)
print(red.name)
```

튜플이나 리스트의 특정 요소를 사용할 때 인덱스를 이용했었는데 위처럼 이름을 붙여놓고 사용하는 것이 더 읽기 좋은 코드를 만들 수 있을 것 같다.

## 제네레이터
> * 제네레이터는 컬렉션의 크기를 알아야 할 필요가 있는 len()과 같은 함수의 경우를 제외하고는 시퀀스와 비슷해 보인다.
> * 제네레이터는 오직 한 번만 사용할 수 있다. 일단 사용하고 나면 제네레이터는 비어 있는 것처럼 보인다.

```py
def pfactorsl(x):
    if x % 2 == 0:
        yield 2
        if x / 2 > 1:
            yield from pfactorsl(x/2)
        return
    for i in range(3, int(math.sqrt(x)+0.5)+1, 2):
        if x % i == 0:
            yield i
            if x / i > 1:
                yield from pfactorsl(x/i)
            return
    yield x
```
> 주어진 수의 소인수(prime factor)를 구한다. 주어진 x가 짝수라면 2를 내놓고, 그 후에 X / 2의 소인수를 내놓는다.

> 홀수라면 3이상의 홀수에 대해 루프를 돌면서 주어진 인수를 찾는다. 어떤 인수를 찾으면 그 인수 i를 내놓고, 그 후 재귀적으로 x / i의 소인수를 찾는다.

> 인수를 전혀 찾을 수 없는 경우에는 주어진 수가 소수임에 틀림없다. 따라서 그 수를 내놓을 수 있다.

> 소인수 중에 2를 따로 처리하여 전체 반복 회수를 절반으로 줄인다. 2를 제외한 모든 소수는 홀수이기 때문이다.

> 재귀 외에도 중요한 for 루프를 하나 더 사용했다. 그 루프를 통해 인수가 1,000개인 경우까지 쉽게 처리할 수 있다. 이 수는 최소한 2^1000, 즉 300자리 숫자 이상의 크기를 가지고 있다. for에서 사용하는 i변수를 그 루프 바깥에서는 사용하지 않기 때문에 루프에 대해 변경을 가하더라도 i가 가지는 상태가 바깥에 영향을 끼치는 일은 없다.

> 결과적으로 우리는 꼬리재귀 최적화를 수행한 셈이다. 재귀호출은 3부터 루트x까지 를 센다. for 루프는 그 범위 안에 있는 모든 수를 하나하나 검토하는 재귀호출을 하지 않고도 원하는 바를 이룰 수 있게 해준다. for 루프는 단지 재귀함수의 결과인 반복 가능 객체를 소비하기 위한 것이다.

> 재귀적인 제네레이터 함수에서는 return 문을 사용할 때 조심해야 한다. 다음과 같은 문장을 사용하면 안 된다.
```py
return recursive_iter(args)
```

잘 이해되지 않는다. recursive_iter()가 제네레이터를 의하는가? 아니면 재귀함수를 의미하는가? 아래의 대안을보면 재귀함수보다는 제네레이터를 의미하는 것 같은데... 맞나?

> 이와 같이 하면 제네레이터 객체를 반환하기만 하고, 만들어진 값을 반환하도록 함수를 평가하지는 않는다. 따라서 다음과 같은 방식을 사용해야 한다.

```py
for result in recursive_iter(args):
    yield result
```

```py
yield from recursive_iter(args)
```

(순수 재귀로 작성한 소인수 분해 pass)

## 제네레이터의 한계
> itertools.tee() 메서드를 사용하면 한 번밖에 사용하지 못하는 한계를 넘어설 수 있다.

tee의 의미가 뭘까? 왜 메서드의 의미를 tee라고 붙였을까?

```py
import itertools
def limits(iterable):
    max_tee, min_tee = itertools.tee(iterable, 2)
    return max(max_tee), min(min_tee)
```

> 매개변수로 받은 제네레이터 식을 복제하여 max_tee()와 min_tee()를 만들었다. 이렇게 하면 원래의 반복자를 그대로 남는다. 이 두 객체를 사용하면 해당 반복 가능 객체를 가지고 최댓값과 최솟값을 얻을 수 있다.

```py
import itertools

div_3_5 = ( x for x in range(100) if x%3==0 and x%5==0 )

def limits(iterable):
    max_tee, min_tee = itertools.tee(iterable, 2)
    print(max(max_tee), min(min_tee))

limits(div_3_5)
```
max_tee, min_tee도 한 번 사용하면 다시 사용 못한다.

itertools.tee로 복제하더라도 원본(여기서는 iterable)을 사용하면 복사본을 사용하지 못한다.

## 제네레이터 식 조합하기
> 제네레이터 함수를 결합하는 일반적인 방법은 합성 함수(composite function)를 만드는 경우다. 
```py
(f(x) for x in range())
```
> 위와같은 식을 계산하는 제네레이터가 있다고 가정해보자. 이때 g(f(x))를 계산하고 싶다.

> 원래의 제네레이터 식을 다음과 같이 약간 변형할 수 있다.

```py
g_f_x = (g(f(x)) for x in range())
```
>기술적으로는 문제가 없지만, 이는 재사용을 막는다. 식을 재사용하기보다는 코드를 재작성하기 때문이다.

> 다음과 같이 한 가지 식을 다른 식 안에서 바꿀 수도 있다.

```py
g_f_x = (g(y) for y in (f(x) for x in range()))
```

> 이러한 방식을 사용하면 단순한 치환으로 조합이 가능하다는 장점이 있다. 이를 재사용을 강조하는 방식으로 작성하는 방법은 다음과 같다.

```py
f_x = (f(x) for x in range())
g_f_x = (g(y) for y in f_x)
```

> 이 방법은 최초의 식인 (f(x) for x in range())에 손을 대지 않는다는 장점이 있다. 우리는 그냥 해당 식을 변수에 대입했을 뿐이다.

> 그 결과로 얻은 합성 함수도 제네레이터 식이다. 따라서 이 또한 지연 계산을 수행한다. 이는 g_f_x에서 다음 값을 가져오면 f_x 에서도 값을 하나 가져오고, 다시 원래의 range() 함수에서도 값을 하나 추출하게 된다는 의미다.

그래 좋다. 이런 조합이 있다는 것을 알았다. 이것을 어디에 활용할 수 있을까?

## 제네레이터 함수를 사용해 원자료 정리하기
> ... 다음과 같이 데이터에 대한 반복자를 정의할 수 있다.

```py
import csv
def row_iter(source):
    return csv.reader(source, delimiter="\t")
```
> 단순히 파일을 csv.reader 함수로 감싸서 각 행을 돌려주는 반복자를 만들 수 있다. 이 반복자는 다음과 같은 컨텍스트에서 사용할 수 있다.

csv.reader()는 실체화된 데이터를 반환하지 않는다. _csv.reader 객체를 반환한다. 이것은 일종의 제네레이터다.

```py
with open("Anscombe.txt") as source:
    print( list(row_iter(source)))
```

> 첫 세줄을 걸러낸다. 다음은 세 줄을 깔끔하게 가져오고, 나머지 줄만 포함하고 있는 반복자를 반환하는 함수다.

```py
def head_split_fixed(row_iter):
    title = next(row_iter)
    assert len(title) == 1 and title[0] == "A's q"
    heading = next(row_iter)
    assert len(heading) == 4 and heading == ['1', '2', '3', '4']
    columns = next(row_iter)
    assert len(columns) == 8 and columes == ['x', 'y']
    return row_iter
```

> 이 함수는 반복 가능 객체에서 세 줄을 없앤다. 이때 각 줄에 원하는 내용이 있는지를 assert로 확인한다. 해당 파일에 그러한 정보가 없다면 해당 파일이 오염됐거나 우리가 잘못된 파일을 대상으로 분석을 시도하고 있다는 것을 나타내는 징후일 것이다.

> row_iter()나 head_split_fixed() 함수는 모두 인자값으로 반복 가능한 객체를 요구한다. 따라서 이를 다음과 같이 쉽게 결합할 수 잇다. 

```py
with open("A's q") as source:
    print( list(head_split_fixed(row_iter(soruce))))
```

> 한 반복자를 다른 반복자의 결과에 적용했을 뿐이다. 실제로 이는 합성 함수를 정의하는 것과 같다.

## list, dict, set 사용하기
> list와 같은 파이썬 시퀀스 객체는 반복 가능하다. 하지만 이 외에도 여러가지 기능을 제공한다. 이를 실체화한 반복 가능 객체로 생각할 수 있다.

> 파이썬에서 리스트 디스플레이는 제네레이터를 실체화하기 위한 구문을 제공한다. 단지 []만을 추가하면 된다. 이는 제네레이터 식과 리스트 내장을 구분해주는 요소다. 하지만 실용적인 중요성은 그리 크지 않다.

> list(range(10)) 함수는 제네레이터 식을 평가한다. [range(10)] 리스트 리터럴은 제네레이터 함수를 평가하지 않는다.

> list, dict, set에 대해 []과 {}를 사용하는 리터럴 구문이 있는 반면, 튜플에 대한 것은 없다. 튜플을 실체화하려면 tuple() 함수를 사용해야만 한다. 이러한 이유로 list(), tuple(), set() 함수를 더 선호하는 편이 가장 일관성이 있어 보인다.

```py
from collections import namedtuple
import csv

def row_iter(source):
        return csv.reader(source, delimiter="\t")

def head_split_fixed(row_iter):
    title = next(row_iter)
    assert len(title) == 1 and title[0] == "Anscombe's quartet"
    header = next(row_iter)
    assert len(header) == 4 and header == ['I', 'II', 'III', 'IV']
    columns = next(row_iter)
    assert len(columns) == 8 and\
        columns == ['x', 'y', 'x', 'y', 'x', 'y', 'x', 'y']

    return row_iter

Pair = namedtuple('Pair', ('x', 'y'))
def series(n, row_iter):
    for row in row_iter:
        yield Pair(*row[n*2:n*2+2])

with open("Anscombe.txt") as source:
    data = tuple(head_split_fixed(row_iter(source)))
    sample_I = tuple(series(0, data))
    sample_II = tuple(series(1, data))
    sample_III = tuple(series(2, data))
    sample_IV = tuple(series(3, data))
    print(data)
    print(sample_I)

```

> head_split_fixed()와 row_iter()를 합성한 함수에 tuple()함수를 적용한다. 이렇게 하면 다른 함수에서 사용할 객체를 만들 것이다. tuple 객체를 실체화하지 않으면 오직 첫 번째 샘플에만 데이터가 들어갈 것이다. 그 후 원본 반복자를 모두 소모하기 때문에 그 반복자에 대한 나머지 접근은 모두 빈 시퀀스를 만들어 낼 것이다.

```py
mean = sum(float(pair.y) for pair in sample_I) / len(sample_I)
```

> 이는 각 Pair 객체의 y 값 평균을 계산한다.

```py
for subset in sample_I, sample_II, sample_III, sample_IV:
    mean = sum(float(pair.y) for pair in subset) / len(subset)
```
> 메모리 사용을 줄이기 위해(성능은 향상됨) 우리는 가능한 제네레이터 식과 함수를 더 선호한다. 이러한 식으로 컬렉션에 대한 반복자를 오직 한 번만 사용할 수 있기 때문에 때로는 컬렉션을 tuple로 실체화시켜야 할 수도 있다. 컬렉션을 실체화하려면 메모리가 필요하고, 시간도 소요되기 때문에 주의를 기울여야 한다.

## 상태가 있는 매핑 사용하기
> 파이썬의 dict 클래스에 포함된 여러 매핑과 collections 모듈에 정의된 여러 매핑 등과 같이 몇 가지 상태가 있는 컬렉션을 제공한다. 이러한 매핑은 상태가 있기 때문에 조심스럽게 사용해야 한다.

> 파이썬을 사용해 함수형 프로그래밍 기법을 배우려는 목적 아래 매핑을 사용하는 방법은 두 가지가 있다.

> 첫 번째는 매핑을 누적시키는 상태가 있는 딕셔너리로 사용하는 방식이고, 두 번째는 고정시킨(frozen) 딕셔너리다. 

> 파이썬에서는 사용하기 쉽거나 변경 불가능한 매핑을 제공하지 않는다. collenctions.abc.Mapping 추상 클래스는 변경 불가능하지만, 쉽게 사용할 만한 것은 못된다.

사용하기 쉬우면서 변경 불가능한 매핑을 제공하지 않는다는 의미인것 같다.

collections.abc.Mapping은 뭘까?

> collections.abc.Mapping 추상 클래스를 사용하는 방식으로 엄밀성을 추구하는 대신, 우리는 ns_map을 대입문의 좌변에 오직 한 번만 사용하고, ns_map.update()나 ns.map.pop()과 같은 메서드를 결코 사용하지 않는 방식으로 돌아길 것이다. 또한 맵에 들어 있는 원소를 대상으로 del을 사용하지도 않을 것이다.

> 상태가 있는 딕셔너리의 전형적인 용례는 다음 두 가지 범주에 들어간다.
> * 일단 만들어진 다음에는 결코 변하지 않는 딕셔너리. 이 경우, 우리는 dict의 해시 키 기능을 활용하여 성능을 회적화할 수 있다. dict(시퀀스)를 사용하면 (키, 값)2-튜플의 반복 가능한 시퀀스로부터 딕셔너리를 생성할 수 있다.
> * 점진적으로 만들어지는 딕셔너리. 이는 리스트 객체를 실체화하고 정렬하는 대신에 사용할 수 있는 최적화다. 점진적으로 맵을 만드는 것은 메모이제이션(memoization)시에 유용하다.

> 첫 번째 예제인 딕셔너리를 한 번에 만드는 것은 입력을 수집하고, dict 객체를 만들며, 그 딕셔너리에 있는 매핑을 바탕으로 입력 데이터를 처리하는 세 가지 작동 단계로 이뤄진다. 이러한 종류의 애플리케이션 에로는 이름과 (R, G, B)3-튜플로 이뤄진 특정 색 팔레트를 사용하는 이미지 처리를 들 수 있다.

> Color라는 이름 있는 튜플을 사용한다고 가정해보자.

```py
from collections import namedtuple
Color = namedtuple('Color', ('red', 'green', 'blue', 'name'))

(Color(red=239, green=222, blue=205, name='Almond'), Color(...))
```

> 주어진 색의 이름을 빠르게 찾기 위해 이 시퀀스로부터 고정시킨 딕셔너리를 만들 것이다. 

> 튜플에서 매핑을 만들어 내기 위해서는 처리(감싸기(반복 가능 객체))라는 디자인 패턴을 사용해야 한다.

```py
name_map = dict( (c.name, c) for c in sequence)
```

> 순서는 보장할 수 없다.

> 매핑을 실체화했으므로 dict() 객체를 색 이름에서 (R, G, B) 색으로 변환하는 처리에 반복적으로 사용할 수 있다. 딕셔너리가 키를 해시값으로 빠르게 변환하여 검색을 수행하기 때문에 색 이름 검색은 매우 빠르게 수행될 수 있다.

## bisect 모듈을 사용해 매핑 만들기
> bisect 모듈을 사용한다는 것은 정렬된 객체를 만들고, 나중에 그 객체를 검색에 활용한다는 의미다. 이때 dict 매핑과 완전히 호환되도록 하기 위해 collections.abc.Mapping을 기반 클래스로 사용할 수도 있다.

> dict 매핑은 해시를 사용해 원소의 위치를 거의 즉각적으로 정할 수 있다. 하지만 이를 위해서는 상당히 큰 메모리를 할당해야 한다. bisect 매핑은 검색을 수행하되, 매핑에서만큼 많은 메모리를 소비하지 않지만, 성능은 거의 즉각적이라 할 만큼 좋다.

> static 매핑 클래스는 다음과 같다.
```py
import bisect
from collections.abc import Mapping

class staticMapping(Mapping):
    def __init__(self, iterable):
        self._data = tuple(iterable)
        self._keys = tuple(sorted(key for key, _ in self._data))
    def __getitem__(self, key):
        ix = bisect.bisect_left(self.keys, key)
        if ix != len(self._keys) and self._keys[ix] == key:
            return self._data[ix][1]
        raise ValueError("{0!r} not found".format(key))
    def __iter__(self):
        return iter(self._keys)
    def __len__(self):
        return len(slef._keys)
```
> 이 클래스는 추상 상위 클래스인 collection.abc.Mapping을 확장한다. 이 클래스는 초기화 메서드를 제공하고, 추상 클래스를 상속할 때 구현해야만 하는 세 가지 함수를 구현한다.

> `__getitem__()` 메서드는 bisect.bisect_left() 함수를 사용해 키의 컬렉션을 검색한다. 키를 찾은 경우에는 그에 따른 값을 반환한다.

> `__iter__()` 메서드는 상위 클래스의 요구사항에 맞춰 반복자를 반환한다.

> `__len__()` 메서드도 이와 마찬가지로 요구에 맞게 컬렉션의 길이를 제공한다.

StaticMapping class의 객체를 생성하여 테스트해보자. 꼭.

## 상태가 있는 집합 사용하기
> 파이썬은 집합 컬렉션을 포함한 몇 가지 상태가 있는 컬렉션을 지원한다. 집합을 우리의 목적에 적합하게 사용할 수 있는 경우는 두 가지이다. 한 가지는 원소를 누적시키는 상태가 있는 집합으로 사용하는 것이고, 다른 한 가지는 원소를 빠르게 검색하기 위해 고정시킨 집합으로 사용하는 것이다.

> tuple 객체를 만들어 내는 것과 마찬가지 방식을 forzenset(반복 가능 객체)이라는 문장을 사용하면 고정시킨 집합을 만들 수 있다. 이렇게 하면 매우 빠른 in 연산자를 제공하는 구조를 만들어 낸다. 이를 사용해 데이터를 수집한 후 고정시킨 집합을 만들고 그 집합을 사용해 데이터를 처리할 수 있다.
