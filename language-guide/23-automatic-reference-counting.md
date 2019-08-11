# 자동 참조  카운트 \(Automatic Reference Counting\)

Swift에서는 앱의 메모리 사용을 관리하기 위해 ARC\(Automatic Reference Counting\)을 사용합니다. 자동으로 참조 횟수를 관리하기 때문에 대부분의 경우에 개발자는 메모리 관리에 신경쓸 필요가 없고 ARC가 알아서 더이상 사용하지 않는 인스턴스를 메모리에서 해지합니다. 하지만 몇몇의 경우 ARC에서 메모리 관리를 위해 코드의 특정 부분에 대한 관계에 대한 정보를 필요로 합니다. 참조 횟수는 클래스 타입의 인스턴스에만 적용되고 값 타입인 구조체 열거형 등에는 적용되지 않습니다.

## ARC의 동작 \(How ARC Works\)

클래스의 새 인스턴스를 만들 때마자 ARC는 인스턴스 정보를 담는데 필요한 적정한 크기의 메모리를 할당합니다. 이 메모리는 그 인스턴스에 대한 정보와 관련된 저장 프로퍼티 값도 갖고 있습니다. 추가적으로 인스턴스가 더이상 사용되지 않을 때 ARC는 그 인스턴스가 차지하고 있는 메모리를 해지해서 다른 용도로 사용할 수 있도록 공간을 확보해 둡니다. 하지만 만약 ARC가 아직 사용중인 인스턴스를 메모리에서 내렸는데 인스턴스의 프로퍼티에 접근한다면 앱은 아마 크래시가 발생하게됩니다. ARC에서는 아직 사용중인 인스턴스를 해지하지 않기 위해 얼마나 많은 프로퍼티, 상수 혹은 변수가 그 인스턴스에 대한 참조를 갖고 있는지 추적합니다. 그래서 ARC는 최소 하나라도 그 인스턴스에 대한 참조가 있는 경우 그 인스턴스를 메모리에서 해지 하지 않습니다.

## ARC의 사용 \(ARC in Action\)

ARC가 실제 어떻게 동작하는지 예제 코드를 통해 확인해 보도록 하겠습니다. 아래 코드는 하나의 클래스를 선언하고 클래스의 인스턴스가 생성될 때와 해지될때 print로 로그를 찍게 구현한 클래스 입니다.

```swift
class Person {
    let name: String
    init(name: String) {
        self.name = name
        print("\(name) is being initialized")
    }
    deinit {
        print("\(name) is being deinitialized")
    }
}
```

위에서 선언한 `Person`클래스 타입을 갖는 reference 변수 3개를 선언합니다. 이 변수는 모두 옵셔널 변수입니다. 그래서 초기값으로 모두 `nil`을 갖고 있습니다.

```swift
var reference1: Person?
var reference2: Person?
var reference3: Person?
```

하나의 변수에 Person 인스턴스를 생성에 참조하도록 합니다.

```swift
reference1 = Person(name: "John Appleseed")
// Prints "John Appleseed is being initialized"
```

나머지 두 변수를 첫 번째 변수를 참조하도록 합니다.

```swift
reference2 = reference1
reference3 = reference1
```

이 경우 `reference2`, `reference3`모두 처음에 `reference1`이 참조하고 있는 같은 `Person`인스턴스를 참조하게 됩니다. 이 시점에 `Person`인스턴스에 대한 참조 횟수는 3이 됩니다. 그리고 나서 `reference1`, `reference2`두 변수의 참조를 해지합니다. 그렇게 되면 `Person` 인스턴스에 대한 참조 횟수는 아직 1이어서 `Person`인스턴스는 해지되지는 않습니다.

```swift
reference1 = nil
reference2 = nil
```

`Person`인스턴스를 참조하고있는 나머지 변수 `reference3`의 참조를 해지하면 더이상 `Person`인스턴스를 참조하고있는 것이 없으므로 ARC가 `Person`인스턴스를 메모리에서 해지하게 됩니다.

```swift
reference3 = nil
// Prints "John Appleseed is being deinitialized"
```

위에 해지 로그가 찍히는 것으로 `Person`인스턴스가 메모리에서 내려 갔음을 확인할 수 있습니다.

## 클래스 인스턴스간 강한 참조 순환 \(Strong Reference Cycles Between Class Instances\)

앞선 예제에서 보았다시피 ARC에서 기본적으로 참조 횟수에 대해 추적하고 있기 때문에 더이상 사용하지 인스턴스는 자동으로 메모리에서 해제되게 됩니다. 하지만 절대로 메모리에서 해제 되지 않는 경우도 있습니다. 예를들어, 클래스의 인스턴스간 강하게 상호 참조를 하고 있는 경우가 바로 그경우 입니다. 이 경우는 강한 참조 순환이라 알려져 있습니다. 예를 통해 어떻게 강한 참조 순환이 발생하는지 알아 보겠습니다.

```swift
class Person {
    let name: String
    init(name: String) { self.name = name }
    var apartment: Apartment?
    deinit { print("\(name) is being deinitialized") }
}

class Apartment {
    let unit: String
    init(unit: String) { self.unit = unit }
    var tenant: Person?
    deinit { print("Apartment \(unit) is being deinitialized") }
}
```

위 예제 코드를 보시면 `Person`이라는 클래스는 변수로 `Apartment`클래스의 인스턴스를 소유하고 있고 그 `Apartment`클래스에서는 변수로 `Person`형의 인스턴스를 소유하고 있습니다. 만약 다음과 같이 변수를 선언하고 인스턴스를 생성하면 어떤 일이 발생할까요? `Person`과 `Apartment`형의 변수를 각각 선언합니다.

```swift
var john: Person?
var unit4A: Apartment?
```

선언한 변수에 각각에 맞는 타입의 인스턴스를 생성합니다. 지금 여기까지는 아무 문제가 없습니다.

```swift
john = Person(name: "John Appleseed")
unit4A = Apartment(unit: "4A")
```

현재까지의 변수와 인스턴스 상태를 그림으로 보면 다음과 같습니다. 변수 john은 Person 인스턴스를 참조하고 있고 변수 unit4A는 Apartment 인스턴스를 참조하고 있습니다.

![](../.gitbook/assets/referencecycle01_2x.png)

이 상황에서 john의 apartment 변수에 unit4A를 unit4A.tenant \(세입자\)에 john을 할당해 보겠습니다.

```swift
john!.apartment = unit4A
unit4A!.tenant = john
```

인스턴스 안의 apartment와 tenant가 각각 Apartment, Person 인스턴스를 참조하고 있는 상황이 됩니다 즉 Person 인스턴스의 참조 횟수는 2, Apartment의 인스턴스 참조 횟수도 마찬가지로 2가 됩니다.

![](../.gitbook/assets/referencecycle02_2x.png)

이 시점에서 각 변수에 nil을 할당해 참조를 해지해 보겠습니다. 원래 의도한 것은 각 변수가 참조하고 있던 Person과, Apartment인스턴스가 해지되는 것이었을 것입니다. 그러나 이 두 인스턴스는 해지되지 않습니다.

```swift
john = nil
unit4A = nil
```

각 변수에 nil을 할당한 시점에서의 참조 상황은 다음 그림과 같습니다. 변수 john과 unit4A는 각 인스턴스에 대한 참조를 하고 있지 않지만 Person인스턴스와 Apartment인스턴스의 변수가 각각 상호 참조를 하고 있어 참조 횟수가 1이기 때문에 이 두 인스턴스는 해지되지 않고 메모리 누수가 발생합니다.

![](../.gitbook/assets/referencecycle03_2x.png)

## 클래스 인스턴스간 강한 참조 순환 문제의 해결 \(Resolving Strong Reference Cycles Between Class Instances\)

앞에서 살펴본 강한 참조 순환 문제를 해결하기 위해서는 두가지 방법이 있습니다. 하나는 weak 참조, 다른 하나는 unowned 참조를 사용하는 것입니다. weak 참조, unowned 참조 모두 ARC에서 참조 횟수를 증가시키지 않고 인스턴스를 참조합니다. 그래서 강한 참조 순환 문제를 해결할 수 있습니다. 약한 참조는 참조하고 있는 인스턴스가 먼저 메모리에서 해제될때 사용하고 미소유 참조는 반대로 참조하고 있는 인스턴스가 같은 시점 혹은 더 뒤에 해제될때 사용합니다. 위 Apartment 예제에서 Apartment의 tenant는 없는 상태가 될 수 있기 때문에 \(먼저 해지되기 때문에\) 약한 참조를 사용하는 것이 적절합니다.

### 약한 참조 \(Weak References\)

약한 참조로 선언하면 참조하고 있는 것이 먼저 메모리에서 해제되기 때문에 ARC는 약한 참조로 선언된 참조 대상이 해지 되면 런타임에 자동으로 참조하고 있는 변수에 nil을 할당합니다.

> NOTE  
> ARC에서 약한 참조에 nil을 할당하면 프로퍼티 옵저버는 실행되지 않습니다.

예제를 살펴 보겠습니다. 아래 예제에서 Apartment의 tenant변수는 weak으로 선언됐습니다.

```swift
class Person {
    let name: String
    init(name: String) { self.name = name }
    var apartment: Apartment?
    deinit { print("\(name) is being deinitialized") }
}

class Apartment {
    let unit: String
    init(unit: String) { self.unit = unit }
    weak var tenant: Person?
    deinit { print("Apartment \(unit) is being deinitialized") }
}
```

그리고 앞선 예제처럼 Person 인스턴스와 Apartment 인스턴스의 변수에서 각각 인스턴스를 상호 참조하도록 할당합니다.

```swift
var john: Person?
var unit4A: Apartment?

john = Person(name: "John Appleseed")
unit4A = Apartment(unit: "4A")

john!.apartment = unit4A
unit4A!.tenant = john
```

그러면 아래와 같은 참조상황이 됩니다. 앞선 예제와 다른 점은 Apartment의 tenant변수가 Person 인스턴스를 약한 참조\(weak\)로 참조하고 있다는 것입니다. 그래서 이 시점에서 Person 인스턴스에 대한 참조 회수는 변수 john이 참조하고 있는 1회 뿐입니다.

![](../.gitbook/assets/weakreference01_2x.png)

그래서 john의 참조 대상을 nil로 할당하면 더 이상 Person 인스턴스를 참조하는 것이 없게 됩니다.

```swift
john = nil
// Prints "John Appleseed is being deinitialized"
```

그결과 ARC에서 아래 그림과 같이 Person 인스턴스를 메모리에서 해지합니다.

![](../.gitbook/assets/weakreference02_2x.png)

이 시점에서 변수 unit4A에 nil을 할당하면

```swift
unit4A = nil
// Prints "Apartment 4A is being deinitialized"
```

Apartment 인스턴스를 참조하는 개체도 사라지게 되서 Apartment 인스턴스도 메모리에서 해지됩니다.

![](../.gitbook/assets/weakreference03_2x.png)

> NOTE  
> 가비지 콜렉션을 사용하는 시스템에서 weak pointer를 단순한 시스템 캐싱 목적으로 사용하기도 합니다. 왜냐하면 메모리가 소모가 많아지면 가비지 콜렉터를 실행해서 강한 참조가 없는 객체를 메모리에서 해제하는 식으로 동작하기 때문입니다. 하지만 ARC는 이 경우와 다르게 참조 횟수가 0이 되는 즉시 해당 인스턴스를 제거하기 때문에 약한 참조를 이런 목적으로 사용할 수 없습니다.

### 미소유 참조 \(Unowned References\)

미소유 참조는 약한 참조와 다르게 참조 대상이 되는 인스턴스가 현재 참조하고 있는 것과 같은 생애주기\(lifetime\)를 갖거나 더 긴 생애 주기\(longer lifetime\)를 갖기 때문에 항상 참조에 그 값이 있다고 기대됩니다. 그래서 ARC는 미소유 참조에는 절대 nil을 할당하지 않습니다. 다시말하면 미소유 참조는 옵셔널 타입을 사용하지 않습니다.

> IMPORTANT  
> 미소유 참조는 참조 대상 인스턴스가 항상 존재한다고 생각하기 때문에 만약 미소유 참조로 선언된 인스턴스가 해제됐는데 접근하게 되면 런타임 에러가 발생합니다.

예제를 통해 미소유 참조의 동작을 확인해 보도록 하겠습니다. 우선 Customer와 CreditCard 두개의 클래스를 선언합니다.

```swift
class Customer {
    let name: String
    var card: CreditCard?
    init(name: String) {
        self.name = name
    }
    deinit { print("\(name) is being deinitialized") }
}

class CreditCard {
    let number: UInt64
    unowned let customer: Customer
    init(number: UInt64, customer: Customer) {
        self.number = number
        self.customer = customer
    }
    deinit { print("Card #\(number) is being deinitialized") }
}
```

여기서 Customer는 card 변수로 `CreditCard` 인스턴스를 참조하고 있고 CreditCard는 customer로 `Custome`인스턴스를 참조하고 있습니다. customer는 미소유 참조 unowned로 선언합니다. 이유는 고객과 신용카드를 비교해 봤을때 신용카드는 없더라도 사용자는 남아있을 것이기 때문입니다. 다시말하면 사용자는 항상 존재합니다. 그래서 CreditCard에 customer를 unowned로 선언합니다.

> NOTE  
> CreditCard에서 카드 번호인 number는 충분히 긴 숫자를 저장할 수 있도록 하기 위해 Int가 아닌 UInt로 사용합니다. UInt64형은 32비트, 64비트 시스템 모두에서 16자리 숫자를 저장할 수 있습니다.

이제 고객 변수 john을 옵셔널 타입으로 선언합니다.

```swift
var john: Customer?
```

선언한 고객에 인스턴스를 생성하고 고객의 카드변수에도 카드 인스턴스를 생성해 할당합니다.

```swift
john = Customer(name: "John Appleseed")
john!.card = CreditCard(number: 1234_5678_9012_3456, customer: john!)
```

이 시점에서의 참조 상황을 그림으로 표현하면 다음과 같습니다. John이 Customer 인스턴스를 참조하고 있고 CreditCard 인스턴스도 Customer Instance를 참조하고 있지만 미소유\(unowned\) 참조를 하고 있기 때문에 Customer 인스턴스에 대한 참조 횟수는 1회가 됩니다.

![](../.gitbook/assets/unownedreference01_2x.png)

이 상황에서 john 변수의 Customer 인스턴스 참조를 끊으면 다음 그림과 같이 됩니다.

![](../.gitbook/assets/unownedreference02_2x.png)

그러면 더이상 Customer 인스턴스를 강하게 참조하고 있는 인스턴스가 없으므로 Customer 인스턴가 해제되고 인스턴스가 해제됨에 따라 CreditCard 인스턴스를 참조하고 있는 개체도 사라지므로 CreditCard 인스턴스도 메모리에서 해제됩니다.

```swift
john = nil
// Prints "John Appleseed is being deinitialized"
// Prints "Card #1234567890123456 is being deinitialized"
```

> NOTE  
> 위 예제는 안전하게 미소유 참조를 사용하는 방법의 예였습니다. 반면 Swift에서는 런타임에 안전성 확인을 하지 않고 사용하는 unsafe 미소유 참조도 제공합니다. 이것을 제공해 주는 이유는 성능 때문입니다.

### 미소유 참조와 암시적 옵셔널 프로퍼티 언래핑 \(Unowned References and Implicitly Unwrapped Optional Properties\)

약한 참조, 미소유 참조의 구분을 해당 참조가 nil이 될 수 있느냐 없느냐로 구분할 수 있습니다. 하지만 이 두경우를 제외한 제 3의 경우도 발생할 수 있습니다. 두 프로퍼티가 항상 값을 갖지만 한번 초기화 되면 절대 nil이 되지 않는 경우 입니다. 이 경우에는 미소유 프로퍼티를 암시적 옵셔널 프로퍼티 언래핑을 사용해 참조 문제를 해결할 수 있습니다. 예제를 보겠습니다.

```swift
class Country {
    let name: String
    var capitalCity: City!
    init(name: String, capitalName: String) {
        self.name = name
        self.capitalCity = City(name: capitalName, country: self)
    }
}

class City {
    let name: String
    unowned let country: Country
    init(name: String, country: Country) {
        self.name = name
        self.country = country
    }
}
```

Country의 capitalCity는 초기화 단계에서 City 클래스에 초기화 된 후 사용되게 됩니다. 즉 실제로 Country의 capitalCity는 옵셔널이 돼야 맞습니다. 하지만 여기서는 느낌표 연산자\(!\)를 이용해 명시적으로 강제 언래핑을 시켰습니다. 그래서 암시적 언래핑이 돼서 Country에서 name이 초기화 되는 시점에 self를 사용할 수 있게 됩니다. 그리고 City에서는 강한 참조 순환을 피하기 위해 미소유 참조로 country를 선언해서 두 인스턴스를 문제없이 사용할 수 있습니다.

```swift
var country = Country(name: "Canada", capitalName: "Ottawa")
print("\(country.name)'s capital city is called \(country.capitalCity.name)")
// Prints "Canada's capital city is called Ottawa"
```

## 클로저에서의 강한 참조 순환 \(Strong Reference Cycles for Closures\)

강한 참조 순환은 변수 뿐만아니라 클로저와 관계돼서 발생할수도 있습니다. 왜냐하면 클로저에서는 self를 캡쳐하기 때문입니다. 이 문제를 해결 하기 위해서는 클로저 캡쳐 리스트를 사용합니다. 예제를 보겠습니다. 아래 HTMLElement 클래스의 클로저 asHTML는 입력 값을 받지 않고 반환값이 String인 `() -> String` 클로저를 사용합니다. 그리고 이 클로저 안에서 self.text와 self.name과 같이 self를 캡쳐하게 됩니다.

```swift
class HTMLElement {
    let name: String
    let text: String?
    lazy var asHTML: () -> String = {
        if let text = self.text {
            return "<\(self.name)>\(text)</\(self.name)>"
        } else {
            return "<\(self.name) />"
        }
    }
    init(name: String, text: String? = nil) {
        self.name = name
        self.text = text
    }
    deinit {
        print("\(name) is being deinitialized")
    }
}
```

asHTML 클로저는 아래와 같이 다른 클로저로 변경 될 수도 있습니다.

```swift
let heading = HTMLElement(name: "h1")
let defaultText = "some default text"
heading.asHTML = {
    return "<\(heading.name)>\(heading.text ?? defaultText)</\(heading.name)>"
}
print(heading.asHTML())
// Prints "<h1>some default text</h1>"
```

> NOTE  
> asHTML 클로저는 지연 프로퍼티로 선언됐습니다. 왜냐하면 HTML를 렌더링 하기 위해 필요한 태그와 텍스트가 준비되고 나서야 그것의 HTML이 필요하기 때문입니다. 또 지연 프로퍼티이기 때문에 프로퍼티 안에서 self를 참조할 수 있습니다.

이 코드를 실행하면 결과는 다음과 같습니다.

```swift
var paragraph: HTMLElement? = HTMLElement(name: "p", text: "hello, world")
print(paragraph!.asHTML())
// Prints "<p>hello, world</p>"
```

> NOTE  
> 위에서 paragraph 변수는 HTMLElement?로 선언됐습니다. 그래서 변수에 nil을 할당할 수 있지만 아래 그림과 같이 강한 참조 순환에 빠지게 됩니다.

아래와 같이 인스턴스와 클로저 간에 강한 참조를 하게돼서 강한 순한 참조에 빠지게 됩니다.

![](../.gitbook/assets/closurereferencecycle01_2x.png)

> NOTE  
> 클로저 안에서 self를 여러번 참조하더라도 실제로는 단 한번의 강한 참조만 캡쳐합니다.

아래와 같이 paragraph의 참조를 nil로 할당하더라도 HTMLElement인스턴스는 해제되지 않습니다.

```swift
paragraph = nil
```

## 클로저에서 강한 참조 순환 문제의 해결 \(Resolving Strong Reference Cycles for Closures\)

클로저에서 강한 참조 순환 문제의 해결하기 위해 캡쳐 참조에 강한 참조 대신 약한 참조\(weak\) 혹은 미소유\(unowend\) 참조를 지정할 수 있습니다. 약한 참조인지 미소유 참조를 사용할지는 코드에서 상호 관계에 달려있습니다.

> NOTE  
> Swift에서는 클로저에서 특정 self의 메소드를 사용할 때 캡쳐를 실수하는 것을 막기위해 someProperty 혹은 someMethod 대신 self.someProperty 혹은 self.someMethod와 같이 self를 명시하는 것을 필요로 합니다.

### 캡쳐리스트 정의 \(Defining a Capture List\)

캡처리스트를 정의하기 위해서는 클로저의 파라미터 앞에 소괄호\(\[\]\)를 넣고 그 안에 각 갭쳐 대상에 대한 참조 타입을 적어 줍니다.

```swift
lazy var someClosure: (Int, String) -> String = {
    [unowned self, weak delegate = self.delegate!] (index: Int, stringToProcess: String) -> String in
    // closure body goes here
}
```

클로저의 파라미터가 없고 반환 값이 추론에 의해 생략 가능한 경우에는 캡처리스트 정의를 in앞에 적어 줍니다.

```swift
lazy var someClosure: () -> String = {
    [unowned self, weak delegate = self.delegate!] in
    // closure body goes here
}
```

### 약한 참조와 미소유 참조 \(Weak and Unowned References\)

앞서 인스턴스 참조와 마찬가지로 참조가 먼저 해제되는 경우는 약한 참조를 같은 시점이나 나중 시점에 해제되는 경우에는 미소유 참조를 사용합니다

> NOTE  
> 만약 캡쳐리스트가 절대 nil이 될 수 없다면 그것은 반드시 약한 참조 리스트가 아니라 미소유 참조 리스트로 캡쳐돼야 합니다.

자 이제 클로저에 적절한 캡쳐 리스트를 적어 코드를 실행해 보도록 하겠습니다. asHTML 클로저의 self에 \[unowned self\]라고 캡쳐리스트를 아래 코드와 같이 적어줍니다.

```swift
class HTMLElement {
    let name: String
    let text: String?
    lazy var asHTML: () -> String = {
        [unowned self] in
        if let text = self.text {
            return "<\(self.name)>\(text)</\(self.name)>"
        } else {
            return "<\(self.name) />"
        }
    }
    init(name: String, text: String? = nil) {
        self.name = name
        self.text = text
    }
    deinit {
        print("\(name) is being deinitialized")
    }
}
```

앞서와 같이 인스턴스를 생성해 실행합니다.

```swift
var paragraph: HTMLElement? = HTMLElement(name: "p", text: "hello, world")
print(paragraph!.asHTML())
// Prints "<p>hello, world</p>"
```

참조 상황을 그림으로 살펴보면 앞서와는 다르게 클로저에서 HTMLElement 인스턴스를 미소유 참조로 참조하고 있습니다.

![](../.gitbook/assets/closurereferencecycle02_2x.png)

그래서 paragraph의 참조를 제거 하면 HTMLElement 인스턴스가 바로 메모리에서 해제되는 것을 확인할 수 있습니다.

```swift
paragraph = nil
// Prints "p is being deinitialized"
```

