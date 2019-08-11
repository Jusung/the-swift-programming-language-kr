# 초기화 \(Initialization\)

초기화는 클래스, 구조체, 열거형 인스턴스를 사용하기 위해 준비 작업을 하는 단계 입니다. 이 단계에서 각 저장 프로퍼티의 초기 값을 설정합니다. 초기화 과정은 initializer를 정의 하는 것으로 구현할 수 있습니다. Swift의 initializer는 값을 반환하지 않습니다. 초기화와 반대로 여러 값과 자원의 해지를 위해 deinitializer도 사용할 수 있습니다.

## 저장 프로퍼티를 위한 초기값 설정 \(Setting Initial Values for Stored Properties\)

인스턴스의 저장 프로퍼티는 사용하기 전에 반드시 특정 값으로 초기화 돼야 합니다. 이 값으로 기본 값을 설정할 수 있고, 특정 값을 설정할 수도 있습니다.

> NOTE  
> initializer에서 저장 프로퍼티에 값을 직접 설정하면 프로퍼티 옵저버가 호출되지 않고 값 할당이 수행됩니다.

### 이니셜라이저 \(Initializers\)

이니셜라이저는 특정 타입의 인스턴스를 생성합니다. 이니셜라이저의 가장 간단한 형태는 파라미터가 없고 init 키워드를 사용하는 것입니다.

```swift
init() {
    // perform some initialization here
}
```

다음 예제는 화씨 온도 구조체를 만들어 온도라는 프로퍼티를 선언하고 이니셜라이저에서 초기화하는 코드입니다.

```swift
struct Fahrenheit {
    var temperature: Double
    init() {
        temperature = 32.0
    }
}
var f = Fahrenheit()
print("The default temperature is \(f.temperature)° Fahrenheit")
// Prints "The default temperature is 32.0° Fahrenheit"
```

### 기본 프로퍼티 \(Default Property Values\)

프로퍼티의 선언과 동시에 값을 할당하면 그 값을 초기 값으로 사용할 수 있습니다.

> NOTE  
> 항상 같은 초기 값을 갖는 다면 기본 프로퍼티를 사용하는 것이 좋습니다. 프로퍼티에 타입을 선언하지 않아도 컴파일러는 초기 값을 참조해서 타입을 추론할 수 있습니다. 이 기본값은 상속시 함께 상속 됩니다.

```swift
struct Fahrenheit {
    var temperature = 32.0
}
```

위 코드는 프로퍼티를 선언과 동시에 초기 값을 할당한 예제 입니다.

## 커스터마이징 초기화 \(Customizing Initialization\)

초기화 프로세스를 입력 값과 옵셔널 프로퍼티 타입 혹은 상수 값을 할당해서 커스터마이징 할 수 있습니다.

### 초기화 파라미터 \(Initialization Parameters\)

초기화 정의에 파라미터를 정의해 사용할 수 있습니다. 다음 코드는 temperatureInCelsius 프로퍼티를 초기화 파라미터로 입력받아 초기화에 사용하는 예제입니다.

```swift
struct Celsius {
    var temperatureInCelsius: Double
    init(fromFahrenheit fahrenheit: Double) {
        temperatureInCelsius = (fahrenheit - 32.0) / 1.8
    }
    init(fromKelvin kelvin: Double) {
        temperatureInCelsius = kelvin - 273.15
    }
}
let boilingPointOfWater = Celsius(fromFahrenheit: 212.0)
// boilingPointOfWater.temperatureInCelsius is 100.0
let freezingPointOfWater = Celsius(fromKelvin: 273.15)
// freezingPointOfWater.temperatureInCelsius is 0.0
```

### 파라미터 이름과 인자 레이블 \(Parameter Names and Argument Labels\)

메소드 파라미터와 초기화 파라미터 모두 파라미터 이름과 인자 레이블을 갖지만, 이니셜라이저는 특정 메소드에서 지정하는 메소드 이름을 지정하지 않고 이니셜라이저 식별자로 파라미터를 사용합니다. 모든 파라미터는 인자 레이블을 갖는데 만약 사용자가 이 레이블을 지정하지 않으면 Swift가 자동으로 하나를 할당해 제공합니다.

다음 코드는 초기화 때 파라미터를 3개 입력받는 이니셜라이저와 하나만 입력받는 이니셜라이저의 예제입니다.

```swift
struct Color {
    let red, green, blue: Double
    init(red: Double, green: Double, blue: Double) {
        self.red   = red
        self.green = green
        self.blue  = blue
    }
    init(white: Double) {
        red   = white
        green = white
        blue  = white
    }
}
```

위에 정의한데로 Color 인스턴스를 인자값 3개 혹은 하나를 이용해 생성할 수 있습니다.

```swift
let magenta = Color(red: 1.0, green: 0.0, blue: 1.0)
let halfGray = Color(white: 0.5)
```

아래 코드는 초기화 과정에 인자 레이블이 포함돼 있지 않아서 컴파일 에러가 발생합니다.

```swift
let veryGreen = Color(0.0, 1.0, 0.0)
// this reports a compile-time error - argument labels are required
```

### 인자 레이블이 없는 이니셜라이저 파라미터 \(Initializer Parameters Without Argument Labels\)

코드를 작성할 때 인자 레이블을 생략하는 것이 더 명료한 경우 \_ 기호를 사용해 이니셜라이저에서 인자 레이블을 생략할 수 있습니다.

```swift
struct Celsius {
    var temperatureInCelsius: Double
    init(fromFahrenheit fahrenheit: Double) {
        temperatureInCelsius = (fahrenheit - 32.0) / 1.8
    }
    init(fromKelvin kelvin: Double) {
        temperatureInCelsius = kelvin - 273.15
    }
    init(_ celsius: Double) {
        temperatureInCelsius = celsius
    }
}
let bodyTemperature = Celsius(37.0)
// bodyTemperature.temperatureInCelsius is 37.0
```

`let bodyTemperature = Celsius(37.0)` 코드와 같이 인자 레이블 없이 Celsius 인스턴스를 초기화 할 수 있습니다.

### 옵셔널 프로퍼티 타입 \(Optional Property Types\)

프로퍼티의 최초 값이 없고 나중에 추가될 수 있는 값을 옵셔널로 선언해 사용할 수 있습니다. 옵셔널 프로퍼티는 자동으로 nil로 초기화 됩니다.

```swift
class SurveyQuestion {
    var text: String
    var response: String?
    init(text: String) {
        self.text = text
    }
    func ask() {
        print(text)
    }
}
let cheeseQuestion = SurveyQuestion(text: "Do you like cheese?")
cheeseQuestion.ask()
// Prints "Do you like cheese?"
cheeseQuestion.response = "Yes, I do like cheese."
```

### 초기화 중에 상수 프로퍼티 할당 \(Assigning Constant Properties During Initialization\)

이니셜라이저에서는 상수 프로퍼티에 값을 할당하는 것도 가능합니다.

> NOTE  
> 클래스 인스턴스에서 상수 프로퍼티는 초기화 중 그 클래스 안에서만 변경이 가능하고 서브클래스에서는 변경이 불가능 합니다.

프로퍼티를 let으로 선언해서 이 프로퍼티는 처음에 초기화 되면 변경되지 않는 프로퍼티라는 것을 표현할 수 있습니다.

```swift
class SurveyQuestion {
    let text: String
    var response: String?
    init(text: String) {
        self.text = text
    }
    func ask() {
        print(text)
    }
}
let beetsQuestion = SurveyQuestion(text: "How about beets?")
beetsQuestion.ask()
// Prints "How about beets?"
beetsQuestion.response = "I also like beets. (But not with cheese.)"
```

## 기본 이니셜라이저 \(Default Initializers\)

만약 모든 프로퍼티의 초기값이 설정돼 있고, 하나의 초기자도 정의하지 않았다면 Swift는 모든 프로퍼티를 기본 값으로 초기화 화는 기본 초기자를 제공해 줍니다. 아래 코드는 이니셜라이저를 정의하지 않았지만 Swift가 제공하는 기본 이니셜라이저 `ShoppingListItem()`를 사용할 수 있음을 보여주는 예제입니다.

```swift
class ShoppingListItem {
    var name: String?
    var quantity = 1
    var purchased = false
}
var item = ShoppingListItem()
```

### 구조체 타입을 위한 맴버쪽 이니셜라이저 \(Memberwise Initializers for Structure Types\)

기본 이니셜라이저와 다르게 맴버쪽 이니셜라이저는 프로퍼티가 기본 값이 없어도 커스텀 이니셜라이저를 정의하지 않았다면 멤버쪽 이니셜라이저를 제공해 줍니다. 이 초기자는 선언한 모든 프로퍼티를 인자로 사용합니다.

```swift
struct Size {
    var width = 0.0, height = 0.0
}
let twoByTwo = Size(width: 2.0, height: 2.0)
```

## 값 타입을 위한 이니셜라이저 위임 \(Initializer Delegation for Value Types\)

이니셜라이저에서 다른 이니셜라이저를 호출할 수 있습니다. 이 과정을 이니셜라이저 위임이라 합니다. 값 타입\(structures, enumerations\)과 클래스 타입\(class\)간 이니셜라이저 위임 동작이 다릅니다. 값 타입은 상속을 지원하지 않아 이니셜라이저를 자기 자신의 다른 이니셜라이저에만 사용할 수 있습니다. 반면 클래스 타입은 상속이 가능하기 때문에 superclass의 이니셜라이저를 subclass에서 호출 가능합니다. 상속 받은 모든 프로퍼티의 초기화는 책임은 이니셜라이저에 있습니다. 커스텀 이니셜라이저 선언지 기본 이니셜라이저 혹은 멤버쪽 이니셜라이저를 사용할 수 없습니다.

이런 제약이 이니셜라이저의 복잡성을 낮춰주고 이니셜라이저가 의도하지 않게 사용되는 것을 방지해 줍니다.

> NOTE  
> 커스텀 이니셜라이저를 사용하면서 기본 이니셜라이저와 맴버쪽 이니셜라이저도 사용하고 싶다면 커스텀 이니셜라이저를 오리지널 클래스에서 구현하지 않고 익스텐션\(extension\)에서 구현하면 됩니다.

다음 예제는 Size와 Point라는 값 타입 구조체를 선언한 코드입니다.

```swift
struct Size {
    var width = 0.0, height = 0.0
}
struct Point {
    var x = 0.0, y = 0.0
}
```

위에서 정의한 구조체를 다른 구조체에서 프로퍼티로 사용합니다.

```swift
struct Rect {
    var origin = Point()
    var size = Size()
    init() {}
    init(origin: Point, size: Size) {
        self.origin = origin
        self.size = size
    }
    init(center: Point, size: Size) {
        let originX = center.x - (size.width / 2)
        let originY = center.y - (size.height / 2)
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
}
```

이 구조체를 초기화 하는 방법은 세 가지가 있습니다. init\(\), init\(origin: Point, size: Size\) 그리고 init\(center: Point, size: Size\) 입니다.

첫 이니셜라이저 init\(\)를 이용해 초기화를 하면 아래와 같이 각 프로퍼티가 기본 값을 초기 값으로 갖게 됩니다.

```swift
let basicRect = Rect()
// basicRect's origin is (0.0, 0.0) and its size is (0.0, 0.0)
```

두번째 이니셜라이저 init\(origin: Point, size: Size\)를 사용하면 초기화를 수행할 때, 프로퍼티의 값을 지정할 수 있습니다. 이 이니셜라이저는 멤버쪽 이니셜라이저와 동일합니다.

```swift
let originRect = Rect(origin: Point(x: 2.0, y: 2.0),
                      size: Size(width: 5.0, height: 5.0))
// originRect's origin is (2.0, 2.0) and its size is (5.0, 5.0)
```

마지막으로 세번째 이니셜라이저 init\(center: Point, size: Size\)에서는 내부에서 다른 초기자인 init\(center: Point, size: Size\)를 사용합니다. 그래서 특정 수행을 한 후 init\(center: Point, size: Size\)를 호출해 초기화를 이 이니셜라이저에게 위임합니다.

```swift
let centerRect = Rect(center: Point(x: 4.0, y: 4.0),
                      size: Size(width: 3.0, height: 3.0))
// centerRect's origin is (2.5, 2.5) and its size is (3.0, 3.0)
```

> NOTE  
> 이니셜라이저를 익스텐션을 이용해 선언하면 첫번째, 두번째 이니셜라이저는 자동으로 생성되고 익스텐선에 선언한 이니셜라이저도 사용할 수 있습니다.

## 클래스 상속과 초기화 \(Class Inheritance and Initialization\)

모든 클래스의 저장 프로퍼티와 superclass로부터 상속받은 모든 프로퍼티는 초기화 단계에서 반드시 초기값이 할당돼야 합니다. Swift에서는 클래스 타입에서 모든 프로퍼티가 초기 값을 갖는 것을 보장하기 위해 2가지 방법을 지원합니다.

### 지정 초기자와 편리한 초기자 \(Designated Initializers and Convenience Initializers\)

지정 초기자는 클래스의 주\(primary\) 초기자입니다. 지정 초기자는 클래스의 모든 프로퍼티를 초기화 합니다. 클래스 타입은 반드시 한개 이상의 지정 초기자가 있어야 합니다.

편리한 초기자는 초기화 단계에서 미리 지정된 값을 사용해서 최소한의 입력으로 초기화를 할 수 있도록 해주는 초기자입니다. 편리한 초기자 내에서 반드시 지정 초기자가 호출돼야 합니다.

### 지정 초기자와 편리한 초기자의 문법 \(Syntax for Designated and Convenience Initializers\)

클래스의 지정 초기자의 문법은 값 타입 초기자와 같습니다.

```swift
init(parameters) {
    statements
}
```

편리한 초기자는 기본 초기자와 문법이 같지만 init 앞에 convenience 키워드를 붙여 줍니다.

```swift
convenience init(parameters) {
    statements
}
```

### 클래스 타입을 위한 이니셜라이저 위임 \(Initializer Delegation for Class Types\)

지정 초기자와 편리한 초기자 사이의 관계를 단순하게 하기 위해 Swift는 이니셜라이저는 위임하는 호출을 위해 다음 3가지 규칙을 따릅니다.

**규칙 1** 지정 초기자는 반드시 직계 superclass의 지정 초기자를 호출해야 합니다.

**규칙 2** 편리한 초기자는 반드시 같은 클래스의 다른 초기자를 호출해야 합니다.

**규칙 3** 편리한 초기자는 궁극적으로 지정초기자를 호출해야 합니다.

기억하기 쉽게 요약하면

* 지정 초기자는 반드시 위임을 superclass로 해야하고
* 편리한 초기자는 반드시 위임을 같은 레벨에서 해야합니다.

이 규칙은 다음 그림에서 잘 설명 돼 있습니다.

![](../.gitbook/assets/initializerdelegation01_2x.png)

그림을 보시면 Subclass의 편리한 초기자는 같은 레벨에서 다른 지정 초기자를 호출하고 지정 초기자는 초기화를 상위 클래스에게 위임해 상위 클래스의 지정 초기자가 호출되는 것을 확인할 수 있습니다.

> NOTE  
> 초기자 호출의 모양이 반드시 위 처럼 되야 하는 것은 아닙니다. 다만 위에서 언급만 규칙만 지켜지면 상관 없습니다.

다음은 좀 더 복잡한 초기화 위임의 형태입니다.

![](../.gitbook/assets/initializerdelegation02_2x.png)

### 2단계 초기화 \(Two-Phase Initialization\)

Swift에서 클래스 초기화는 2단계로 진행됩니다. 첫번째 단계에서는 각 저장된 프로퍼티는 초기값으로 초기화 됩니다. 모든 저장된 프로퍼티의 상태가 결정되면 두번째 단계가 시작됩니다. 두번째 단계에서는 새로운 인스턴스의 사용이 준비됐다고 알려주기 전에 저장된 프로퍼티를 커스터마이징하는 단계입니다.

> NOTE  
> Swift의 2단계 초기화는 Objective-C에서의 초기화와 유사합니다. 주된 차이점은 첫번째 단계입니다. Objective-C에서는 모든 프로퍼티에 zero 혹은 null 값을 할당합니다. Swift의 초기화는 좀더 유연해서 커스텀한 초기 값을 할 당할 수 있습니다. 그리고 0과 nil이 잘못된 초기값으로 지정된 경우 대처할 수 있습니다.

Swift의 컴파일러는 2단계 초기화가 에러없이 끝나는 것을 보장하기 위해 4단계 안전 확인\(safety-check\)을 합니다.

**안전 확인 1단계** 지정 초기자는 클래스 안에서 초기화를 superclass의 초기자에게 위임하기 전에 모든 프로퍼티를 초기화 해야 합니다. 위에서 언급한 것 처럼 메모리에서 객체는 모든 저장된 프로퍼티가 초기 상태를 갖어야만 완전히 초기화 된것으로 간주되기 때문에 이 규칙을 만족시키기 위해 지정 초기자는 반드시 다른 초기자로 넘기기 전에 소유하고있는 모든 프로퍼티를 초기화 해야 합니다.

**안전 확인 2단계** 지정 초기자는 반드시 상속된 값을 할당하기 전에 superclass의 초기자로 위임을 넘겨야 합니다. 그렇지 않으면 상속된 값이 superclass이 초기자에 의해 덮어 쓰여지게 됩니다.

**안전 확인 3단계** 편리한 초기자는 반드시 어떤 프로퍼티를 할당하기 전에 다른 초기자로 위임을 넘겨야 합니다. 만약 그렇지 않으면 편리한 초기자에 의해 할당된 값을 다른 클래스의 지정 초기자에 의해 덮어 쓰여지게 됩니다.

**안전 확인 4단계** 이니셜라이저는 초기화의 1단계가 끝나기 전에는 self의 값을 참조하거나 어떤 인스턴스 프로퍼티, 메소드 등을 호출하거나 읽을 수 없습니다.

다음은 2단계 초기화를 그림으로 나타낸 것입니다.

![\[&#xB2E8;&#xACC4; 1\]](../.gitbook/assets/twophaseinitialization01_2x.png)

![\[&#xB2E8;&#xACC4; 2\]](../.gitbook/assets/twophaseinitialization02_2x.png)

### 이니셜라이저의 상속과 오버라이딩 \(Initializer Inheritance and Overriding\)

Swift에서는 기본적으로 subclass에서 superclass의 이니셜라이저를 상속하지 않습니다. 이유는 superclass의 이니셜라이저가 무분별하게 상속돼 복잡하게 돼 subclass에서 이것들이 잘못 초기화 되는 것을 막기 위함입니다.

> NOTE  
> Superclass의 이니셜라이저는 안전하고 적당한 특정 환경에서 상속됩니다. 자세한건 아래 내용을 참조하세요.

만약 클래스에서 모든 프로퍼티의 초기 값이 지정돼 있고 아무런 커스텀 초기자를 선언하지 않았다면 기본 초기자 init\(\)을 사용할 수 있습니다.

superclass의 초기자를 오버라이드 하기 위해서는 subclass에서 그 초기자에 override 키워드를 붙이고 재정의 합니다.

다음 예제는 클래스를 생성하고 그것의 subclass에서 초기자를 오버라이드 해 사용하는 예제입니다.

```swift
class Vehicle {
    var numberOfWheels = 0
    var description: String {
        return "\(numberOfWheels) wheel(s)"
    }
}
```

인스턴스 생성 후 초기 값이 지정한 대로 인지 확인합니다.

```swift
let vehicle = Vehicle()
print("Vehicle: \(vehicle.description)")
// Vehicle: 0 wheel(s)
```

subclass에서 superclass의 초기자를 오버라이드 합니다.

```swift
class Bicycle: Vehicle {
    override init() {
        super.init()
        numberOfWheels = 2
    }
}
```

인스턴스 생성 후 초기 값이 변한 것을 확인할 수 있습니다.

```swift
let bicycle = Bicycle()
print("Bicycle: \(bicycle.description)")
// Bicycle: 2 wheel(s)
```

> NOTE  
> subclass의 초기자에서 var은 변경 가능하지만 let은 변경할 수 없습니다.

### 자동 초기자 인스턴스 \(Automatic Initializer Inheritance\)

앞서 언급했던 것과 같이 서브클래스는 수퍼클래스의 초기자를 기본적으로 상속하지 않습니다. 하지만 특정 상황에서 자동으로 상속 받습니다. 사실 많은 상황에서 직접 초기자를 오버라이드 할 필요가 없습니다. 서브클래스에서 새로 추가한 모든 프로퍼티에 기본 값을 제공하면 다음 두가지 규칙이 적용됩니다. **규칙1** 서브클래스가 지정초기자를 정의하지 않으면 자동으로 수퍼클래스의 모든 지정초기자를 상속합니다. **규칙2** 서브클래스가 수퍼클래스의 지정초기자를 모두 구현한 경우\(규칙 1의 경우 혹은 모든 지정초기자를 구현한 경우\) 자동으로 수퍼클래스의 편리한 초기자를 추가합니다.

> NOTE  
> 규칙2에따라 서브클래스는 수퍼클래스의 지정초기자를 서브클래스의 편리한 초기자로 구현 가능합니다.

### 지정초기자와 편리한 초기자의 사용

다음은 지정초기자, 편리한 초기자 그리고 자동 초기자의 상속의 예제 입니다.

```swift
class Food {
    var name: String
    init(name: String) {
        self.name = name
    }
    convenience init() {
        self.init(name: "[Unnamed]")
    }
}
```

편리한 초기자 `convenience init()`에서 지정초기자 `init(name: String)`이 호출되는 형태입니다.

![](../.gitbook/assets/initializersexample01_2x.png)

Food 클래스는 모든 프로퍼티의 기본값이 있는게 아니므로 맴버쪽 초기자를 갖지는 않습니다.

```swift
let namedMeat = Food(name: "Bacon")
// namedMeat's name is "Bacon"
```

위 예제는 지정초기자를 이용해 Food 인스턴스의 name의 초기값을 “Bacon”으로 설정해 생성하는 예제입니다. 다음 예제는 편리한 초기자를 이용해 인스턴스를 생성한 코드입니다.

```swift
let mysteryMeat = Food()
// mysteryMeat's name is "[Unnamed]"
```

mysteryMeat은 초기값을 지정하지 않았음으로 편리한 초기자에 의해 "\[Unnamed\]”를 갖습니다.

다음 코드는 Food 클래스를 서브클래싱해서 생성한 RecipeIngredient 클래스에서 수퍼클래스의 편리한 초기자를 오버라이딩해서 사용한 예제입니다.

```swift
class RecipeIngredient: Food {
    var quantity: Int
    init(name: String, quantity: Int) {
        self.quantity = quantity
        super.init(name: name)
    }
    override convenience init(name: String) {
        self.init(name: name, quantity: 1)
    }
}
```

수퍼클래스의 `init(name: name)` 초기자를 상속받아 지정초기자를 생성하고 그 지정초기자를 편리한 초기자 `convenience init(name: String)`에서 오버라이딩해 사용합니다. RecipeIngredient에서 초기자가 사용되는 구조를 표현하면 다음 그림과 같습니다.

![](../.gitbook/assets/initializersexample02_2x.png)

이렇게 생성한 RecipeIngredient 클래스는 다음 3가지 형태의 초기자를 이용해 인스턴스를 생성할 수 있습니다.

```swift
let oneMysteryItem = RecipeIngredient()
let oneBacon = RecipeIngredient(name: "Bacon")
let sixEggs = RecipeIngredient(name: "Eggs", quantity: 6)
```

마지막으로 RecipeIngredient를 상속받아 생성한 ShoppingListItem 클래스에서의 초기자에 대해 살펴보겠습니다. ShoppingListItem는 purchased라는 bool 프로퍼티와 description이라는 계산된 프로퍼티 2가지를 새로 생성합니다.

```swift
class ShoppingListItem: RecipeIngredient {
    var purchased = false
    var description: String {
        var output = "\(quantity) x \(name)"
        output += purchased ? " ✔" : " ✘"
        return output
    }
}
```

> NOTE  
> ShoppingListItem 클래스에서 purchased 프로퍼티 값은 언제나 구매하지 않음\(purchased = false\)을 시작값으로 사용하기 때문에 이 값을 변경하는 초기자를 제공하지 않습니다. ShoppingListItem 클래스는 새로 생성한 모든 프로퍼티에 대해 기본 값을 제공하고 새로운 초기자를 정의하지 않았기 때문에 자동으로 수퍼클래스의 모든 지정초기자와 편리한 초기자를 상속 받습니다. ShoppingListItem 클래스에서 초기자가 호출되는 구조를 살펴보면 다음과 같습니다.

![](../.gitbook/assets/initializersexample03_2x%20%281%29.png)

이제 이 모든 3개의 초기자를 이용해 ShoppingListItem 인스턴스를 생성할 수 있습니다.

```swift
var breakfastList = [
    ShoppingListItem(),
    ShoppingListItem(name: "Bacon"),
    ShoppingListItem(name: "Eggs", quantity: 6),
]
breakfastList[0].name = "Orange juice"
breakfastList[0].purchased = true
for item in breakfastList {
    print(item.description)
}
// 1 x Orange juice ✔
// 1 x Bacon ✘
// 6 x Eggs ✘
```

## 실패 가능한 초기자 \(Failable Initializers\)

초기화 과정 중에 실패할 가능성이 있는 초기자를 init뒤에 물음표\(?\)를 사용해 실패가 가능한 초기자라고 표시할 수 있습니다.

> NOTE  
> 초기자는 이름이 따로 있는 것이 아니라 파라미터로 구분하기 때문에 실패 가능한 초기자와 실패 불가능한 초기자를 같은 파라미터 타입과 이름으로 동시에 사용할 수 없습니다. 실패 가능한 초기자는 반환값으로 옵셔널 값을 생성합니다. 초기화에 실패하는 부분에서 nil을 반환하는 코드를 작성해 초기화가 실패했다는 것을 나타낼 수 있습니다.
>
> NOTE  
> 엄밀히 말하면 초기자 init은 값을 반환하지 않습니다. 그래서 비록 nil을 반환하는 return nil 코드에는 사용하지만 init이 성공하는 경우 return 키워드를 사용하지 않습니다. 다음 코드는 숫자형을 위해 정의돼 있는 실패 가능한 초기자 Int\(exactly:\)를 사용한 예제 입니다.

```swift
let wholeNumber: Double = 12345.0
let pi = 3.14159

if let valueMaintained = Int(exactly: wholeNumber) {
    print("\(wholeNumber) conversion to Int maintains value of \(valueMaintained)")
}
// Prints "12345.0 conversion to Int maintains value of 12345"

let valueChanged = Int(exactly: pi)
// valueChanged is of type Int?, not Int

if valueChanged == nil {
    print("\(pi) conversion to Int does not maintain value")
}
// Prints "3.14159 conversion to Int does not maintain value"
```

다음 코드는 초기자에 입력값이 없으면 초기화 실패가 발생하도록 구현한 예제입니다.

```swift
struct Animal {
    let species: String
    init?(species: String) {
        if species.isEmpty { return nil }
        self.species = species
    }
}
```

위에서 만든 Animal 구조체를 이용해 인스턴스를 생성하는데 동물의 종류를 Giraffe로 지정해 생성한 예입니다.

```swift
let someCreature = Animal(species: "Giraffe")
// someCreature is of type Animal?, not Animal

if let giraffe = someCreature {
    print("An animal was initialized with a species of \(giraffe.species)")
}
// Prints "An animal was initialized with a species of Giraffe"
```

다음은 Animal 생성시 인자를 넣지 않고 생성한 경우 초기화에 실패해서 생성된 인스턴스는 nil을 갖게 되는 것을 확인할 수 있습니다.

```swift
let anonymousCreature = Animal(species: "")
// anonymousCreature is of type Animal?, not Animal

if anonymousCreature == nil {
    print("The anonymous creature could not be initialized")
}
// Prints "The anonymous creature could not be initialized"
```

> NOTE  
> 빈것\(Empty\)과 nil은 다릅니다. 그래서 .isEmpty를 이용해 비교하지 않으면 인스턴스 초기화시 `“”`를 넣어도 초기화가 됩니다. 이것은 저희가 의도한 동작이 아니니 empty를 확인해서 nil을 반환하도록 구현해야 합니다.

### 열거형에서 사용하는 실패 가능한 초기자 \(Failable Initializers for Enumerations\)

열거형에서도 실패 가능한 초기자를 사용할 수 있습니다. 예제는 다음과 같습니다.

```swift
enum TemperatureUnit {
    case kelvin, celsius, fahrenheit
    init?(symbol: Character) {
        switch symbol {
        case "K":
            self = .kelvin
        case "C":
            self = .celsius
        case "F":
            self = .fahrenheit
        default:
            return nil
        }
    }
}
```

위 예제에서는 초기화시 지정된 특정 온도 표시 단위가 아닌 경우 초기화 결과로 nil을 반환합니다.

`TemperatureUnit(symbol: "F")`에서 F는 TemperatureUnit 열거형에서 사전에 정의돼 있는 단위여서 초기화가 정상적으로 수행됩니다. 반면 `TemperatureUnit(symbol: "X")`에서 X는 정의되지 않은 단위여서 초기화에 실패합니다.

```swift
let fahrenheitUnit = TemperatureUnit(symbol: "F")
if fahrenheitUnit != nil {
    print("This is a defined temperature unit, so initialization succeeded.")
}
// Prints "This is a defined temperature unit, so initialization succeeded."

let unknownUnit = TemperatureUnit(symbol: "X")
if unknownUnit == nil {
    print("This is not a defined temperature unit, so initialization failed.")
}
// Prints "This is not a defined temperature unit, so initialization failed."
```

### 열거형에서 Raw 값을 사용하는 실패 가능한 초기자 \(Failable Initializers for Enumerations with Raw Values\)

열거형의 각 케이스에 지정돼 있는 Raw값을 초기자 인자로 넣어 초기화에 사용할 수 있습니다.

```swift
enum TemperatureUnit: Character {
    case kelvin = "K", celsius = "C", fahrenheit = "F"
}

let fahrenheitUnit = TemperatureUnit(rawValue: "F")
if fahrenheitUnit != nil {
    print("This is a defined temperature unit, so initialization succeeded.")
}
// Prints "This is a defined temperature unit, so initialization succeeded."

let unknownUnit = TemperatureUnit(rawValue: "X")
if unknownUnit == nil {
    print("This is not a defined temperature unit, so initialization failed.")
}
// Prints "This is not a defined temperature unit, so initialization failed."
```

동작은 앞선 TemperatureUnit과 같습니다. 하지만 TemperatureUnit에서 초기자 구현이 훨씬 간단해 졌습니다.

### 초기자 실패의 생성 \(Propagation of Initialization Failure\)

실패가능한 초기자에서 실패가 발생하면 즉시 관련된 초기자가 중단 됩니다.

> NOTE  
> 실패가능한 초기자를 실패가 가능하지 않은 초기자에 위임할 수 있습니다. 이런 방식을 조합해 현재 존재하는 초기자를 특정 상황에만 실패하는 초기자로 만들 수 있습니다.

아래 코드는 Product의 name이 없거나 CartItem의 quantity가 1미만인 경우 초기화 실패를 하도록 구현한 예제입니다.

```swift
class Product {
    let name: String
    init?(name: String) {
        if name.isEmpty { return nil }
        self.name = name
    }
}

class CartItem: Product {
    let quantity: Int
    init?(name: String, quantity: Int) {
        if quantity < 1 { return nil }
        self.quantity = quantity
        super.init(name: name)
    }
}
```

아래 코드는 name도 있고 quantity도 1이상이므로 인스턴스 생성에 성공합니다.

```swift
if let twoSocks = CartItem(name: "sock", quantity: 2) {
    print("Item: \(twoSocks.name), quantity: \(twoSocks.quantity)")
}
// Prints "Item: sock, quantity: 2"
```

아래 코드는 인스턴스 생성시 quantity가 0이므로 초기화에 실패합니다.

```swift
if let zeroShirts = CartItem(name: "shirt", quantity: 0) {
    print("Item: \(zeroShirts.name), quantity: \(zeroShirts.quantity)")
} else {
    print("Unable to initialize zero shirts")
}
// Prints "Unable to initialize zero shirts"
```

아래 코드는 quantity는 1이상이지만 name이 비어있기 때문에 초기화에 실패합니다.

```swift
if let oneUnnamed = CartItem(name: "", quantity: 1) {
    print("Item: \(oneUnnamed.name), quantity: \(oneUnnamed.quantity)")
} else {
    print("Unable to initialize one unnamed product")
}
// Prints "Unable to initialize one unnamed product"
```

### 실패 가능한 초기자의 오버라이딩 \(Overriding a Failable Initializer\)

수퍼클래스의 실패가능한 초기자를 서브클래스에서 실패불가능한 초기자로 오버라이딩 할 수 있습니다. 이 방법을 이용해 실패불가능한 초기자를 생성 가능합니다.

> NOTE  
> 실패가능한 초기자를 실패불가능한 초기자에서 오버라이드 할 수 있지만, 그 반대는 불가능합니다.

아래 Document 클래스는 초기화를 할때, name값으로 특정 String 지정하거나 nil을 지정할 수 있습니다. 하지만 name값이 비어있는\(empty\) 경우에는 초기화 실패를 나타내는 nil을 반환합니다.

```swift
class Document {
    var name: String?
    // this initializer creates a document with a nil name value
    init() {}
    // this initializer creates a document with a nonempty name value
    init?(name: String) {
        if name.isEmpty { return nil }
        self.name = name
    }
}
```

Document를 서브클래싱한 AutomaticallyNamedDocument 클래스에서는 기본 초기자와 지정초기자를 오버라이딩해서 실패가능한 초기자를 실패불가능한 초기자로 만들었습니다. 초기 값이 없는 경우에는 name에 기본값으로 \[Untitled\]를 넣도록 했습니다.

```swift
class AutomaticallyNamedDocument: Document {
    override init() {
        super.init()
        self.name = "[Untitled]"
    }
    override init(name: String) {
        super.init()
        if name.isEmpty {
            self.name = "[Untitled]"
        } else {
            self.name = name
        }
    }
}
```

Document를 서브클래싱한 또다른 클래스 UntitledDocument에서는 기본 초기자를 오버라이딩해서 초기자를 구현했고 그 값이 옵셔널 값을 갖지 않도록 느낌표를 사용해 강제로 언래핑을 했습니다. `super.init(name: "[Untitled]")!`

```swift
class UntitledDocument: Document {
    override init() {
        super.init(name: "[Untitled]")!
    }
}
```

### 실패 가능한 init! 초기자 \(The init! Failable Initializer\)

실패 가능한 초기자 init?을 init!로 오버라이딩 할 수 있고 아니면 위임해서 사용할 수 있습니다.

## 필수 초기자 \(Required Initializers\)

모든 서브클래스에서 반드시 구현해야 하는 초기자에는 아래 예제와 같이 required 키워드를 붙여 줍니다.

```swift
class SomeClass {
    required init() {
        // initializer implementation goes here
    }
}
```

필수초기자를 상속받은 서브클래스에서도 반드시 required 키워드를 붙여서 다른 서브클래스에게도 이 초기자는 필수 초기자라는 것을 알려야 합니다.

```swift
class SomeSubclass: SomeClass {
    required init() {
        // subclass implementation of the required initializer goes here
    }
}
```

> NOTE  
> 필수초기자 표시를 해도 꼭 구현을 할 필요는 없습니다.

## 클로저나 함수를 이용해 기본 프로퍼티 값을 설정하기 \(Setting a Default Property Value with a Closure or Function\)

기본 값 설정이 단순히 값을 할당하는 것이 아니라 다소 복잡한 계산을 필요하다면 클로저나 함수를 이용해 값을 초기화 하는데 이용할 수 있습니다. 기본값을 지정하기 위해 클로저를 사용하는 형태의 코드는 보통 다음과 같습니다.

```swift
class SomeClass {
    let someProperty: SomeType = {
        // create a default value for someProperty inside this closure
        // someValue must be of the same type as SomeType
        return someValue
    }()
}
```

someProperty는 클로저가 실행된 후 반환 타입이 SomeType인 SomeValue를 기본 값으로 갖게 됩니다.

> NOTE  
> 클로저를 초기자에서 사용하면 클로저 안에 self나 다른 프로퍼티를 사용할 수 없습니다. 그 이유는 클로저가 실행될 시점에 다른 프로퍼티는 초기화가 다 끝난 것이 아니기 때문입니다.

![](../.gitbook/assets/chessboard_2x.png)

```swift
struct Chessboard {
    let boardColors: [Bool] = {
        var temporaryBoard = [Bool]()
        var isBlack = false
        for i in 1...8 {
            for j in 1...8 {
                temporaryBoard.append(isBlack)
                isBlack = !isBlack
            }
            isBlack = !isBlack
        }
        return temporaryBoard
    }()
    func squareIsBlackAt(row: Int, column: Int) -> Bool {
        return boardColors[(row * 8) + column]
    }
}
```

Chessboard에서 boardColors는 클로저를 이용해 8x8의 보드 색을 갖는 Bool 배열로 초기화 됩니다. 보드의 특정 행-열이 어떤 색인지 확인하는 함수 squareIsBlackAt\(row: Int, column: Int\) 가 제공됩니다.

예제 코드를 보시겠습니다.

```swift
let board = Chessboard()
print(board.squareIsBlackAt(row: 0, column: 1))
// Prints "true"
print(board.squareIsBlackAt(row: 7, column: 7))
// Prints "false"
```

`Chessboard()` 수행으로 8x8 보드 인스턴스가 생성됩니다. 생성된 보드에서 특정 행-열이 어떤 색인지 알아보기 위해 squareIsBlackAt\(row: Int, column: Int\) 함수를 호출합니다.

