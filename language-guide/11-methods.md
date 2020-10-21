# 메소드 \(Methods\)

특정 타입의 클래스, 구조체, 열거형과 관련된 함수를 메소드라 합니다. 특정 타입의 인스턴스에서 실행할 수 있는 메소드를 인스턴스 메소드, 특정 형과 관련된 메소드를 타입 메소드라 합니다. 타입 메소드는 Objective-C에서 클래스 메소드와 유사합니다. Swift에서 메소드가 C나 Objective-C 메소드 간의 가장 큰 차이는 바로 Objective-C에서는 클래스 타입에서만 메소드를 선언할 수 있다는 것 입니다. 반면 Swift에서는 클래스 타입 뿐만아니라 구조체, 열거형에서도 메소드를 선언해 사용할 수 있습니다.

## 인스턴스 메소드 \(Instance Methods\)

인스턴스 메소드는 특정 클래스, 구조체, 열거형의 인스턴스에 속한 메소드입니다. 이 메소드를 통해 인스턴스 내의 값을 제어하거나 변경할 수 있습니다. 인스턴스 메소드는 이름 그대로 그 인스턴스가 속한 특정 타입의 인스턴스에서만 실행 가능합니다. 예제를 보시겠습니다.

```swift
class Counter {
    var count = 0
    func increment() {
        count += 1
    }
    func increment(by amount: Int) {
        count += amount
    }
    func reset() {
        count = 0
    }
}
```

`Counter`클래스를 선언하고 인스턴스 메소드로 각각 `increment()`, `increment(by amount: Int)`,`reset()` 를 정의해 인스턴스 내의 `count`property를 변경하는 기능을 수행합니다. 다음은 이 클래스를 사용한 \(예\)입니다.

```swift
let counter = Counter()
// 초기 count 값은 0입니다.
counter.increment()
// count 값이 1로 변경 됐습니다.
counter.increment(by: 5)
// count 값은 현재 6입니다.
counter.reset()
// count 값은 0이 됩니다.
```

### self 프로퍼티 \(The self Property\)

모든 프로퍼티는 암시적으로 인스턴스 자체를 의미하는 `self`라는 프로퍼티를 갖습니다. 인스턴스 메소드 안에서 `self`프로퍼티를 이용해 인스턴스 자체를 참조하는데 사용할 수 있습니다.

```swift
func increment() {
    self.count += 1
}
```

앞의 예제에서는 `increment()`메소드에서는 `count += 1`이라 표현했지만 사실은 `self.count += 1`의 의미 입니다. 이것이 가능한 이유는 Swift에서 특정 메소드에서 해당 인스턴스에 등록된 메소드나 프로퍼티를 호출하면 현재 인스턴스의 메소드나 프로퍼티를 사용하는 것으로 자동으로 가정하기 때문입니다. 위 규칙이 적용되지 않는 예외적인 상황이 있습니다. 바로 인자 이름이 프로퍼티 이름과 같은 경우 입니다. 이 경우에는 프로퍼티에 접근하기 위해 명시적으로 `self`키워드를 사용해야 합니다. 다음은 `self`키워드를 이용해 파라미터와 프로퍼티의 모호함을 명확하게 한 \(예\)입니다.

```swift
struct Point {
    var x = 0.0, y = 0.0
    func isToTheRightOf(x: Double) -> Bool {
        return self.x > x  // self.x를 이용해 프로퍼티 x와 인자 x를 구분
    }
}
let somePoint = Point(x: 4.0, y: 5.0)
if somePoint.isToTheRightOf(x: 1.0) {
    print("This point is to the right of the line where x == 1.0")
}
// "This point is to the right of the line where x == 1.0" 출력
```

위 `isToTheRightOf(x: Double)`메소드에서 `self.x`를 사용해 프로퍼티 `x`와 인자`x`를 구분했습니다. 만약 프로퍼티와 인자 이름이 같을 때 `self`키워드를 사용하지 않으면 Swift는 자동으로 인자 이름으로 가정합니다.

### 인스턴스 메소드 내에서 값 타입 변경 \(Modifying Value Types from Within Instance Methods\)

구조체와 열거형은 값 타입입니다. 그래서 기본적으로 인스턴스 메소드 내에서 값 타입의 프로퍼티를 변경할 수 없습니다. 하지만 값 타입 형의 메소드에서 프로퍼티를 변경하고 싶을 때가 있을 텐데요. 어떻게 해야할까요? 바로 메소드에 `mutating`붙여 주면 가능합니다. `mutating`이라는 키워드가 붙은 메소드에서는 메소드의 계산이 끝난 후 원본 구조체에 그 결과를 덮어 써서 그 값을 변경합니다.

```swift
struct Point {
    var x = 0.0, y = 0.0
    mutating func moveBy(x deltaX: Double, y deltaY: Double) {
        x += deltaX
        y += deltaY
    }
}
var somePoint = Point(x: 1.0, y: 1.0)
somePoint.moveBy(x: 2.0, y: 3.0)
print("The point is now at (\(somePoint.x), \(somePoint.y))")
// "The point is now at (3.0, 4.0)" 출력
```

위 `Point`는 구조체여서 메소드로 인스턴스의 값을 변경할 수 없지만 `mutating func moveBy()`같이 메소드 앞에 `mutating`을 붙여서 값을 변경할 수 있습니다. 한 가지 기억하실 부분은 여기서는 `Point` 인스턴스를 `var somePoint`로 선언해서 사용해서 가능했지만 만약 `let somePoint`으로 선언하면 구조체 단에서 변경이 불가능하다는 선언이기 때문에 mutating 선언과 상관없이 메소드로 값을 변경할 수 없습니다.

```swift
let fixedPoint = Point(x: 3.0, y: 3.0)
fixedPoint.moveBy(x: 2.0, y: 3.0)
// let으로 선언해서 값 변경 시도시 에러 발생!
```

### Mutating 메소드 내에서 self 할당 \(Assigning to self Within a Mutating Method\)

`Mutating`메소드에서 `self`프로퍼티를 이용해 완전 새로운 인스턴스를 생성할 수 있습니다. 위 메소드를 아래와 같이 작성할 수도 있습니다.

```swift
struct Point {
    var x = 0.0, y = 0.0
    mutating func moveBy(x deltaX: Double, y deltaY: Double) {
        self = Point(x: x + deltaX, y: y + deltaY)
    }
}
```

이 버전의 메소드의 동작은 앞의 동작과 완전히 동일합니다. 같은 열거형에서 `Mutating`메소드를 사용해 다른 상태를 표현할 수 있습니다.

```swift
enum TriStateSwitch {
    case off, low, high
    mutating func next() {
        switch self {
        case .off:
            self = .low
        case .low:
            self = .high
        case .high:
            self = .off
        }
    }
}
var ovenLight = TriStateSwitch.low
ovenLight.next()
// ovenLight 값은 .high
ovenLight.next()
// ovenLight 값은 .off
```

`ovenLight`인스턴스에서 `next()`메소드를 호출해 상태를 변경할 수 있습니다.

## 타입 메소드 \(Type Methods\)

인스턴스 메소드는 특정 타입의 인스턴스에서 호출되고, 타입 메소드는 특정 타입 자체에서 호출해 사용합니다. 타입 메소드의 선언은 메소드 키워드 `func`앞에 `static`혹은 `class`키워드를 추가하면 됩니다. `static`메소드와 `class`메소드의 차이점은 `static`메소드는 서브클래스에서 오버라이드 할 수 없는 타입 메소드 이고, `class`메소드는 서브클래스에서 오버라이드 할 수 있는 타입 메소드 라는 것입니다.

> Note  
> Objective-C에서는 클래스 타입에서만 타입 메소드를 선언할 수 있던 것에 반해 Swift에서는 클래스, 구조체, 열거형에서 모두 타입 메소드를 사용할 수 있습니다.

타입 메소드도 인스턴스 메소드와 같이 점\(.\) 문법으로 호출할 수 있습니다. 하지만 인스턴스에서 호출하는 것이 아니라 타입 이름에서 메소드를 호출합니다. 예제를 보시겠습니다.

```swift
class SomeClass {
    class func someTypeMethod() {
        // 타입 메소드 구현
    }
}
SomeClass.someTypeMethod()    // 타입 메소드 호출!
```

타입 메소드 안에서도 `self`키워드를 사용할 수 있습니다. 하지만 타입 메소드에서의 `self`는 인스턴스가 아니라 타입 자신을 의미합니다. 또 타입 메소드 안에서 다른 타입 메소드를 사용하는 것이 가능합니다. 코드를 보시겠습니다.

```swift
struct LevelTracker {
    static var highestUnlockedLevel = 1
    var currentLevel = 1

    static func unlock(_ level: Int) {
        if level > highestUnlockedLevel { highestUnlockedLevel = level }
    }

    static func isUnlocked(_ level: Int) -> Bool {
        return level <= highestUnlockedLevel
    }

    @discardableResult
    mutating func advance(to level: Int) -> Bool {
        if LevelTracker.isUnlocked(level) {
            currentLevel = level
            return true
        } else {
            return false
        }
    }
}
```

위 코드는 타입 메소드 2개와 `mutating`메소드 1개를 선언해 현재 레벨과, 최고 레벨, 다음 레벨 열기, 다음 레벨로 넘어가기 기능을 구현 했습니다. 그래서 게임에서 최고 레벨이 어디인지 추적하고 만약 그 레벨이 열려 있으면 그쪽으로 다른 사용자가 바로 넘어갈 수 있는\(advance\) 기능을 제공합니다. `advance`메소드 앞에 붙은 `@discardableResult`키워드는 리턴 값이 있는 메소드에서 리턴 값을 사용하지 않으면 컴파일러 경고가 발생하는데, 그 경고를 발생하지 않도록 해줍니다. 다시말해 `xx.advance`라고 하면 이 메소드를 실행하고나서 `Bool`값의 리턴 값을 어떤 변수에도 할당하지 않아 컴파일러 경고가 발생하지만 `@discardableResult`이라는 키워드를 붙여줘서 말 그대로 이 결과 값은 버릴 수 있는 결과 값이야 라고 표시를 해서 경고가 발생하지 않도록 합니다. 경고를 발생하지 않게하는 다른 방법으로는 `_ = xx.advance`형태로 호출하는 것도 가능합니다.

아래 \(예\)는 `Player`클래스의 `complete`메소드에서 `LevelTracker` 인스턴스 `tracker`와 타입메소드 `LevelTracker.unlock`를 사용해 특정 사용자의 현재 레벨을 추적하도록 구현 했습니다.

```swift
class Player {
    var tracker = LevelTracker()
    let playerName: String
    func complete(level: Int) {
        LevelTracker.unlock(level + 1)
        tracker.advance(to: level + 1)
    }
    init(name: String) {
        playerName = name
    }
}
```

```swift
var player = Player(name: "Argyrios")
player.complete(level: 1)
print("highest unlocked level is now \(LevelTracker.highestUnlockedLevel)")
// "highest unlocked level is now 2" 출력
```

레벨 1을 완료해서 레벨 2가 열렸습니다.

```swift
player = Player(name: "Beto")
if player.tracker.advance(to: 6) {
    print("player is now on level 6")
} else {
    print("level 6 has not yet been unlocked")
}
// "level 6 has not yet been unlocked" 출력
```

레벨 6으로 바로 건너뛰려 하지만 아직 열리지 않아 관련 메시지를 출력합니다.

