# 4. 컬렉션으로 작업하기
> 파이썬은 전체 컬렉션을 처리하는 여러 함수를 제공한다. 이들을 시퀀스(리스트나 튜플), 집합, 매핑, 제네레이터 식의 결과인 반복 가능 객체에 적용할 수 있다.

> 이번 장에서는 다음 함수를 컬렉션과 함께 사용하는 방법은 살펴본다.
> * any() 와 all()
> * len(), sum() 및 이러한 함수와 관련 있는 고차 통계 처리 방법
> * zip() 및 이와 관련 있는 데이터의 리스트를 조직화하거나 펼치는 기법
> * reversed()
> * enumerate()

> 첫 네 함수는 모두 축약(reduction)이라 부를 수 있다. 이들은 컬렉션을 단일 값으로 줄여주기 때문이다. 다른 세 가지 함수(zip(), reversed(), enumerate())는 매핑이다. 이들은 기존의 컬렉션에서 새 컬렉션을 만든다. 다음 장에서는 처리를 특화하기 위해 함수를 인자로 받는 몇몇 mapping()과 reduction() 함수들을 살펴본다.

## 함수의 다양성에 대한 정리
> 우리는 다음과 같이 함수를 두 가지 넓은 부류로 구별해야 한다.
> * 스칼라(scalar) 함수는 개별 값에 적용할 수 있고, 개별적인 결과를 내놓는다. abs(), pow() 등의 함수나 math 모듈에 있는 모든 함수들이 바로 스칼라 함수의 예다.
> * Collection() 함수는 반복 가능한 컬렉션에 작용한다.

> 컬렉션 함수는 다음과 같이 세 가지로 나눌 수 있다.
> * 축약(reduction): 컬렉션에 있는 여러 값을 함수를 사용해 겹쳐 호출함으로써 최종적으로는 단일 값을 만들어 낸다. 이를 종합(aggregate) 함수라 부르기도 한다. 왜냐하면 입력 컬렉션의 여러 값을 하나로 종합한 값을 반환하기 때문이다.
> * 매핑(mapping): 함수를 컬렉션의 모든 원소에 적용한다. 결과는 입력 컬렉션과 크기가 같은, 다른 컬렉션이다.
> * 걸러내기(filter): 컬렉션의 모든 원소에 함수를 적용하여 일부 원소는 버리고 일부 원소는 통과시키는 것이다. 결과는 입력 컬렉션의 부분 집합이다. 걸러내는 함수를 호출해도 아무 일도 일어나지 않을 수도 있다. 이 경우 걸러낸 출력과 입력이 같다.

## 반복 가능 객체로 작업하기
> 앞 장에서 언급한 것처럼, 우리는 종종 파이썬의 for 루프를 사용해 컬렉션에 대한 작업을 수행한다. 튜플, 리스트, 맵, 집합 등의 실체화한 컬렉션에 대해 작업하는 경우, for 루프는 상태를 명시적으로 관리한다. 이는 순수한 함수형 프로그래밍으로부터 벗어난 것이기는 하지만, 파이썬에 필요한 최적화를 반영하는 것이기도 하다.

> 우리가 for 문을 평가하는 과정에서 생기는 반복자 객체에만 상태가 한정되도록 보장할 수 있다면, 순수한 함수형 프로그래밍에서 크게 벗어나지 않은 상태에서 for문을 활용할 수 있을 것이다.

> for 루프를 사용하는 일반적인 응용으로는 `풀기(처리(감싸기(반복가능객체)))` 디자인 패턴을 들 수 있다. `감싸기()` 함수는 반복 가능 객체의 각 원소를 정렬하기 위한 키나 다른 값과 함께 2-튜플로 묶는다. 그 후 이 2-튜플을 바탕으로 처리를 수행한다. 마지막으로 `풀기()` 함수는 감싸기 위해 사용했던 (키 등의) 값을 버리고, 원래의 원소로 복원한다. 이러한 경우에 자주 사용하는 두 함수가 존재한다.

```py
fst = lambda x: x[0]
snd = lambda x: x[1]
```

> 다른 일반적인 패턴은 `감싸기(감싸기(감싸기()))`다. 이 경우, 우리는 간단한 튜플에서 시작하여 다른 결과를 추가한 후 좀 더 크고 복잡한 튜플을 만든다. 이러한 패턴을 변경한 것 중 흔히 사용하는 것으로는 `확장(확장(확장()))`이 있다. 여기서는 원래의 튜플을 감싸는 대신 한 번 확장할 때마다 새로운 namedtuple 인스턴스를 만들어낸다. 이 두 가지 모두 첨가 디자인 패턴(Accretion design pattern)이라 부를 수 있다.

## XML 파일 구문 분석하기
> XML 파일을 구문 분석하여 위경도 쌍을 가져오는 것부터 시작할 것이다. 이를 통해 파이썬에서 확실히 함수형이 아닌 기능을 감싸 값의 반복 가능한 시퀀스를 만드는 과정을 보여줄 것이다.

> xml.etree 모듈. 구문 분석한 결과인 ElementTree 객체에는 모든 값에 대해 방문할 수 있는 findall() 메서드가 들어있다.

> xml 파일에는 <Placemark> 태그가 여럿 들어 있다. 각각에는 Point가 있고, 그 내부에는 좌표 구조가 들어 있다. 이는 지리 정보가 담겨 있는 전형적인 키홀 마크업 언어(Keyhole Markup Language, KML)의 예다.

```py
import xml.etree.ElementTree as XML

def comma_split(text):
    return text.split(',')

def row_iter_kml(file_obj):
    ns_map = {
        'ns0': 'http://www.opengis.net/kml/2.2',
    }
    doc = XML.parse(file_obj)
    return (comma_split(coordinates.text)
            for coordinates in
            doc.findall('./ns0:Document/'
            'ns0:Placemark/ns0:Point/ns0:coordinates', ns_map))

data = row_iter_kml('kml.xml')
print(list(data))
```

> 이 함수는 보통 with문 안에서 이미 열려 있는 파일을 받는다. 하지만 XML 구문 분석기가 처리할 수 있는 파일과 유사한 객체라면 어떤 것이든 관계 없다. 이 함수에는 간단하고 정적인 dict 객체, ns_map이 들어 있고, 그 객체에는 우리가 찾고자 하는 XML 태그의 namespace 매핑 정보가 들어 있다. 이 딕셔너리를 XML의 ElementTree.findall() 메서드에서 사용할 것이다.

> 구문 분석의 핵심은 doc.findall() 으로 찾은 태그의 시퀀스를 사용하는 제네레이터 함수에 있다. 이 태그의 시퀀스를 comma_split() 함수로 처리하여 텍스트 값을 콤마로 구분한 구성 요소로 나눈다.

> comma_split() 함수는 문자열의 split() 메서드를 함수형으로 만든 것이다. 문법적인 균일성을 강조하기 위해 메서드를 함수로 둘러쌌다.

> 이 함수가 만들어 내는 결과는 데이터행의 반복 가능한 시퀀스다. 각 행에는 경로에 속하는 각 지점을 이루는 세 가지 문자열, 즉 latitude(위도), longitude(경도), altitude(고도)가 들어간다. 이 3-튜플은 아직 유용하지 않다. latitude, longitude를 구하는 한편, 각각을 더 유용한 부동 소수점 수의 값으로 바꾸는 처리를 좀 더 진행해야 한다.

> 결과 값은 동-서 경도, 남-북 위도, 그리고 고도다.

## 파일을 상위 수준에서 구문 분석하기
> * altitude를 없애고, 필요하면 latitude나 longitude만을 남기는 것
> * 순서를 (longitude, latitude)에서 (latitude, longitude)로 바꾸는 것
> 다음과 같이 도구 함수를 정의하면 더 일관된 구문을 사용해 이 두 가지 변환을 다룰 수 있다.

```py
def pick_lat_lon(lon, lat, alt):
    return lat, lon
```

```py
def let_lon_kml(row_iter):
    return (pick_lat_lon(*row) for row in row_iter)
```

> 좋은 함수형 설계를 사용하면 어떤 함수를 그와 동등한 다른 함수로 언제든지 바꿀 수 있다는 사실을 알아두는 것이 중요하다. 따라서 리팩토링이 매우 쉽다.

> 다음과 같은 처리를 사용해 파일을 분석하고 원하는 구조를 만들 것이다.

```py
with urllib.request.urlopen('file:./Winter%202012-2013.kml') as source:
    v1 = tuple(lat_lon_kml(row_iter_kml(source))
print(v1)
```

> urllib 명령을 사용해 원본을 열었다. 여기서는 원본이 로컬 파일이다. 원격 서버에 있는 KML 파일을 열 수도 있다. 이러한 방식으로 파일을 열때 우리의 목표는 데이터 원본이 무엇이든 같은 방식으로 처리할 수 있게 하는 것이다.

> 우리는 저수준 XML 구문 분석과 고수준 데이터 재구성을 명확히 분리했다. XML 구문 분석은 일반적인 문자열의 튜플 구조를 만들어 낸다. 이는 CSV 파서의 출력과도 호환 가능하다. 이를 통해 다양한 원본에서 가져온 데이터에 균일하게 사용할 수 있는 고수준 처리 코드를 만들 수 있다.

```py
import xml.etree.ElementTree as XML
import urllib.request

def comma_split(text):
    return text.split(',')

def row_iter_kml(file_obj):
    ns_map = {
        'ns0': 'http://www.opengis.net/kml/2.2',
    }
    doc = XML.parse(file_obj)
    return (comma_split(coordinates.text)
            for coordinates in
            doc.findall('./ns0:Document/'
            'ns0:Placemark/ns0:Point/ns0:coordinates', ns_map))

def pick_lat_lon(lon, lat, alt):
    return lat, lon

def lat_lon_kml(row_iter):
    return (pick_lat_lon(*row) for row in row_iter)

with urllib.request.urlopen('file:./kml.xml') as source:
    v1 = tuple(lat_lon_kml(row_iter_kml(source)))

print(v1)
```

urllib만 import 하면 request를 찾을 수 없다고 나온다.

urllib.request까지 import 해야한다.

## 시퀀스 원소를 둘씩 짝짓기
> 데이터 재구성의 요구사항 중 하나는 시퀀스에 있는 여러 점의 정보를 시작점-끝점 쌍으로 만드는 것이다. 시계열 분석을 수행하는 경우에는 좀 더 많은 개수의 정보를 묶어야 할 수도 있다. 여기서는 단지 연속된 두 값만을 묶는다.

> 쌍의 시퀀스에 대해 haversine 함수를 적용하면 각 쌍의 시작점에서 끝점에 이르는 거리를 계산할 수 있다.

```py
begin = next(iterable)
for end in iterable:
    compute_something(begin, end)
    begin = end
```
> 위 코드는 데이터의 각 부분을 시작-끝 쌍으로 처리한다. 하지만 처리 함수와 데이터를 재구성하는 루프가 너무 밀접하게 엮여 있다. 따라서 재사용이 필요이상으로 복잡해진다. 쌍을 만들어 내는 알고리즘과 compute_something()이 엮여 있기 때문에 쌍을 만들어 내는 알고리즘을 따로 떼어 테스트하기도 어렵다.

> 이런식으로 조합된 함수를 사용하면, 애플리케이션을 재설정할 수 있는 가능성도 줄어든다. compute_something()을 대신할 다른 구현을 주입할 수 있는 쉬운 방법도 없다. 추가로, 위 코드에는 명시적인 상태, 즉 begin 변수를 사용하기 때문에 일이 더 복잡해질 가능성도 있다. loop의 몸통에 기능을 추가하는 경우, 어떤 점을 미처 고려하지 못하여 begin 변수를 제대로 설정하는 것을 잊어버릴 수 있다. 또한 filter() 함수로 인해 begin 변수를 제대로 변경할 수 없게 만드는 오류를 발생시킬 수 있는 if 문이 들어갈 수도 있다.

> 단순한 쌍 만들기 함수를 분리하면 재사용성을 더 높일 수 있다.

```py
def pairs(iterable):
    def pair_from(head, iterable_tail):
        nxt = next(iterable_tail)
        yield head, nxt
        yield from pair_from(nxt, iterable_tail)
    try:
        return pair_from(next(iterable), iterable)
    except StopIteration:
        return
```
> 필수적인 함수는 내부의 pair_from() 함수다. 이 함수는 반복 가능 객체의 머리에 있는 원소와 반복 가능 객체 자체에 작용한다. 그 함수는 첫 번째 쌍을 내놓은 후 반복 가능 객체에서 다음 번 원소를 빼내고 자기 자신을 재귀적으로 호출하여 다른 쌍을 계속 만들어 내게 한다.

> 이 함수를 pairs() 함수에서 호출했다. pairs() 함수는 초기화를 제대로 하고, 종료를 표현하는 예외를 조용히 처리한다.

> 파이썬의 반복 가능 재귀에는 재귀의 결과를 제대로 소비하고 내보내기 위한 for 루프가 들어간다. 좀 더 간단해보이는 return pair_from(nxt, iterable_tail) 방식을 사용하면 반복 기능을 제대로 소비하지 못하고, 모든 값을 만들어 내지 못한다는 사실을 알게 될 것이다. 제네레이터 함수 안에서 재귀를 사용하려면 결과로 반환하는 반복 가능 객체가 값을 소비할 수 있도록 해주는 yield문이 필요하다. 이를 위해 yield from recursive_iter(args)를 사용하라. return recursive_iter(args)와 같은 것은 제네리이터 객체만을 반환한다. 따라서 해당 재귀함수를 평가하여 만들어진 값을 반환하지 못한다.

> 꼬리 호출 재귀를 최적화하기 위한 전략은 재귀를 제네레이터 식으로 바꾸는 것이다. 이 방식을 사용하면 재귀를 단순한 for 루프로 최적화할 수 있다.

```py
def legs(lat_lon_iter):
    begin = next(lat_lon_iter)
    for end in lat_lon_iter:
        yield begin, end
        begin = end
```

> 이 버전은 빠르고 스택의 한계에도 영향을 받지 않는다. 이 코드는 시퀀스 제네레이터가 발생시키는 모든 것으로부터 쌍을 만들기 때문에 시퀀스의 타입과 무관하게 잘 동작한다. 루프 내부에 다른 처리 함수가 없기 때문에 이 legs() 함수를 필요에 따라 재사용할 수 있다.

> 이 함수를 다음과 같은 쌍의 시퀀스를 발생시키는 것처럼 생각할 수 있다.
```
list[0:1], list[1:2], list[2:3], ..., list[-2:] 
```

> 이 함수를 다른 방식으로 정리하면 다음과 같다.
```py
zip(list, list[1:])
```

```py
import xml.etree.ElementTree as XML
import urllib.request

def comma_split(text):
    return text.split(',')

def row_iter_kml(file_obj):
    ns_map = {
        'ns0': 'http://www.opengis.net/kml/2.2',
    }
    doc = XML.parse(file_obj)
    return (comma_split(coordinates.text)
            for coordinates in
            doc.findall('./ns0:Document/'
            'ns0:Placemark/ns0:Point/ns0:coordinates', ns_map))

def pick_lat_lon(lon, lat, alt):
    return lat, lon

def lat_lon_kml(row_iter):
    return (pick_lat_lon(*row) for row in row_iter)

# with urllib.request.urlopen('file:./kml.xml') as source:
#     v1 = tuple(lat_lon_kml(row_iter_kml(source)))
# print(v1)

def pairs(iterable):
    def pair_from(head, iterable_tail):
        nxt = next(iterable_tail)
        yield head, nxt
        yield from pair_from(nxt, iterable_tail)
    try:
        return pair_from(next(iterable), iterable)
    except StopIteration:
        return

# with urllib.request.urlopen('file:./kml.xml') as source:
#     pair_tuple = tuple(pairs(lat_lon_kml(row_iter_kml(source))))
# print(pair_tuple)

def legs(lat_lon_iter):
    begin = next(lat_lon_iter)
    for end in lat_lon_iter:
        yield begin, end
        begin = end

with urllib.request.urlopen('file:./kml.xml') as source:
    pair_tuple = tuple(legs(lat_lon_kml(row_iter_kml(source))))
print(pair_tuple)


```

> 이해하는 데는 도움이 되지만, 이 두 가지 코드는 오직 시퀀스 객체에 대해서만 작동한다. legs()와 pairs() 함수는 시퀀스 객체를 포함해 모든 반복 가능 객체에 대해 잘 작동한다.

## iter() 함수를 명시적으로 사용하기
> 순수하게 함수적인 관점에서는 모든 반복 가능 객체들을 재귀함수로 처리할 수 있어야 하며, 상태는 재귀호출 스택뿐이어야 한다. 실용적으로 살펴보면, 파이썬의 반복 가능 객체들은 오직 다른 for 루프의 평가에만 참여한다. 일반적인 경우의 예로는 컬렉션과 반복 가능 객체가 있다.

> 컬렉션을 다루는 경우 for문은 반복자 객체를 만들어 낸다. 제네레이터 함수를 다루는 경우에는 제네레이터 함수가 반복자이며, 그 내부에 상태를 저장한다. 파이썬의 프로그래밍 관점에서 볼 때 이 둘은 서로 동등한 경우가 많다. 하지만 next() 함수를 꼭 호출해야만 하는 경우에는 그 둘이 완전히 동등하지 않다.

> legs() 함수는 반복 가능 객체에서 첫 원소를 얻기 위해 next()를 명시적으로 호출했다. 제네레이터 함수, 제네레이터 식 또는 다른 반복 가능 객체의 경우, 이는 잘 작동한다. 하지만 튜플이나 리스트와 같은 시퀀스 객체에서는 잘 작동하지 않는다.

```py
list(legs(x for x in range(3)))
# [(0, 1), (1, 2)]

list(legs([0, 1, 2]))
# TypeError: 'list' object is not an iterator

list(legs(iter([0, 1, 2])))
# [(0, 1), (1, 2)]
```

> 두 번째 경우가 제대로 작동하게 만들기 위해서는 명시적으로 list 객체에서 반복자를 만들어야 한다. 그렇게 하면 legs() 함수가 리스트의 첫 원소를 반복자를 통해 가져올 수 있다.

## 단순한 루프 확장하기
```py
def legs_filter(lat_lon_iter):
    begin = next(lat_lon_iter)
    for end in lat_lon_iter:
        if #some rule for rejecting:
            continue
        yield begin, end
        begin = end
```

> 다음 리팩토링은 루프에 새로운 매핑을 추가할 것이다. 설계가 발전해 나감에 따라 매핑이 추가되는 일이 흔하다. 우리의 경우에는 string의 시퀀스를 가지고 있다. 으를 나중에 사용하기 위해 float 값으로 바꿀 필요도 있다. 이는 상대적으로 단순한 매핑이지만 디자인 패턴을 보여줄 수 있다.

> 다음은 이러한 데이터 매핑을 처리하는 한 가지 방법이다. 제네레이터 함수를 둘러싼 제네레이터 식을 사용한다.

```py
print(tuple(legs((float(lat), float(lon)) for lat, lon in lat_lon_kml())))
```
> legs() 함수를 제네레이터 식에 적용한 후 lat_lon_kml()의 결과를 가지고 float 값을 만들었다. 이 코드는 뒤에서 앞으로 읽을 수 있다. lat_lon_kml()의 출력을 float 값의 쌍으로 변환한 후 이를 legs() 함수의 시퀀스로 변환한다.

> 이러한 식의 코드는 금방 복잡해진다. 여기서도 내포된 함수가 매우 많다. float(), legs() tuple() 함수를 제네레이터에 적용하고 있다. 복잡한 식을 리펙토링하는 일반적인 방법 중 하나는 제네레이터 식과 실체화한 컬렉션을 분리하는 것이다. 식을 다음과 같이 단순화할 수 있다.

```py
flt = ((float(lat), float(lon)) for lat, lon in lat_lon_kml())
print(tuple(legs(flt)))
```

> 적용할 만한 리팩토링이 또 한 가지 있다. 일반적으로, 데이터의 원본을 바꾸고 싶은 경우가 자주 있다. 우리 예제에서 lat_lon_kml() 함수는 식의 나머지에 단단히 엮여 있다. 이로 인해 다른 원본을 사용하고 싶은 경우 재활용이 어렵다.

> 재활용을 위해 float() 연산을 매개변수화하고 싶을 수도 있다. 이 경우 제네레이터 식을 함수로 정의할 수 있다. 처리의 일부를 그룹으로 묶기 위해 처리 고정의 일부를 별도의 함수로 뽑아낼 것이다 string 싸에서 float 쌍으로 변환하는 것은 특정 원본 데이터에만 적용할 수 있다. 이러한 복잡한 변환 함수를 다음과 같이 좀 더 단순한 함수로 만들 수도 있다.

```py
def float_from_pair(lat_lon_iter):
    return ((float(lat), float(lon)) for lat, lon in lat_lon_iter)
```

> 이 함수는 다음과 같은 문맥에서 활용할 수 있다.

```py
legs(float_from_pair(lat_lon_pair()))
```

## 제네레이터 식을 스칼라 함수에 적용하기
> 제네레이터가 아닌 함수를 스칼라(scalar) 함수라고 한다. 왜냐하면 함수가 단순한 스칼라(벡터가 아닌 단일 값)에 작용하기 때문이다. 데이터의 컬렉션을 작업하는 경우에는 스칼라 함수를 제네레이터 식에 내포시킬 것이다.

```py
from math import radians, sin, cos, sqrt, asin

MI = 3959
NM = 3440
KM = 6371

def haversine(point1, point2, R=NM):
    lat1, lon1 = point1
    lat2, lon2 = point2

    delta_lat = radians(lat2 - lat1)
    delta_lon = radians(lon2 - lon1)
    lat1 = radians(lat1)
    lat2 = radians(lat2)
    a = sin(delta_lat/2)**2 + cos(lat1) * con(lat2) * sin(delta_lon/2) ** 2
    c = 2*asin(sqrt(a))

    return R * c
```

> 다음은 KML 데이터에 우리가 만든 함수들을 적용하여 거리의 시퀀스를 계산하는 방법을 보여준다.
```py
trip = ((start, end, round(haversine(start, end), 4))
    for start, end in legs(float_from_pair(lat_lon_kml())))
for start, end, dist in trip:
    print(start, end, dist)
```

## any()와 all()을 축약으로 사용하기
> any()나 all() 함수는 boolean으로 축약하는 기능을 제공한다. 두 함수 모두 값의 컬렉션을 True나 False 값 중 한 값으로 축약한다. all() 함수는 모든 값이 True라는 것을 보장한다. any() 함수는 True인 값이 최소 하나 이상 있다는 것을 보장한다.

> 어떤 집합에 속하는 모든 x에 대해, 함수 Prime(x)가 참이다.
```py
all(isprime(x) for x in 어떤집합)
```
> 어떤 집합의 모든 원소가 소수는 아니다 == 어떤 집합에 소수가 아닌 원소가 적어도 하나 있다.

```py
not all(isprime(x) for x in 어떤집합)
any(not isprime(x) for x in 어떤집합)
```

> 이 둘은 동등하지만, 한쪽으로 다른 쪽보다 더 선호할 만한 이유는 두 가지이다. 성능과 명확성이 바로 그것이다. 성능은 거의 값다. 따라서 문제는 명확성으로 귀결된다.

> all() 함수는 값의 집합을 and로 축약하는 것이라고 설명할 수 있다. 결과는 주어진 값의 시퀀스를 and 연산자로 중첩시키는 것과 같다. 그와 비슷하게, any() 함수는 or로 축약하는 것이라 설명할 수 있다.

> 이러한 함수의 가장 극단적인 경우에 대해서도 알아야 한다. 시퀀스에 원소가 하나도 없다면 어떻게 해야 할까? all(())이나 all([])의 값은 무엇이어야 할까?

```py
all(())
# True

any(())
# False
```

## len()과 sum() 함수 사용하기
> len()과 sum() 함수는 두 가지 간단한 축약-시퀀스 안의 원소 개수와 모든 원소의 합계-을 제공한다.

> sum() 함수는 반복 가능 객체에 대해서도 작동한다. len() 함수는 반복 가능 객체에는 적용할 수 없다. 오직 시퀀스에만 적용할 수 있다. 각 함수의 구현상 비대칭성으로 인해 통계 알고리즘의 측면에서는 약간 어색한 부분이 생긴다.

> 빈 시퀀스의 경우, 각 함수는 덧샘의 항등원인 0을 반환한다.
```py
sum(())
# 0
```

## 통계에 합계와 원소 개수 활용하기
```py
def mean(iterable):
    return sum(iterable)/len(iterable)
```

> 우아하기는 하지만 이 정의가 모든 반복 가능 객체에 적용할 수 있는 것은 아니다. 이 정의는 시퀀스에만 적용할 수 있다.

> 실제로, 반복 가능 객체를 기반으로 평균이나 표준편차를 계산하는 것은 비효율적이다. 파이썬에서는 시퀀스 객체를 실체화하거나 좀 더 복잡한 연산으로 각 계산을 재구성해야만 한다.

> 다음은 꽤 우아한 평균과 표준편차 정의의 예다.

```py
import math
s0 = len(data) # sum(1 for x in data) # x**0
s1 = sum(data) # sum(x for x in data) # x**1
s2 = sum(x*x for x in data)

mean = s1/s0
stdev = math.sqrt(s2/s0 - (s1/s0)**2)
```

```py
def s0(data):
    return sum(1 for x in data)

def s1(data):
    return sum(x for x in data)

def s2(data):
    return sum(x*x for x  in data)

def mean(x):
    return s1(x)/s(0)

def stdev(x):
    return math.sqrt(s2(x)/s0(x) = (s1(x)/s0(x))**2)
```

다음은 두 표본 집합의 상관관계를 계산하는 방법을 보여준다.
```py
def corr(sample1, sample2):
    u_1, o_1 = mean(smaple1), stdev(sample1)
    u_2, o_2 = mean(smaple2), stdev(sample2)
    z_1 = (z(x, u_1, o_1) for x in sample1)
    z_2 = (z(x, u_2, o_2) for x in sample2)
    r = sum(zx1*zx2 for zx1, zx2 in zip(z_1, z_2) )/s0(sample1)
    return r
```

## zip()을 사용해 시퀀스를 구조화하거나 펼치기
> zip() 함수는 여러 반복자나 시퀀스의 값을 일정 간격으로 섞는다. 그 함수는 n개의 입력 반복자나 시퀀스로부터 n-튜플을 만들어 낸다.

> zip() 함수는 제네레이터다. 따라서 결과 컬렉션을 실체화하지 않는다.

> zip() 함수에는 몇 가지 고려해야 할 극단적인 상황이 존재한다. 동작 시 다음과 같은 상황에서 어떤 행동 방식을 보일 것인지 결정해야 한다.
> * 인자가 아예 없다면 어떻게 해야 할까?
> * 인자가 오직 하나만 주어진다면 어떻게 해야 할까?
> * 인자로 받은 두 시퀀스의 길이가 같지 않다면 어떻게 해야 할까?

> 축약(any(), all(), len(), sum())의 경우, 빈 시퀀스를 축악하는 경우에 항등원을 반환했다.

> 이러한 극단적인 상황에서도 어떤 종류의 반복 가능한 객체를 반환해야 한다.

```py
zip()
list(_)
# []
```
> 인자가 없는 zip() 함수는 제네레이터 함수지만 아무 원소도 들더 있지 않다는 것을 알 수 있다. 이는 출력이 반복 가능 객체여야 한다는 정의와 잘 들어맞는다.

```py
zip((1, 2, 3))
list(_)
# [(1,), (2,), (3,)]
```

> 이 경우 zip() 함수는 각 입력 값으로부터 튜플을 하나씩 만들어 냈다.

```py
list(zip((1, 2, 3), ('a', 'b')))
# [(1, 'a'), (2, 'b')]
```

> 이 결과는 논란의 여지가 있다. 왜 길이를 줄여야 할까? 더 짧은 리스트에서 부족한 원소를 None 값으로 채워넣지 말아야 할 이유가 있을까? 이러한 방식을 선택하는 zip() 함수의 또 다른 구현이 itertools 모듈에 있으며, 그 이름은 zip_longest()이다.

## 튜플로 묶은 시퀀스를 다시 풀기
> zip()을 뒤집을 수도 있다. 튜플의 컬렉션을 풀 수 있는 방법 몇 가지를 살펴본다.

> 첫 번째. 튜플의 시퀀스를 풀기 위해 제네레이터 함수를 사용할 수 있다.

```py
p0 = (x[0] for x in pairs)
p1 = (x[1] for x in pairs)

sum(p0 * p1 for p0, p1 in pairs)
```

## 시퀀스 펼치기
> 공백으로 구분된 숫자, 2 line

```py
(line.split() for line in file)

# [['2', '3', ...], ['31', '37', ...]]
```

> 모든 수가 한 시퀀스에 하나씩 따로 들어가 있기를 원한다면?

```py
(x for line in blocked for x in line)
```

```py
rows = [['1', '2', '3', '4'], ['11', '22', '33', '44']]
result = [x for row in rows for x in row]
print(result)
```

> 다음과 같이 쓰면 좀 더 이해하기가 쉬울 것이다.

```py
for line in blocked:
    for x in line:
        yield x
```

## 평평한 시퀀스 구조화하기
> 값이 한 리스트 안에 평면적으로 나열된 리스트를 하위 그룹으로 묶고 싶을 수도 있다.

```py
flat_iter = iter(flat)
(tuple(next(flat_iter) for i in range(5)) for row in range(len(flat)/5))
list(_)
```

> 시퀀스의 시퀀스를 만들어 내기 위한 2중 for 루프 바깥에서, 먼저 `flat_iter` 반복자를 만든다. `tuple(next(flat_iter) for i in range(5))` 식은 `flat_iter`에 있는 반복 가능 객체로부터 5-튜플을 만들어 낸다. 그 식이 내포된 제네레이터 식은 만들려고 하는 시퀀스의 시퀀스에 필요한 회수만큼 내포된 식을 반복 실행한다.

```py
rows = [['1', '2', '3', '4'],
        ['11', '22', '33', '44'],
        ['12', '23', '34', '45']]
result = [x for row in rows for x in row]
print(result)
# ['1', '2', '3', '4', '11', '22', '33', '44', '12', '23', '34', '45']

result_iter = iter(result)
tie_3 = [tuple(next(result_iter) for i in range(3))
         for row in range(int(len(result)/3))]
print(tie_3)
# [('1', '2', '3'), ('4', '11', '22'), ('33', '44', '12'), ('23', '34', '45')]
```

`range(len(result)/3)`은 `TypeError: 'float' object cannot be interpreted as an integer`가 발생한다. 형변환이나 `//` 연산자를 사용해야 한다.

`//`연산자는 나누기를 하면서 소수점 이하를 버린다.

> 이는 원리스트의 길이가 5의 배수인 경우에만 사용 가능하다. 그렇지 않은 경우에는 맨 마지막에 남는 5개 미만의 원소를 따로 처리할 필요가 있다.

> 이러한 식의 함수를 사용해 데이터를 크기가 같은 튜플로 나눌 수 있다. 다음 코드를 보면 마지막에 남는 원소를 어떻게 처리할 것인지도 알 수 있다.

```py
def group_by_seq(n, sequence):
    flat_iter = iter(squence)
    full_sized_items = list( tuple(next(flat_iter)
        for i in range(n))
            for row in range(len(sequence)//n) )
    trailer = tuple(flat_iter)
    if trailer:
        return full_sized_items + [trailer]
    else:
        return fill_sized_items
```
```py
def group_by_seq(n, sequence):
    flat_iter = iter(sequence)
    full_sized_items = list( tuple(next(flat_iter)
                       for i in range(n))
                       for row in range(len(sequence)//n) )
    trailer = tuple(flat_iter)
    if trailer:
        return full_sized_items + [trailer]
    else:
        return full_sized_items
print(group_by_seq(5, result))
# [('1', '2', '3', '4', '11'), ('22', '33', '44', '12', '23'), ('34', '45')]
```
> 각 튜플의 크기가 n인 리스트를 만든다. 남는 원소가 있다면 크기가 0보다 큰 튜플로 만들어 앞에서 만든 온전한 튜플의 리스트 뒤에 붙인다. 남는 원소가 없다면 아무 것도 하지 않는다.

```py
def group_by_iter(n, iterable):
    row = tuple(next(iterable) for i in range(n))
    while row:
        yield row
        row = tuple(next(iterable) for i in range(n))
```

```py
def group_by_iter(n, iterable):
    row = tuple(next(iterable) for i in range(n))
    while row:
        yield row
        row = tuple(next(iterable) for i in range(n))
print(list(group_by_iter(5, iter(result))))
# [('1', '2', '3', '4', '11'), ('22', '33', '44', '12', '23'), ('34', '45')]
```
> 입력 반복 가능 객체로부터 원하는 길이의 row를 만든다. 입력의 끝에 도달했다면 tuple(next(iterable) for i in range(n))의 결과가 길이가 0인 퓨틀일 것이다. 그 경우가 바로 재귀의 기본적인 경우라고 할 수 있다.


## 평면 시퀀스 구조화하기 - 다른 방법
> 단순한 평면적인 리스크가 있고, 그로부터 튜플을 만들고 싶다고 가정하자. 다음과 같이 리스트 슬라이스를 사용해 튜플의 리스트를 만들 수 있다.

```py
zip(flat[0::2], flat[1::2])
```
```py
other_tie_3 = (result[0::4], result[1::4], result[2::4], result[3::4])
print(other_tie_3)
# (['1', '11', '12'], ['2', '22', '23'], ['3', '33', '34'], ['4', '44', '45'])
```
> flat 리스트의 길이가 짝수라면 이 코드는 훌륭하게 튜플의 리스트를 만들어낸다.

```py
other_result = (result[0::5], result[1::5], result[2::5], result[3::5], result[4::5])
print(other_result)
# (['1', '22', '34'], ['2', '33', '45'], ['3', '44'], ['4', '12'], ['11', '23'])
```
입력 시퀀스를 적적하게 나누지 못하면 복잡한 결과가 나올 수 있다. 그러므로 간단한 조건에만 사용하도록 하자.

> 이러한 접근 방법을 일반화시킬 수도 있다. `*(args)` 형태의 접근 방식을 사용하여 zip() 할 시퀀스를 만들어 낼 수 있다.
```py
zip(*(flat[i::n] for i in range(n)))
```
```py
print(tuple(zip(*(result[i::5] for i in range(5)))))
# (('1', '2', '3', '4', '11'), ('22', '33', '44', '12', '23'))
```

> 이는 n개의 슬라이스, flat[0::n], flat[1::n], filat[2::n], ..., flat[n-1::n]을 만들어 낸다. 이렇게 만들어진 슬라이스의 컬렉션은 zip()의 인자가 된다. 따라서 그 결과는 각 슬라이스의 원소를 합친 튜플 리스트다.

> zip()은 인자 리스트 중 가장 짧은 쪽에 맞게 결과를 잘라낸다는 사실을 기억하라. 이는 원리스트가 그룹의 크기 n의 배수가 아닌 경우 (즉, len(flat)%n != 0) 마지막 슬라이스들의 길이가 더 짧기 때문에 앞 슬라이스의 맨 마지막에 있던 원소까지 결과에서 사라진다는 뜻이다. 하지만 이러한 결과를 원하는 경우는 드물 것이다.

> 이러한 경우 itertools.zip_longest() 메서드를 사용한다면, 마지막 튜플의 일부가 None으로 채워져서 길이가 n인 튜플이 될 것이다. 경우에 따라서는 이러한 식으로 채워넣어지는 것을 용인할 수도 있다. 그렇지 않은 경우, None으로 채워지는 것은 우리가 원하는 것이 아니다.

> 데이터를 그룹화하기 위해 리스트의 슬라이스를 사용하는 방식은 평면석인 데이터 시퀀스를 여러 블록으로 구조화하는 문제에 대한 또 다른 접근방식이다. 일반적인 해법의 관점에서 보면 이 방식이 앞에서 봤던 다른 삼수도다 더 많은 이점은 없다. 하지만 2-튜플을 만들어야 하는 경우라면 이 방식의 해법이 더 우아하고 더 짧다.

## 순서를 바꾸기 위해 reversed() 사용하기
> 시퀀스의 순서를 뒤집어야 할 때 파이썬에서는 두 가지 방법을 사용할 수 있다. reversed() 함수와 인덱스 값을 반대로 한 슬라이스가 그 두 가지 방법이다.

> 예를 들어 16진수를 2진수로 변환한다고 가정하자.

```py
def digits(x, b):
    if x == 0:
        return
    yield x % b
    for d in to_base(x//b, b):
        yield d
```

> 이 함수는 재귀를 사용해 최하위(가장 오른쪽)로부터 최상위의 순서로 각 자리의 숫자를 내놓는다. x%b의 값을 b를 밑으로 x를 표시하는 경우, 최하위 위치의 숫자가 될 것이다.

> 많은 경우, 각각의 숫자가 반대 순서로 나오길 바란다. 따라서 이 함수를 reversed()로 감싸 숫자의 순서를 뒤집는다.
```py
def to_base(x, b):
    return reversed(tuple(digits(x, b)))
```

> reversed() 함수는 반복 가능 객체를 만든다. 하지만 인자로 주어지는 객체는 반드시 시퀀스 객체여야만 한다. reversed() 함수는 주어진 시퀀스 객체의 원소를 역순으로 내놓는다.

> tuple(digits(x, b))[::-1]이라는 슬라이스를 사용해 같은 작업을 할 수도 있다. 하지만 슬라이스는 반복자가 아니다. 슬라이스는 다른 실체화한 객체로부터 만들어 낼 실체화한 객체다. 여기서 보여준 함수와 같은 경우, 값의 컬렉션 크기가 작다면 슬라이스와 reversed() 사이의 차이가 크지 않을 것이다. reversed() 함수가 메모리를 더 적게 사용하므로, 컬렉션이 커지면 그로 인한 이익도 커질 것이다.

슬라이스보다 reversed()를 사용하자.

## enumerate()를 사용해 인덱스 번호 포함시키기
> 파이썬의 enumerate() 함수는 어떤 시퀀스나 반복 가능 객체의 원소에 인덱스 값을 추가해준다. 이 함수는 우리가 사용해온 풀기(처리하기(감싸기(데이터))) 패턴에 사용할 수 있는 감싸기 함수에 속하는 특별한 경우다.

> enumerate() 함수는 입력 반복 가능 객체의 원소 item을 그 원소의 인덱스와 item을 결합시킨 쌍으로 변환한다. 이는 다음과 같은 일을 하는 것과 비슷하다.

```py
zip(range(len(source)), source)
```

```py
print(tuple(zip(range(len(result)), result)))
# ((0, '1'), (1, '2'), (2, '3'), (3, '4'), (4, '11'), (5, '22'), (6, '33'), (7, '44'), (8, '12'), (9, '23'), (10, '34'), (11, '45'))
```

```py
print(tuple(enumerate(iter(result))))
# ((0, '1'), (1, '2'), (2, '3'), (3, '4'), (4, '11'), (5, '22'), (6, '33'), (7, '44'), (8, '12'), (9, '23'), (10, '34'), (11, '45'))
```

> enumerate() 의 중요한 특징은 결과가 반복 가능 객체이고, 모든 반복 가능 객체를 인자로 받을 수 있다는 점이다.

> 통계 처리를 살펴볼 때 enumerate() 함수를 사용하면 시계열 데이터에 속하는 각 표본과 그 인덱스를 합쳐 처리하기 편한 데이터를 손쉽게 구성할 수 있다.
