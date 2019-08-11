# 접근제어 \(Access Control\)

접근제어는 특정 코드의 접근을 다른 소스파일이나 모듈에서 제한하는 것입니다. 이렇게 접근제어를 함으로써 특정 코드의 세부적인 구현을 감추고 딱 필요한 만큼 공개해 다른 곳에서 사용할 수 있도록 합니다. 접근제어는 클래스, 구조체, 열거형 등 개별 타입에도 적용할 수 있고 그 타입에 속한 프로퍼티, 메소드, 초기자, 서브스크립트에도 적용할 수 있습니다. 프로토콜은 그래서 전역상수, 변수, 함수같이 특정 문맥에 종속됩니다. Swift에서는 기본 접근레벨을 제공해 접근레벨의 처리를 쉽게할 수 있도록 돕습니다. 그래서 사실 단일타겟의 앱에서는 특별히 접근레벨을 전혀 명시하지 않아도 됩니다.

> NOTE  
> 프로퍼티, 타입, 함수 등에 적용가능한 각 접근제어와 관련한 내용은 이후 섹션에서 자세하고 명확하게 기술해 두었습니다.

## 모듈과 소스파일 \(Modules and Source Files\)

Swift의 접근제어는 모듈과 소스파일에 기반을 두고 있습니다. 모듈은 코드를 배포하는 단일 단위로 하나의 프레임워크나 앱이 이 단위로 배포되고 다른 모듈에서 Swift의 `import`키워드를 사용해 import될 수 있습니다. Xcode의 각 빌드 타겟은 Swift에서 분리된 단일 모듈로 취급됩니다. 소스파일은 모듈안에 있는 소스파일을 의미합니다. 각 소스파일에 여러 특정 타입을 선언해 사용할 수 있습니다.

## 접근레벨 \(Access Levels\)

Swift에서는 5개의 접근레벨을 제공합니다.

* `Open` & `Public` : Open과 Public 접근자 모두 선언한 모듈이 아닌 다른 모듈에서 사용가능합니다. 두 접근자의 차이점은 Open은 다른 모듈에서 오버라이드와 서브클래싱이 가능하지만 Public 접근자로 선언된 것은 다른 모듈에서는 오버라이드와 서브클래싱이 불가능 합니다.
* `Internal` : 기본 접근레벨로 아무 접근레벨을 선언하지 않으면 `Internal`로 간주됩니다. `Internal`레벨로 선언되면 해당 모듈 전체에서 사용 가능합니다.
* `File-private` : 특정 엔티티를 선언한 파일 안에서만 사용 가능합니다.
* `Private` : 특정 엔티티가 선언된 괄호\({}\) 안에서만 사용 가능합니다.

**접근레벨 가이드 원칙 \(Guiding Principle of Access Levels\)**

Swift에서 접근 레벨은 더 낮은 레벨을 갖고 있는 다른 엔티티를 특정 엔티티에 선언해 사용할 수 없다는 일반 가이드 원칙을 따릅니다. 예를 들어,

* public 변수는 다른 internal, file-private 혹은 private 타입에서 정의될 수 없습니다. 왜냐하면 그 타입은 public 변수가 사용되는 모든 곳에서 사용될 수 없을 것이기 때문입니다.
* 함수는 그 함수의 파라미터 타입이나 리턴 값 타입보다 더 높은 접근 레벨을 갖을 수 없습니다. 왜냐하면 함수에는 접근 가능하지만 파라미터에 접근이 불가능 하거나 혹은 반환 값 타입보다 접근 레벨이 낮아 함수를 사용하는 관련 코드에서 이용할 수 없을 수 있기 때문입니다.

**기본 접근레벨 \(Default Access Levels\)**

위에서 설명한 것과 같이 아무런 접근 레벨을 명시하지 않은 경우 `internal`을 갖게 됩니다.

**단일 타겟 앱을 위한 접근레벨 \(Access Levels for Single-Target Apps\)**

단일 타겟 앱에서는 특별히 접근레벨을 명시할 필요가 없지만 필요에 따라 `file-private`, `private`등을 사용해 앱내에서 구현 세부사항을 숨길 수 있습니다.

**프레임워크를 위한 접근레벨 \(Access Levels for Frameworks\)**

프레임워크를 개발한다면 `public`혹은 `open`으로 지정해서 다른 모듈에서 볼 수 있고 접근 가능하도록 만들어야 합니다.

> NOTE  
> 만약 프레임워크의 구체적 구현을 감추고 싶은 부분이 있다면 그 부분은 `internal`로 선언하면 됩니다. 그리고 노출 시키고 싶은 API만 `public`혹은 `open`으로 지정합니다.

### 유닛테스트 타겟을 위한 접근레벨 \(Access Levels for Unit Test Targets\)

기본적으로 `open`이나 `public`으로 지정된 엔티티만 다른 모듈에서 접근 가능합니다. 하지만 유닛테스트를 하는 경우 모듈을 import할때 import앞에 `@testable`이라는 에트리뷰트를 붙여주면 해달 모듈을 테스트가 가능한 모듈로 컴파일해 사용합니다.

## 접근제어 문법 \(Access Control Syntax\)

각 접근자를 사용해 클래스와 변수, 상수를 선언한 예는 다음과 같습니다.

```swift
public class SomePublicClass {}
internal class SomeInternalClass {}
fileprivate class SomeFilePrivateClass {}
private class SomePrivateClass {}

public var somePublicVariable = 0
internal let someInternalConstant = 0
fileprivate func someFilePrivateFunction() {}
private func somePrivateFunction() {}
```

`internal`접근레벨은 생략할 수 있습니다.

```swift
class SomeInternalClass {}              // implicitly internal
let someInternalConstant = 0            // implicitly internal
```

## 커스텀 타입 \(Custom Types\)

커스텀 클래스에 특정 접근레벨을 지정할 수 있습니다. 그 레벨을 지정하면 그 클래스 안의 프로퍼티와 리턴 타입의 접근레벨은 클래스 레벨의 접근 권한만 사용할 수 있습니다. 예를 들어, 클래스를 `file-private` 클래스로 선언하면 그안의 프로퍼티와 함수 파라미터 그리고 반환 타입도 `file-private` 접근 권한을 갖습니다. 다시말해 특정 타입의 접근레벨의 지정은 그 타입의 멤버\(프로퍼티, 메소드 초기자 와 서브스크립트\)에 기본 접근레벨에 영향을 미칩니다.

> IMPORTANT `public`타입은 기본 적으로 `internal` 멤버를 갖고 `public`을 갖지 않습니다. 이렇게 동작하는 이유는 public API를 만들시 노출되지 말아야 할 API가 실수로 노출되는 것을 막기 위함입니다. 그래서 노출시키고 싶은 멤버는 명시적으로 `public` 접근제어자를 붙여줘야 합니다.

```swift
public class SomePublicClass {                  // explicitly public class
    public var somePublicProperty = 0            // explicitly public class member
    var someInternalProperty = 0                 // implicitly internal class member
    fileprivate func someFilePrivateMethod() {}  // explicitly file-private class member
    private func somePrivateMethod() {}          // explicitly private class member
}

class SomeInternalClass {                       // implicitly internal class
    var someInternalProperty = 0                 // implicitly internal class member
    fileprivate func someFilePrivateMethod() {}  // explicitly file-private class member
    private func somePrivateMethod() {}          // explicitly private class member
}

fileprivate class SomeFilePrivateClass {        // explicitly file-private class
    func someFilePrivateMethod() {}              // implicitly file-private class member
    private func somePrivateMethod() {}          // explicitly private class member
}

private class SomePrivateClass {                // explicitly private class
    func somePrivateMethod() {}                  // implicitly private class member
}
```

### 튜플타입 \(Tuple Types\)

튜플타입의 접근레벨은 튜플에서 사용되는 모든 타입의 접근레벨 중 가장 제한적인 접근레벨을 갖습니다. 예를 들어, 만약 하나는 `internal` 다른 하나는 `private`접근 권한을 갖는 2개의 타입으로 구성된 튜플은 더 낮은 레벨인 `private` 접근 레벨을 갖습니다.

> NOTE  
> 튜플은 자체적으로 접근레벨을 선언하지 않고 사용하는 클래스, 구조체, 열거형 그리고 함수 등에 따라 자동으로 최소 접근레벨을 부여 받습니다. 즉, 튜플은 명시적으로 접근권한을 명시하지 않습니다.

### 함수타입 \(Function Types\)

함수 타입의 접근레벨은 함수의 파라미터 타입과 리턴타입의 접근레벨 중 최소의 접근레벨로 계산돼 사용됩니다. 그래서 그것에 맞는 접근 레벨을 함수 앞에 명시해 줘야 합니다. 다음 코드는 명시적인 접근 레벨을 명시하지 않은 함수인데, 컴파일시 에러가 발생합니다. 이유는 반환값 중에 접근레벨이 `private`인 `SomePrivateClass` 가 존재하기 때문에 `someFunction()`은 `internal`접근레벨로 선언될 수 없기 때문입니다.

```swift
func someFunction() -> (SomeInternalClass, SomePrivateClass) {
    // function implementation goes here
}
```

이 함수를 적절히 선언하기 위해서는 아래와 같이 함수 앞에 `private`접근레벨을 지정해 줘야 합니다.

```swift
private func someFunction() -> (SomeInternalClass, SomePrivateClass) {
    // function implementation goes here
}
```

### 열거형 타입 \(Enumeration Types\)

열거형에서 각 case는 enum의 접근레벨을 따르고 개별적으로 다른 접근레벨을 지정할 수 없습니다. 다음 코드에서 각 case는 enum의 접근레벨 `public`을 따라 모두 `public`접근레벨을 갖습니다.

```swift
public enum CompassPoint {
    case north
    case south
    case east
    case west
}
```

## 고유값과 연관값\(Raw Values and Associated Values\)

고유값과 연관값을 사용하는 타입의 경우 반드시 그 타입보다 높은 접근 레벨을 가져야 합니다. 즉, `internal`접근 레벨을 갖고 있는 열거형 타입에서 `private`접근레벨을 갖는 고유값을 사용할 수 없습니다.

### 중첩타입 \(Nested Types\)

`private`로 선언된 타입의 중첩 타입은 자동으로 `private`접근레벨을 갖습니다. `file-private`으로 선언된 경우 중첩타입은 `file-private`을 갖습니다. `public` 혹은 `internal`로 선언된 타입에서 중첩타입은 자동으로 `internal`접근레벨을 갖습니다. `public`으로 선언된 타입에서 `public`으로 선언된 중첩타입을 사용하고 싶으면 명시적으로 `public`접근자를 중첩타입에 적어줘야 합니다.

## 서브클래싱 \(Subclassing\)

서브클래스는 수퍼클래스보다 더 높은 접근레벨을 갖을 수 없습니다. 예를들어, 수퍼클래스가 `internal` 를 갖는데 그것을 서브클래싱해서 `pubic` 서브클래스를 만들 수 없습니다. 하지만 메소드는 서브클래스에서 더 높은 접은 레벨을 갖는 메소드로 오버라이드 할 수 있습니다. 아래 예제에서는 class B의 someMethod\(\)는 수퍼클래스의 `fileprivate`보다 더 높은 접근레벨인 `internal`을 갖도록 오버라이드 됐습니다.

```swift
public class A {
    fileprivate func someMethod() {}
}

internal class B: A {
    override internal func someMethod() {}
}
```

이게 가능한 이유는 class A와 class B가 같은 소스파일에 선언돼 있어서 class B의 someMethod구현에서 `super.someMethod()`를 호출하는 것이 유효하기 때문입니다.

```swift
public class A {
    fileprivate func someMethod() {}
}

internal class B: A {
    override internal func someMethod() {
        super.someMethod()
    }
}
```

## 상수, 변수, 프로퍼티 그리고 서브스크립트 \(Constants, Variables, Properties, and Subscripts\)

상수, 변수, 프로퍼티 등은 그 타입보다 더 높은 접근레벨을 갖을 수 없습니다. 즉, `private`타입에서 `public`프로퍼티를 선언할 수 없습니다. 아래와 같이 `private`클래스 변수의 접근레벨은 `private`이 돼야 합니다.

```swift
private var privateInstance = SomePrivateClass()
```

### 게터와 세터 \(Getters and Setters\)

상수, 변수, 프로퍼티 그리고 서브스크립트의 게터와 세터는 자동으로 해당 상수, 변수, 프로퍼티 그리고 서브스크립트가 갖는 접근레벨을 동일하게 갖습니다. 필요에 따라 세터의 접근레벨을 게터보다 낮게 정할 수 있습니다. 이를 위해 다음 키워드를 var혹은 subscript앞에 붙여 사용합니다. `fileprivate(set)`, `private(set)`, `internal(set)`

> NOTE  
> 이 규칙은 저장프로퍼티 뿐아니라 계산된 프로퍼티에도 동일하게 적용됩니다. 아래 예제는 numberOfEdits변수를 `private(set)`로 설정해 TrackedString 내부에서만 변경되도록 지정한 코드입니다. TrackedString에 아무런 접근레벨이 지정되지 않았기 때문에 `internal`로 취급돼 numberOfEdits변수에 `private(set)`이 지정되지 않았다면 TrackedString 밖에서도 접근이 가능했을 것입니다.

```swift
struct TrackedString {
    private(set) var numberOfEdits = 0
    var value: String = "" {
        didSet {
            numberOfEdits += 1
        }
    }
}
```

이 코드를 실행하면 다음과 같이 게터만을 이용해 변경된 숫자를 확인할 수 있습니다.

```swift
var stringToEdit = TrackedString()
stringToEdit.value = "This string will be tracked."
stringToEdit.value += " This edit will increment numberOfEdits."
stringToEdit.value += " So will this one."
print("The number of edits is \(stringToEdit.numberOfEdits)")
// Prints "The number of edits is 3"
```

세터뿐 아니라 게터에도 접근레벨을 지정하고 싶다면 아래 코드와 같이 `public private(set) var numberOfEdits = 0` 게터 레벨도 지정할 수 있습니다.

```swift
public struct TrackedString {
    public private(set) var numberOfEdits = 0
    public var value: String = "" {
        didSet {
            numberOfEdits += 1
        }
    }
    public init() {}
}
```

## 초기자 \(Initializers\)

초기자의 접근레벨은 타입의 레벨과 같거나 낮습니다. 한가지 예외상황은 지정초기자\(required Initializer\)인데, 이 지정초기자는 반드시 이 초기자가 속한 타입과 접근레벨이 같아야 합니다.

### 기본 초기자 \(Default Initializers\)

기본 초기자는 타입의 접근레벨이 `public`으로 지정돼있지 않은 이상 타입과 같은 접근레벨을 갖습니다. 만약 타입의 접근 레벨이 `public`으로 지정돼 있으면 기본 초기자는 `internal` 접근레벨을 갖습니다.

### 구조체 타입을 위한 기본 멤버쪽 초기자 \(Default Memberwise Initializers for Structure Types\)

만약 모든 저장 프로퍼티가 `private`로 지정된 경우 멤버쪽 초기자의 접근레벨은 `private`을 갖고 `file private`인 경우는 `file private`를 갖습니다. 그밖에는 `internal`접근레벨을 갖습니다. 만약 `public` 구조체 타입이 다른 모듈에서 사용될 때는 반드시 멤버쪽 초기자의 선언에 `public`으로 지정해야 다른 모듈에서 그 멤버쪽 초기자를 사용할 수 있습니다.

## 프로토콜 \(Protocols\)

프로토콜의 접근레벨과 그 안의 요구사항의 접근레벨은 항상 동일합니다.

> NOTE  
> 프로토콜의 접근레벨과 그 안의 요구사항의 접근레벨은 항상 동일해야하는 것은 다른 타입과는 다르다. 다른 타입은 타입의 선언이 `public`이어도 안의 구현은 `internal`일 수 있습니다.

### 프로토콜 상속 \(Protocol Inheritance\)

이미 존재하는 프로토콜을 상속받아 새로운 프로토콜을 선언하는 경우 새 프로토콜은 상속을 한 프로토콜과 같은 접근레벨을 갖습니다.

### 프로토콜 순응 \(Protocol Conformance\)

특정 타입은 그 타입보다 낮은 접근레벨을 갖는 프로토콜을 따를 수 있습니다. 예를들어 `public`타입을 다른 모듈에서 사용하지만 `internal`프로토콜은 오직 `internal`프로토콜이 선언된 곳에서만 사용가능 합니다. 만약 타입이 `public`이고 프로토콜이 `internal`이라면 그 프로토콜을 따르는 타입도 `internal`입니다.

> NOTE  
> Objective-C 와 같이 Swift에서도 프로토콜 순응은 전역적으로 같은 프로그램에서 다른 방법으로 프로토콜을 따르는 것은 불가능 합니다.

## 익스텐션 \(Extensions\)

클래스, 구조체, 열거형 등에 익스텐션에서 새로운 맴버를 추가하면 새로 추가된 것은 기존에 타입이 선언된 접근레벨과 같은 레벨을 갖습니다. 익스텐션에 명시적으로 접근레벨을 지정할수도 있습니다. 익스텐션을 프로토콜로 사용하는 경우 명시적인 접근레벨 변경이 불가능합니다. 대신 프로토콜의 접근레벨이 익스텐션의 프로토콜 구현에서 사용 됩니다.

### 프라이빗 멤버 \(Private Members in Extensions\)

익스텐션이 클래스, 구조체 혹은 열거형과 같은 파일에 선언된 경우 다음이 가능합니다.

* 원본 선언에서 `private` 멤버로 선언한것을 익스텐션에서 멤버로 접근할 수 있습니다.
* 하나의 익스텐션에서 `private`멤버로 선언한 것을 같은 파일의 다른 익스텐션에서 접근할 수 있습니다.
* 하나의 익스텐션에서 `private`멤버로 선언한 것을 원본 선언에서 멤버로 접근할 수 있습니다.

  예제 입니다.

```swift
protocol SomeProtocol {
func doSomething()
}
```

위와 같이 프로토콜을 선언한고 아래와 같이 구조체를 하나 선언하고 `private`변수를 하나 선언합니다.

```swift
struct SomeStruct {
    private var privateVariable = 12
}

extension SomeStruct: SomeProtocol {
    func doSomething() {
        print(privateVariable)
    }
}
```

프로토콜을 따르는 익스텐션에서 그 타입의 `private`변수에 접근할 수 있습니다.

## 지네릭 \(Generics\)

지네릭 타입 혹은 함수는 지네릭 타입 혹은 함수 자체 그리고 타입 파라미터의 접근레벨의 최소 접근레벨을 갖습니다.

## 타입 별칭 \(Type Aliases\)

타입 별칭은 별칭을 붙인 타입보다 같거나 낮은 접근레벨을 갖습니다. 예를들어, `private`타입의 별칭은 `private`, `file-private`, `internal`, `public`, `open` 타입의 접근 권한을 갖을 수 있습니다.

> NOTE  
> 이 규칙은 프로토콜의 순응을 충족시키는데 사용하는 연관 타입을 위한 별칭에도 동일하게 적용됩니다.

