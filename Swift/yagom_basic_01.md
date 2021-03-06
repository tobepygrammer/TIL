## 강좌소개

* macOS, ubuntu에서 사용가능

* 문법 표현력이 높은 언어
```swift
Dictionary<String, Any> ==> [String: Any]
```

## 이름짓기, 콘솔로그 문자열 보간법

### 이름짓기 규칙
* Lower Camel Case
    * function, method, variable, constant
    * ex) someVariableName

* Upper Camel Case
    * type(class, struct, enum, extension ...)
    * ex) Person, Point, Week

* 대소문자 구분

### 콘솔로그
* print: 단순 문자열 출력
* dump: 인스턴스의 자세한 설명(description 프로퍼티)까지 출력

### 문자열 보간법
* String Interpolation
* 프로그램 실행 중 문자열 내에 변수 또는 상수의 실질적인 값을 표현하기 위해 사용
* `\()`

```swift
import Swift

let age: Int = 10
print("안녕하세요. 저는 \(age + 5)살입니다.")
```
* 변수를 값으로 치환할 뿐만 아니라 수식 계산도 할 수 있다.

```swift
class Person {
    var name: String = "yagom"
    var age: Int = 10
}

let yagom: Person = Person()

print(yagom) // Person의 인스턴스라는 것이 출력됨
dump(yagom) // print의 값과 해당 인스턴스의 정보가 모두 출력됨
```

## 상수와 변수
* 상수 선언 키워드 let
* 변수 선언 키워드 var

* 선언
```
let 이름: 타입 = 값
var 이름: 타입 = 값
```

* swift는 띄어쓰기도 민감하다.

* 값의 타입이 명확하다면 타입은 생략 가능
```
let 이름 = 값
var 이름 = 값
```

* 나중에 할당하려고 하는 상수나 변수는 타입을 꼭 명시해야한다.
```swift
let sum: Int
let inputA: Int = 100
let inputB: Int = 200

// 선언 후 첫 할당, 첫 할당 이후 값을 바꾸려고 하면 오류 발생
sum = inputA + inputB
```

* 할당하지 않은 상수, 변수를 print()등으로 사용하려고 하면 에러 발생

## 기본 데이터 타입
* Bool, Int, UInt, Float, Double, Character, String

var someBool: Bool = true
someBool = false
someBool = 0 // error
someBool = 1 // error

var someInt: Int = -100

var someUInt: UInt = 100
someUInt = -100 // error
someUInt = someUInt // error

var someFloat: Float = 3.14

var someDouble: Double = 3.14
someDouble = someFloat // error

var someCharacter: Character = "a" // unicode

var someString: String = "하하하"
someString = someString + "웃으면 복이와요"
someString = someCharacter // error

* 묵시적인 데이터 형 변환은 모두 error

## Any, AnyObject, nil
* Any: Swift의 모든 타입을 지칭하는 키워드
* AnyObject: 모든 클래스 타입을 지칭하는 프로토콜
* nil: 없음을 의미하는 키워드

var someAny: Any = 100
someAny = "어떤 타입도 수용 가능합니다."
someAny = 123.123

let someDouble: Double = someAny // error

class SomeClass {}
var someAnyObject: AnyObject = SomeClass()
someAnyObject = 123.123 // error

someAny = nil // error
someAnyObject = nil // error

## 컬렉션 타입
* Array, Dictionary, Set
* Array: 순서가 있는 리스트 컬렉션
* Dictionary: 키와 값의 쌍으로 이루어진 컬렉션
* Set: 순서가 없고 멤버가 유일한 컬렉션

var intergers: Array<Int> = Array<Int>() // 빈 Array 생성
integers.append(1)
integers.append(100)

integers.contains(100) // true
integers.contains(99) // false

integers.remove(at: 0)
integers.removeLast()
integers.removeAll()

integers.count

var doubles: Array<double> = [Double]()

var strings: [String] = [String]()

var Characters: [Character] = []

let immutableArray = [1, 2, 3]


var anyDictionary: Dictionary<String, Any> = [String: Any]()
anyDictionary["someKey"] = "value"
anyDictionary["anotherKey"] = 100
// 순서가 없다.

anyDictionary["someKey"] = "dictionary"

anyDictionary.removeValue(forKey: "anotherKey")
anyDictionary["someKey"] = nil // 해당 키의 값을 삭제

let emptyDictionary: [String: String] = [:]
let initalizedDictionary: [String: String] = ["name": "yagom", "gender": "male"]

let someValue: String = initalizedDictionary["name"] // error
// name 키의 value가 없을 수도 있기 때문에!


var integerSet: Set<Int> = Set<Int>()
integerSet.insert(1)
integerSet.insert(1)
integerSet.insert(1)

integerSet.contains(1) // ture
integerSet.contains(2) // false

integerSet.remove(100)
integerSet.removeFirst()

integerSet.count

let setA: Set<Int> = [1, 2, 3, 4, 5]
let setB: Set<Int> = [3, 4, 5, 6, 7]

let union: Set<Int> = setA.union(setB)
let sortedUnion: [Int] = union.sorted()
let intersection: Set<Int> = setA.intersection(setB)
let subtracting: Set<Int> = setA.subtracting(setB)


## 함수

```
func 함수이름(인자1: 타입, 인자2: 타입 ...) -> return 타입 {
    함수 구현부
    return 반환값
}

func sum(a: Int, b: Int) -> Int {
    return a + b
}
```

* return 값이 없는 경우
    * Void라고 명시
    * Void를 생략해도 된다.

func printMyName(name: String) -> Void {
    print(name)
}

* 함수의 호출
sum(a: 3, b: 5)

## 함수 고급
* 매개변수 기본값
    * 인자에 기본값을 설정할 수 있다.

* 전달인자 레이블
    * 함수를 호출할 때 매개변수의 역할을 좀 더 명확하게 하거나
    * 함수 사용자의 입장에서 표현하고자 할 때 사용
    * 함수 내부에서 인지를 사용할 때는 반드시 인자의 이름을 사용
    * 호출할 때는 꼭 레이블을 이용하여 호출
    * 레이블이 없는 함수와 동일하더라도 다른 함수로 인식한다.
```
func greeting(to friend: String, from me: String) {
    print("Hello \(firend)! I'm \(me)")
}

greeting(to: "hana", from: "yagom")
```

* 가변인자
    * 인자 타입뒤에 `...`을 공백없이 사용한다.
    * 가변인자는 함수당 하나만 가질 수 있다.
    * 가변인자에 아무값도 넘기고 싶지 않다면 레이블을 생략한다.

```
func sayHelloToFriends(me: String, firends: String...) -> String {
    return "Hello \(firends)! I'm \(me)!"
}
print(sayHelloToFriends(me: "yagom", firends: "hana", "eric", "wing"))
```

* 일급객체
    * 인자의 타입이 다른 함수는 할당할 수 없다.
```
var someFunction: (String, String) -> Void = greeting(to:from:)
someFunction("eric", "yagom")

someFunction = greeting(firend:me:)
someFunction("eric", "yagom")
```

* 인자로 함수를 넘겨줄 수도 있다.
```
func runAnother(function: (String, String) -> Void) {
    function("jenny", "mike")
}

runAnother(function: greeting(firend:me:))

runAnother(function: someFunction)
```
