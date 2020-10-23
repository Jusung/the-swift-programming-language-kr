# 상속 \(Inheritance\)

클래스는 메소드, 프로퍼티와 다른 특징\(characteristics\)을 다른 클래스로 부터 상속할 수 있습니다. 이것이 Swift에서 클래스가 다른 타입과 구분되는 근본적인 요소입니다. 클래스에서는 저장된 프로퍼티와 계산된 프로퍼티와 상관없이 상속받은 프로퍼티에 프로퍼티 옵저버를 설정해서 값 설정에 반응할 수 있습니다.

## 기반 클래스 정의 \(Defining a Base Class\)

다른 어떤 클래스로부터도 상속받지 않은 클래스를 기반 클래스라 합니다.

> NOTE  
> Objective-C에서는 모든 클래스가 `NSObject`클래스로부터 파생된 클래스로 생성되는 것과 달리 Swift에서는 SuperClass 지정없이 클래스 선언이 가능하고 그 클래스가 SuperClass가 됩니다.

예제를 통해 실제 사용에 대해 알아 보겠습니다.

```swift
class Vehicle {
    var currentSpeed = 0.0
    var description: String {
        return "traveling at \(currentSpeed) miles per hour"
    }
    func makeNoise() {
        // do nothing - an arbitrary vehicle doesn't necessarily make a noise
    }
}
```

위 코드는 탈것\(Vehicle\)이라는 기반 클래스를 하나 정의한 것입니다. 이 탈것은 현재속도\(currentSpeed\)를 프로퍼티로 갖고 있고 `description`을 계산된 프로퍼티로 선언했습니다. `makeNoise()`라는 메소드도 갖고 있습니다. 이 클래스를 이용해 탈것 객체를 하나 생성합니다. `let someVehicle = Vehicle()` `description`프로퍼티를 이용해 탈 것의 현재 상태를 확인합니다.

```swift
print("Vehicle: \(someVehicle.description)")
// Vehicle: traveling at 0.0 miles per hour
```

## 서브클래싱 \(Subclassing\)

상속, 다시말해 서브클래싱을 하면 부모로 부터 성격을 상속받고 자기 자신 고유의 특성도 추가할 수 있습니다. 서브클래싱해 새 클래스를 선언하는 코드는 다음과 같습니다.

```swift
class SomeSubclass: SomeSuperclass {
    // subclass definition goes here
}
```

위 탈것을 서브클래싱해 자전거라는 클래스를 선언하면 코드는 다음과 같습니다.

```swift
class Bicycle: Vehicle {
    var hasBasket = false
}
```

`Bicycle`은 `Vehicle` 서브클래싱하고 자기 자신의 속성 `hasBasket`도 추가합니다.

```swift
let bicycle = Bicycle()
bicycle.hasBasket = true
```

`Bicycle`객체를 생성해 고유 속성을 사용할 수 있는 것을 확인할 수 있습니다.

```swift
bicycle.currentSpeed = 15.0
print("Bicycle: \(bicycle.description)")
// Bicycle: traveling at 15.0 miles per hour
```

물론 부모로부터 물려받은 `currentSpeed`, `description`속성도 사용 가능합니다. 서브클래스로 생성된 클래스를 다시 서브클래싱 하는 것도 가능합니다.

```swift
class Tandem: Bicycle {
    var currentNumberOfPassengers = 0
}
```

`Bicycle`을 서브클래싱해서 `Tandem`이라는 클래스를 생성하고 속성도 추가합니다.

```swift
let tandem = Tandem()
tandem.hasBasket = true
tandem.currentNumberOfPassengers = 2
tandem.currentSpeed = 22.0
print("Tandem: \(tandem.description)")
// Tandem: traveling at 22.0 miles per hour
```

위 코드에서 `Tandem`객체 에서는 `Tandem`객체 고유 속성인 `currentNumberOfPassengers`와 자신의 부모 `Bicycle`의 속성 그리고 조부모인 `Vehicle`의 속성도 모두 사용 가능한 것을 확인할 수 있습니다.

## 오버라이딩 \(Overriding\)

서브클래스에서는 부모클래스에서 상속받은 것을 재정의 할 수 있습니다. 이것을 `overriding` 이라 부르는데, 오버라이딩은 인스턴스 메소드, 타입 메소드, 인스턴스 프로퍼티, 타입 프로퍼티, 서브스크립트 모두에 대해 가능합니다. 오버라이드를 위해서는 다른 선언 앞에 `override`키워드를 붙여줍니다. Swift에서는 이 `override`키워드를 보면 부모에 그 정의가 있는지 확인합니다.

## 부모클래스의 메소드, 프로퍼티, 서브스크립트의 접근 \(Accessing Superclass Methods, Properties, and Subscripts\)

`super`키워드와 점문법 혹은 인덱스 구문으로 부모 클래스의 메소드, 프로퍼티, 서브스크립트에 접근할 수 있습니다. `super.someMethod`, `super.someProperty`, `super[someIndex]`

### 메소드 오버라이드 \(Overriding Methods\)

상속받은 메소드를 오버라이드 하기 위해서는 메소드 선언 앞에 `override`키워드를 붙이고 내용을 적어주면 됩니다.

```swift
class Train: Vehicle {
    override func makeNoise() {
        print("Choo Choo")
    }
}
```

```swift
let train = Train()
train.makeNoise()
// Prints "Choo Choo" : 새로 정의한 내용이 출력됩니다.
```

### 프로퍼티 오버라이드 \(Overriding Properties\)

서브클래스에서는 상속받은 저장된 프로퍼티, 계산된 프로퍼티 모두 오버라이드 가능합니다. 오버라이드시에는 프로퍼티의 이름과 타입을 명시해야합니다. 왜냐하면 서브클래스에서는 단순히 상속받은 특정 형의 프로퍼티가 있다는 정도만 알고 있기 때문입니다. 상속받은 읽기전용\(read only\) 프로퍼티도 `getter/setter`를 정의해서 읽고/쓰기가 가능한\(read-write\) 프로퍼티로 변경해서 제공 가능합니다. 하지만 반대의 읽고/쓰기가 가능한 프로퍼티를 읽기전용 프로퍼티로 선언하는 것은 할 수 없습니다.

> NOTE  
> 만약 `setter`를 오버라이드 해서 제공한다면 반드시 `getter`도 제공해야 합니다.

계산된 프로퍼티를 오버라이딩한 \(예\) 입니다.

```swift
class Car: Vehicle {
    var gear = 1
    override var description: String {
        return super.description + " in gear \(gear)"
    }
}
```

오버라이딩한 프로퍼티가 재정의 한대로 출력되는 것을 확인할 수 있습니다.

```swift
let car = Car()
car.currentSpeed = 25.0
car.gear = 3
print("Car: \(car.description)")
// Car: traveling at 25.0 miles per hour in gear 3
```

### 프로퍼티 옵저버 오버라이드 \(Overriding Property Observers\)

이미 부모클래스에 선언된 프로퍼티 옵저버도 서브클래스에서 재정의해 사용할 수 있습니다.

> NOTE  
> 상수 프로퍼티와 읽기전용 프로퍼티에는 옵저버를 붙일 수 없습니다. 이 프로퍼티는 정의 그대로 `set`을 할 수 없는 프로퍼티이기 때문입니다. 또 같은 프로퍼티에 옵저버를 추가하고 `setter`를 추가해 둘을 동시에 사용할 수 없습니다. 이미 `setter`를 설정했다면 옵저버를 붙인 것과 같은 동작을 하기 때문입니다.

다음은 옵저버 오버라이드의 예제 코드입니다. 부모의 `currentSpeed` 계산된 프로퍼티를 오버라이드 했습니다.

```swift
class AutomaticCar: Car {
    override var currentSpeed: Double {
        didSet {
            gear = Int(currentSpeed / 10.0) + 1
        }
    }
}
```

코드 실행결과 오버라이드한 내용이 출력되는 것을 확인할 수 있습니다.

```swift
let automatic = AutomaticCar()
automatic.currentSpeed = 35.0
print("AutomaticCar: \(automatic.description)")
// AutomaticCar: traveling at 35.0 miles per hour in gear 4
```

## 오버라이드 방지 \(Preventing Overrides\)

서브클래스에서 특정 메소드, 프로퍼티, 서브스크립트가 오버라이드 되는 것을 방지하려면 `final`키워드를 사용합니다. 다시말해, `final`로 선언되면 `override`되는 것을 막을 수 있습니다. \(final func, final class func, final subscript\) 만일 `final`로 선언된 메소드, 프로퍼티, 서브스크립트를 오버라이드 하려고 하면 컴파일 시간\(compile-time\)에 에러가 발생합니다. 클래스 전체를 `final`로 선언해서 클래스 안의 모든 메소드, 프로퍼티 등이 `override`가 되는 것을 막을 수 있습니다. \(final class\)

