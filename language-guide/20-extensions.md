# 익스텐션 \(Extensions\)

익스텐션을 이용해 클래스, 구조체, 열거형 혹은 프로토콜 타입에 기능을 추가할 수 있습니다. retroactive modeling으로 알려진 것과 같이 원본 코드를 몰라도 그 타입에 대한 기능을 확장할 수 있습니다. 익스텐션은 Objective-C 의 카테고리와 유사합니다. Swift에서 익스텐션을 이용해 다음을 할 수 있습니다.

* 계산된 인스턴스 프로퍼티와 계산된 타입 프로퍼티의 추가
* 인스턴스 메소드와 타입 메소드의 추가
* 새로운 이니셜라이저 제공
* 서브스크립트 정의
* 중첩 타입의 선언과 사용
* 특정 프로토콜을 따르는 타입 만들기

> NOTE  
> 익스텐션은 타입에 새 기능을 추가할 수 있지만 오버라이드는\(override\)는 할 수 없습니다.

## 익스텐션 문법 \(Extension Syntax\)

익스텐션은 extension 키워드를 사용해 선언합니다.

```swift
extension SomeType {
    // new functionality to add to SomeType goes here
}
```

하나의 익스텐션에서 현재 존재하는 타입에 한개 이상의 프로토콜을 따르도록 확장할 수 있습니다.

```swift
extension SomeType: SomeProtocol, AnotherProtocol {
    // implementation of protocol requirements goes here
}
```

이런 방식으로 프로토콜을 구현하는 것은 Addind Protocol Conformance with an Extension에 설명돼 있습니다. 하나의 익스텐션은 Extending a Generic Type에 설명돼 있는 것처럼 generic 타입으로 확장하는데 사용할 수 있습니다. 또 generic 타입에 조건적으로 기능을 추가 할 수 있는 것은 Extensions with a Generic Where clause에 설명돼 있습니다.

> NOTE  
> 익스텐션을 정의하여 존재하는 타입에 새 기능을 추가하면, 그 기능은 익스텐션을 정의하기 이전에 생성한 인스턴스를 포함한 존재하는 모든 해당 타입의 인스턴스에서 사용 가능합니다.

## 계산된 프로퍼티 \(Computed Properties\)

익스텐션을 이용해 존재하는 타입에 계산된 인스턴스 프로퍼티와 타입 프로퍼티를 추가할 수 있습니다. 다음 예제는 Swift의 built-in 타입인 Double에 5개의 계산된 인스턴스 프로퍼티를 추가하는 예제 입니다.

```swift
extension Double {
    var km: Double { return self 1_000.0 }
    var m: Double { return self }
    var cm: Double { return self / 100.0 }
    var mm: Double { return self / 1_000.0 }
    var ft: Double { return self / 3.28084 }
}
let oneInch = 25.4.mm
print("One inch is \(oneInch) meters")
// Prints "One inch is 0.0254 meters"
let threeFeet = 3.ft
print("Three feet is \(threeFeet) meters")
// Prints "Three feet is 0.914399970739201 meters"
```

주어진 Double값에 km, m, cm 등 단위를 붙여 미터로 변경하는 계산된 프로퍼티 입니다. 단위 변환은 m\(미터\)를 기준으로 합니다. 이 프로퍼티들은 읽기 전용 계산된 프로퍼티이기 때문에 간결함을 위해 get을 붙이지 않았습니다.

```swift
let aMarathon = 42.km + 195.m
print("A marathon is \(aMarathon) meters long")
// Prints "A marathon is 42195.0 meters long"
```

단위 변환 값이 Double 타입이기 때문에 각각의 변환 값의 연산도 가능합니다.

> NOTE  
> 익스텐션은 새 계산된 값을 추가할 수 있지만 새로운 저장된 프로퍼티나 프로퍼티 옵저버를 추가할 수는 없습니다.

## 이니셜라이저 \(Initializers\)

익스텐션을 이용해 존재하는 타입에 새로운 이니셜라이저를 추가할 수 있습니다. 이 방법으로 커스텀 타입의 이니셜라이저 파라미터를 넣을 수 있도록 변경하거나 원래 구현에서 포함하지 않는 초기화 정보를 추가할 수 있습니다.

익스텐션은 클래스에 새로운 편리한 이니셜라이저\(convenience initializer\)를 추가할 수는 있지만 지정된 이니셜라이저\(designated initializers\)나 디이니셜라이저\(deinitializers\)를 추가할 수는 없습니다. 지정된 이니셜라이저는 항상 반드시 오리지널 클래스의 구현에서 작성돼야합니다.

만약 익스텐션을 값타입에 이니셜라이저를 추가하는데 사용하고, 그 값타입이 모든 프로퍼티에 대해 기본 값을 제공하고 커스텀 이니셜라이저를 정의하지 않았다면, 익스텐션에서 기본 이니셜라이저와 멤버쪽 이니셜라이저를 익스텐션에서 호출할 수 있습니다. 만약 값 타입의 오리지널 구현의 부분으로 이니셜라이저 코드를 작성했다면 그것은 이 경우에 해당하지 않습니다.

만약 다른 모듈에 선언돼 있는 구조체에 이니셜라이저를 추가하는 익스텐션을 사용한다면 새로운 이니셜 라이져는 모듈에 정의된 이니셜라이저를 호출하기 전까지 self에 접근할 수 없습니다.

```swift
struct Size {
    var width = 0.0, height = 0.0
}
struct Point {
    var x = 0.0, y = 0.0
}
struct Rect {
    var origin = Point()
    var size = Size()
}
```

위 예제에서는 Size와 Point구조체를 정의하고 그것을 사용하는 Rect 구조체를 정의했습니다. Rect 구조체에서 모든 프로퍼티의 기본 값을 제공하기 때문에 Rect구조체는 기본 이니셜라이저와 멤버쪽 이니셜라이저를 자동으로 제공 받아 사용할 수 있습니다.

기본적으로 제공되는 이니셜라이저를 사용해 초기화를 한 예제입니다.

```swift
let defaultRect = Rect()
let memberwiseRect = Rect(origin: Point(x: 2.0, y: 2.0),
   size: Size(width: 5.0, height: 5.0))
```

Rect 구조체를 추가적인 이니셜라이저를 제공하기 위해 확장 할 수 있습니다.

```swift
extension Rect {
    init(center: Point, size: Size) {
        let originX = center.x - (size.width / 2)
        let originY = center.y - (size.height / 2)
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
}
```

Rect에서 확장한 이니셜라이저를 사용한 코드는 다음과 같이 사용할 수 있습니다.

```swift
let centerRect = Rect(center: Point(x: 4.0, y: 4.0),
                      size: Size(width: 3.0, height: 3.0))
// centerRect's origin is (2.5, 2.5) and its size is (3.0, 3.0)
```

> NOTE  
> 익스텐션에서 이니셜라이저를 제공할 때 각 인스턴스가 이니셜라이저가 한번 완료되면 완전히 초기화 되도록 확실히 해야 합니다.

## 메소드 \(Methods\)

익스텐션을 이용해 존재하는 타입에 인스턴스 메소드나 타입 메소드를 추가할 수 있습니다. 다음 예제는 Int 타입에 repetitions라는 인스턴스 메소드를 추가한 예제 입니다.

```swift
extension Int {
    func repetitions(task: () -> Void) {
        for _ in 0..<self {
            task()
        }
    }
}
```

repetitions\(task:\) 메소드는 \(\) -&gt; Void 타입의 하나의 인자를 받고 파라미터와 반환 값이 없는 함수입니다.

```swift
3.repetitions {
    print("Hello!")
}
// Hello!
// Hello!
// Hello!
```

함수를 실행하면 함수 안의 task를 숫자 만큼 반복 실행합니다.

## 변경 가능한 인스턴스 메소드 \(Mutating Instance Methods\)

익스텐션에서 추가된 인스턴스 메소드는 인스턴스 자신\(self\)을 변경할 수 있습니다. 구조체와 열거형 메소드 중 자기 자신\(self\)를 변경하는 인스턴스 메소드는 원본 구현의 mutating 메소드와 같이 반드시 mutating으로 선언돼야 합니다.

아래 코드는 새 mutating 메소드를 추가하고 호출하는 예제입니다.

```swift
extension Int {
    mutating func square() {
        self = self * self
    }
}
var someInt = 3
someInt.square()
// someInt is now 9
```

## 서브스크립트 \(Subscripts\)

익스텐션을 이용해 존재하는 타입에 새로운 서브스크립트를 추가할 수 있습니다. 다음 예제는 Swift의 built-in 타입에 integer 서브스크립트를 추가한 예제입니다. 서브스크립트 \[n\]은 숫자의 오른쪽에서부터 n번째 위치하는 정수를 반환합니다.

* 123456789\[0\]  returns 9
* 123456789\[1\]  returns 8

```swift
extension Int {
    subscript(digitIndex: Int) -> Int {
        var decimalBase = 1
        for _ in 0..<digitIndex {
            decimalBase *= 10
        }
        return (self / decimalBase) % 10
        // 10 * n번째 수로 현재 수를 나눈 것의 나머지
      // 1인 경우 746381295 % 10 -> 5가 나머지
      // 2인 경우 746381295 % 10 -> 9가 나머지
    }
}
746381295[0]
// returns 5
746381295[1]
// returns 9
746381295[2]
// returns 2
746381295[8]
// returns 7
```

만약 Int값에서 요청한 값이 처리할 수 있는 자릿 수를 넘어가면 서브스크립트 구현에서 0을 반환합니다.

```swift
746381295[9]
// 9로 처리할 수 있는 자릿 수를 넘어가면 0을 반환
0746381295[9]
```

## 중첩 타입 \(Nested Types\)

익스텐션을 이용해 존재하는 클래스, 구조체, 열거형에 중첩 타입을 추가할 수 있습니다.

```swift
extension Int {
    enum Kind {
        case negative, zero, positive
    }
    var kind: Kind {
        switch self {
        case 0:
            return .zero
        case let x where x > 0:
            return .positive
        default:
            return .negative
        }
    }
}
```

위 예제는 Int에 중첩형 enum을 추가한 예제입니다. Kind라고 불리는 열거형은 Int를 음수, 0, 양수로 표현합니다.

아래 예제는 새로운 계산된 프로퍼티 kind를 이용해 특정 수가 음수, 0, 양수 중 어떤 것인지를 나타내는 예제입니다.

```swift
func printIntegerKinds(_ numbers: [Int]) {
    for number in numbers {
        switch number.kind {
        case .negative:
            print("- ", terminator: "")
        case .zero:
            print("0 ", terminator: "")
        case .positive:
            print("+ ", terminator: "")
        }
    }
    print("")
}
printIntegerKinds([3, 19, -27, 0, -6, 0, 7])
// Prints "+ + - 0 - 0 + "
```

printIntegerKinds\(\_:\) 함수를 Int 배열을 입력으로 받아 각 Int가 음수, 0, 양수 어디에 속하는지 계산해서 그에 맞는 기호를 반환하는 함수 입니다.

> NOTE  
> number.kind가 이미 switch에서 Int.Kind 타입이라는 것을 알고 있기 때문에 안의 case에서 kind의 축약형인 .negative, .zeo, .positive로 사용할 수 있습니다.

