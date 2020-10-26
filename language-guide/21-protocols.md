# 프로토콜 \(Protocols\)

프로토콜은 특정 기능 수행에 필수적인 요수를 청의한 청사진\(blueprint\)입니다. 프로토콜을 만족시키는 타입을 프로토콜을 따른다\(conform\)고 말합니다. 프로토콜에 필수 구현을 추가하거나 추가적인 기능을 더하기 위해 프로토콜을 확장\(extend\)하는 것이 가능합니다.

## 프토토콜 문법 \(Protocol Syntax\)

프로토콜의 정의는 클래스, 구조체, 열거형 등과 유사합니다.

```swift
protocol SomeProtocol {
    // protocol definition goes here
}
```

프로토콜을 따르는 타입을 정의하기 위해서는 타입 이름 뒤에 콜론\(:\)을 붙이고 따를\(Conforming\) 프로토콜 이름을 적습니다. 만약 따르는 프로토콜이 여러개라면 콤마\(,\)로 구분해 줍니다.

```swift
struct SomeStructure: FirstProtocol, AnotherProtocol {
    // structure definition goes here
}
```

서브클래싱인 경우 수퍼클래스를 프로토콜 앞에 적어 줍니다.

```swift
class SomeClass: SomeSuperclass, FirstProtocol, AnotherProtocol {
    // class definition goes here
}
```

## 프로퍼티 요구사항 \(Property Requirements\)

프로토콜에서는 프로퍼티가 저장된 프로퍼티인지 계산된 프로퍼티인지 명시하지 않습니다. 하지만 프로퍼티의 이름과 타입 그리고 gettable, settable한지는 명시합니다. 필수 프로퍼티는 항상 var로 선언해야 합니다.

```swift
protocol SomeProtocol {
    var mustBeSettable: Int { get set }
    var doesNotNeedToBeSettable: Int { get }
}
```

타입 프로퍼티는 static 키워드를 적어 선언합니다.

```swift
protocol AnotherProtocol {
    static var someTypeProperty: Int { get set }
}
```

하나의 프로퍼티를 갖는 프로토콜을 선언합니다.

```swift
protocol FullyNamed {
    var fullName: String { get }
}
```

이 프로토콜을 따르는 구조체를 선언합니다. `fullName` 프로퍼티는 저장된 프로퍼티로 사용될 수 있고,

```swift
struct Person: FullyNamed {
    var fullName: String
}
let john = Person(fullName: "John Appleseed")
// john.fullName is "John Appleseed"
```

다음과 같이 계산된 프로퍼티로 사용될 수 있습니다.

```swift
class Starship: FullyNamed {
    var prefix: String?
    var name: String
    init(name: String, prefix: String? = nil) {
        self.name = name
        self.prefix = prefix
    }
    var fullName: String {
        return (prefix != nil ? prefix! + " " : "") + name
    }
}
var ncc1701 = Starship(name: "Enterprise", prefix: "USS")
// ncc1701.fullName is "USS Enterprise"
```

## 메소드 요구사항 \(Method Requirements\)

프로토콜에서는 필수 인스턴스 메소드와 타입 메소드를 명시할 수 있습니다. 하지만 메소드 파라미터의 기본 값은 프로토콜 안에서 사용할 수 없습니다.

```swift
protocol SomeProtocol {
    static func someTypeMethod()
}
```

필수 메소드 지정시 함수명과 반환값을 지정할 수 있고, 구현에 사용하는 괄호는 적지 않아도 됩니다.

```swift
protocol RandomNumberGenerator {
    func random() -> Double
}
```

다음 코드는 따르는 프로토콜의 필수 메소드 `random()`을 구현한 클래스입니다.

```swift
class LinearCongruentialGenerator: RandomNumberGenerator {
    var lastRandom = 42.0
    let m = 139968.0
    let a = 3877.0
    let c = 29573.0
    func random() -> Double {
        lastRandom = ((lastRandom a + c).truncatingRemainder(dividingBy:m))
        return lastRandom / m
    }
}
let generator = LinearCongruentialGenerator()
print("Here's a random number: \(generator.random())")
// Prints "Here's a random number: 0.3746499199817101"
print("And another one: \(generator.random())")
// Prints "And another one: 0.729023776863283"
```

## 변경 가능한 메소드 요구사항 \(Mutating Method Requirements\)

mutating 키워드를 사용해 인스턴스에서 변경 가능하다는 것을 표시할 수 있습니다. 이 mutating 키워드는 값타입 형에만 사용합니다.

> NOTE  
> 프로토콜에 mutating을 명시한 경우 이 프로토콜을 따르는 클래스 형을 구현할 때는 메소드에 mutating을 명시하지 않아도 됩니다. mutating은 값타입 형에만 사용합니다.

다음 코드는 mutating 메소드를 선언한 프로토콜의 예입니다.

```swift
protocol Togglable {
    mutating func toggle()
}
```

이 프로토콜을 따르는 값타입 형에서 `toggle()`메소드를 변경해 사용할 수 있습니다.

```swift
enum OnOffSwitch: Togglable {
    case off, on
    mutating func toggle() {
        switch self {
        case .off:
            self = .on
        case .on:
            self = .off
        }
    }
}
var lightSwitch = OnOffSwitch.off
lightSwitch.toggle()
// lightSwitch is now equal to .on
```

## 초기자 요구사항 \(Initializer Requirements\)

프로토콜에서 필수로 구현해야하는 이니셜라이저를 지정할 수 있습니다.

```swift
protocol SomeProtocol {
    init(someParameter: Int)
}
```

### 클래스에서 프로토콜 필수 이니셜라이저의 구현 \(Class Implementation of Protocol Initializer Requirements\)

프로토콜에서 특정 이니셜라이저가 필요하다고 명시했기 때문에 구현에서 해당 이니셜라이저에 required 키워드를 붙여줘야 합니다.

```swift
class SomeClass: SomeProtocol {
    required init(someParameter: Int) {
        // initializer implementation goes here
    }
}
```

> NOTE  
> 클래스 타입에서 final로 선언된 것에는 required를 표시하지 않아도 됩니다. final로 선언되면 서브클래싱 되지 않기 때문입니다.

특정 프로토콜의 필수 이니셜라이저를 구현하고, 수퍼클래스의 이니셜라이저를 서브클래싱하는 경우 이니셜라이저 앞에 required 키워드와 override 키워드를 적어줍니다.

```swift
protocol SomeProtocol {
    init()
}

class SomeSuperClass {
    init() {
        // initializer implementation goes here
    }
}

class SomeSubClass: SomeSuperClass, SomeProtocol {
    // "required" from SomeProtocol conformance; "override" from SomeSuperClass
    required override init() {
        // initializer implementation goes here
    }
}
```

### 실패가능한 초기자 요구사항 \(Failable Initializer Requirements\)

프로토콜에서 실패가능한 이니셜라이저를 선언할 수 있습니다.

## 타입으로써의 프로토콜 \(Protocols as Types\)

프로토콜도 하나의 타입으로 사용됩니다. 그렇기 때문에 다음과 같이 타입 사용이 허용되는 모든 곳에 프로토콜을 사용할 수 있습니다.

* 함수, 메소드, 이니셜라이저의 파라미터 타입 혹은 리턴 타입
* 상수, 변수, 프로퍼티의 타입
* 컨테이너인 배열, 사전 등의 아이템 타입

> NOTE  
> 프로토콜은 타입이기 때문에 위 예제의 FullyNamed, RandomNumberGenerator와 같이 첫 글자를 대문자로 적어줍니다.

다음은 프로토콜을 타입으로 사용한 예입니다.

```swift
class Dice {
    let sides: Int
    let generator: RandomNumberGenerator
    init(sides: Int, generator: RandomNumberGenerator) {
        self.sides = sides
        self.generator = generator
    }
    func roll() -> Int {
        return Int(generator.random() Double(sides)) + 1
    }
}
```

RandomNumberGenerator를 generator 상수의 타입으로 그리고 이니셜라이저의 파라미터 형으로 사용했습니다.

위에서 선언한 Dice를 초기화 할 때 generator 파라미터 부분에 RandomNumberGenerator 프로토콜을 따르는 인스턴스를 넣습니다.

```swift
var d6 = Dice(sides: 6, generator: LinearCongruentialGenerator())
for _ in 1...5 {
    print("Random dice roll is \(d6.roll())")
}
// Random dice roll is 3
// Random dice roll is 5
// Random dice roll is 4
// Random dice roll is 5
// Random dice roll is 4
```

## 위임 \(Delegation\)

위임은 클래스 혹은 구조체 인스턴스에 특정 행위에 대한 책임을 넘길 수 있게 해주는 디자인 패턴 중 하나입니다.

```swift
protocol DiceGame {
    var dice: Dice { get }
    func play()
}
protocol DiceGameDelegate: AnyObject {
    func gameDidStart(_ game: DiceGame)
    func game(_ game: DiceGame, didStartNewTurnWithDiceRoll diceRoll: Int)
    func gameDidEnd(_ game: DiceGame)
}
```

DiceGame 프로토콜을 선언하고 DiceGameDelegate에 선언해서 실제 DiceGame의 행위와 관련된 구현을 DiceGameDelegate를 따르는 인스턴스에 위임합니다. DiceGameDelegate를 AnyObject로 선언하면 클래스만 이 프로토콜을 따를 수 있게 만들 수 있습니다.

```swift
class SnakesAndLadders: DiceGame {
    let finalSquare = 25
    let dice = Dice(sides: 6, generator: LinearCongruentialGenerator())
    var square = 0
    var board: [Int]
    init() {
        board = Array(repeating: 0, count: finalSquare + 1)
        board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
        board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
    }
    weak var delegate: DiceGameDelegate?
    func play() {
        square = 0
        delegate?.gameDidStart(self)
        gameLoop: while square != finalSquare {
            let diceRoll = dice.roll()
            delegate?.game(self, didStartNewTurnWithDiceRoll: diceRoll)
            switch square + diceRoll {
            case finalSquare:
                break gameLoop
            case let newSquare where newSquare > finalSquare:
                continue gameLoop
            default:
                square += diceRoll
                square += board[square]
            }
        }
        delegate?.gameDidEnd(self)
    }
}
```

SnakesAndLadders는 DiceGame를 따르고 DiceGameDelegate를 따르는 델리게이트 delegate를 갖습니다. 게임을 실행\(play\(\)\) 했을 때 `delegate?.gameDidStart(self)`, `delegate?.game(self, didStartNewTurnWithDiceRoll: diceRoll)`, `delegate?.gameDidEnd(self)`를 실행합니다. delegate는 게임을 진행시키는데 반드시 필요한 건 아니라서 옵셔널로 정의돼 있습니다. 아래는 실제 DiceGameDelegate를 상속하는 delegate DiceGameTracker를 구현한 예입니다.

```swift
class DiceGameTracker: DiceGameDelegate {
    var numberOfTurns = 0
    func gameDidStart(_ game: DiceGame) {
        numberOfTurns = 0
        if game is SnakesAndLadders {
            print("Started a new game of Snakes and Ladders")
        }
        print("The game is using a \(game.dice.sides)-sided dice")
    }
    func game(_ game: DiceGame, didStartNewTurnWithDiceRoll diceRoll: Int) {
        numberOfTurns += 1
        print("Rolled a \(diceRoll)")
    }
    func gameDidEnd(_ game: DiceGame) {
        print("The game lasted for \(numberOfTurns) turns")
    }
}
```

DiceGameTracker를 이용해 게임을 진행시킵니다. 게임의 tracking관련된 작업은 이 DiceGameTracker가 위임받아 그곳에서 실행됩니다.

```swift
let tracker = DiceGameTracker()
let game = SnakesAndLadders()
game.delegate = tracker
game.play()
// Started a new game of Snakes and Ladders
// The game is using a 6-sided dice
// Rolled a 3
// Rolled a 5
// Rolled a 4
// Rolled a 5
// The game lasted for 4 turns
```

## 익스텐션을 이용해 프로토콜 따르게 하기 \(Adding Protocols Conformance with an Extension\)

이미 존재하는 타입에 새 프로토콜을 따르게 하기 위해 익스텐션을 사용할 수 있습니다. 원래 값에 접근 권한이 없어도 익스텐션을 사용해 기능을 확장할 수 있습니다.

> NOTE 이미 존재하는 타입에 익스텐션이 추가되면 자동으로 그 타입에서 추가된 익스텐션의 기능을 사용할 수 있습니다.

```swift
protocol TextRepresentable {
    var textualDescription: String { get }
}
```

익스텐션을 이용해 Dice를 TextRepresentable 프로토콜을 따르도록 구현하면 다음과 같습니다.

```swift
extension Dice: TextRepresentable {
    var textualDescription: String {
        return "A \(sides)-sided dice"
    }
}
```

아래와 코드의 `d12.textualDescription`같이 Dice에 추가한 익스텐션을 자동으로 그대로 사용할 수 있습니다.

```text
let d12 = Dice(sides: 12, generator: LinearCongruentialGenerator())
print(d12.textualDescription)
// Prints "A 12-sided dice"
```

Dice와 같이 SnakesAndLadders도 TextRepresentable을 따르도록 구현할 수 있습니다.

```swift
extension SnakesAndLadders: TextRepresentable {
    var textualDescription: String {
        return "A game of Snakes and Ladders with \(finalSquare) squares"
    }
}
print(game.textualDescription)
// Prints "A game of Snakes and Ladders with 25 squares"
```

### 조건적으로 프로토콜을 따르기 \(Conditionally Conforming to a Protocol\)

특정 조건을 만족시킬때만 프로토콜을 따르도록 제한할 수 있습니다. 이 선언은 where 절을 사용해 정의합니다. 아래 예제는 TextRepresentable을 따르는 Array중에 Array의 각 원소가 TextRepresentable인 경우에만 따르는 프로토콜을 정의합니다. textualDescription은 Array의 각 원소가 TextRepresentable를 따르게 때문에 `textualDescription` 프로퍼티를 사용할 수 있습니다. textualDescription는 Array의 모든 아이템을 순회하고 각각의 `textualDescription`를 결합해 반환하는 메소드입니다.

```swift
extension Array: TextRepresentable where Element: TextRepresentable {
    var textualDescription: String {
        let itemsAsText = self.map { $0.textualDescription }
        return "[" + itemsAsText.joined(separator: ", ") + "]"
    }
}
let myDice = [d6, d12]
print(myDice.textualDescription)
// Prints "[A 6-sided dice, A 12-sided dice]"
```

### 익스텐션을 이용해 프로토콜 채용 선언하기 \(Declaring Protocol Adoption with an Extension\)

만약 어떤 프로토콜을 충족에 필요한 모든 조건을 만족하지만 아직 그 프로토콜을 따른다는 선언을 하지 않았다면 그 선언을 빈 익스텐션으로 선언할 수 있습니다. 아래 코드는 프로토콜을 따른 다는 선언은 익스텐션에 하고 실제 프로토콜을 따르기 위한 구현은 구조체 원본에 구현한 예 입니다.

```swift
struct Hamster {
    var name: String
    var textualDescription: String {
        return "A hamster named \(name)"
    }
}
extension Hamster: TextRepresentable {}
```

Hamster 인스턴스인 simonTheHamster는 이제 TextRepresentable 타입으로 사용할 수 있습니다.

```swift
let simonTheHamster = Hamster(name: "Simon")
let somethingTextRepresentable: TextRepresentable = simonTheHamster
print(somethingTextRepresentable.textualDescription)
// Prints "A hamster named Simon"
```

> NOTE  
> 프로토콜의 요구사항을 기술하는 것만으로 프로토콜 사용의 조건을 충족시킬 수 없습니다. 반드시 어떤 프로토콜을 따르는지에 대해 기술해야 합니다.

## 프로토콜 타입 콜렉션 \(Collections of Protocol Types\)

프로토콜을 Array, Dictionary등 Collection 타입에 넣기위한 타입으로 사용할 수 있습니다. 아래는 TextRepresentable 프로토콜을 따르는 객체 Array에 대한 선언입니다.

```swift
let things: [TextRepresentable] = [game, d12, simonTheHamster]
```

Array의 모든 객체는 TextRepresentable를 따르므로 textualDescription프로퍼티를 갖습니다.

```swift
for thing in things {
    print(thing.textualDescription)
}
// A game of Snakes and Ladders with 25 squares
// A 12-sided dice
// A hamster named Simon
```

## 프로토콜 상속 \(Protocol Inheritance\)

클래스 상속같이 프로토콜도 상속할 수 있습니다. 여러 프로토콜을 상속받는 경우 콤마\(,\)로 구분합니다.

```swift
protocol InheritingProtocol: SomeProtocol, AnotherProtocol {
    // protocol definition goes here
}
```

위의 TextRepresentable 프로토콜을 상속받아 새로운 프로토콜 PrettyTextRepresentable을 구현합니다. 이 프로토콜을 text를 예쁘게 보여주는 프로퍼티를 구현합니다.

```swift
protocol PrettyTextRepresentable: TextRepresentable {
    var prettyTextualDescription: String { get }
}
```

SnakesAndLadders 클래스에서 위에서 선언한 PrettyTextRepresentable 프로토콜을 따르도록 선언하고 prettyTextualDescription 프로퍼티를 아래와 같이 구현합니다.

```swift
extension SnakesAndLadders: PrettyTextRepresentable {
    var prettyTextualDescription: String {
        var output = textualDescription + ":\n"
        for index in 1...finalSquare {
            switch board[index] {
            case let ladder where ladder > 0:
                output += "▲ "
            case let snake where snake < 0:
                output += "▼ "
            default:
                output += "○ "
            }
        }
        return output
    }
}
```

square가 0보다 큰지, 작은지 혹은 0인지에 대해 구분해 각각 알맞은 기호를 반환합니다.

```swift
print(game.prettyTextualDescription)
// A game of Snakes and Ladders with 25 squares:
// ○ ○ ▲ ○ ○ ▲ ○ ○ ▲ ▲ ○ ○ ○ ▼ ○ ○ ○ ○ ▼ ○ ○ ▼ ○ ▼ ○
```

## 클래스 전용 프로토콜 \(Class-Only Protocols\)

구조체, 열거형에서 사용하지 않고 클래스 타입에만 사용가능한 프로토콜을 선언하기 위해서는 프로토콜에 AnyObject를 추가합니다.

```swift
protocol SomeClassOnlyProtocol: AnyObject, SomeInheritedProtocol {
    // class-only protocol definition goes here
}
```

> NOTE  
> 참조 구문만 필요한 프로토콜을 정의하기 위해 클래스 전용 프로토콜을 사용합니다.

## 프로토콜 합성 \(Protocol Composition\)

동시에 여러 프로토콜을 따르는 타입을 선언할 수 있습니다. 아래 Person은 Named와 Aged 프로토콜을 동시에 따르는 구조체입니다.

```swift
protocol Named {
    var name: String { get }
}
protocol Aged {
    var age: Int { get }
}
struct Person: Named, Aged {
    var name: String
    var age: Int
}
func wishHappyBirthday(to celebrator: Named & Aged) {
    print("Happy birthday, \(celebrator.name), you're \(celebrator.age)!")
}
let birthdayPerson = Person(name: "Malcolm", age: 21)
wishHappyBirthday(to: birthdayPerson)
// Prints "Happy birthday, Malcolm, you're 21!"
```

wishHappyBirthday 메소드의 celebrator파라미터는 Named 프로토콜과 Aged 프로토콜을 동시에 따르는 타입으로 선언하기 위해 Named & Aged로 표시했습니다. 아래 예제는 Location 프로토콜과 위의Named 프로토콜을 따르는 City 클래스를 구현한 예입니다.

```swift
class Location {
    var latitude: Double
    var longitude: Double
    init(latitude: Double, longitude: Double) {
        self.latitude = latitude
        self.longitude = longitude
    }
}
class City: Location, Named {
    var name: String
    init(name: String, latitude: Double, longitude: Double) {
        self.name = name
        super.init(latitude: latitude, longitude: longitude)
    }
}
func beginConcert(in location: Location & Named) {
    print("Hello, \(location.name)!")
}

let seattle = City(name: "Seattle", latitude: 47.6, longitude: -122.3)
beginConcert(in: seattle)
// Prints "Hello, Seattle!"
```

## 프로토콜 순응 확인 \(Checking for Protocol Conformance\)

어떤 타입이 특정 프로토콜을 따르는지 다음과 같은 방법으로 확인 할 수 있습니다.

* `is`연산자를 이용하면 어떤 타입이 특정 프로토콜을 따르는지 확인할 수 있습니다. 특정 프로토콜을 따르면 true를 아니면 false를 반환합니다.
* `as?`는 특정 프로토콜 타입을 따르는 경우 그 옵셔널 타입의 프로토콜 타입으로 다운캐스트를 하게 되고 따르지 않는 경우는 nil을 반환합니다.
* `as!`는 강제로 특정 프로토콜을 따르도록 정의합니다. 만약 다운캐스트에 실패하면 런타임 에러가 발생합니다.

관련한 예제를 살펴보도록 하겠습니다. area라는 값을 필요로 하는 HasArea 프로토콜을 선언합니다.

```swift
protocol HasArea {
    var area: Double { get }
}
```

HasArea 프로토콜을 따르는 Circle클래스와 Country클래스를 선언합니다. Circle클래스에서는 area프로퍼티를 계산된 프로퍼티로 구현했고, Country클래스에서는 저장프로퍼티로 구현합니다.

```swift
class Circle: HasArea {
    let pi = 3.1415927
    var radius: Double
    var area: Double { return pi radius radius }
    init(radius: Double) { self.radius = radius }
}
class Country: HasArea {
    var area: Double
    init(area: Double) { self.area = area }
}
```

HasArea를 따르지 않는 Animal이란 클래스도 선언합니다.

```swift
class Animal {
    var legs: Int
    init(legs: Int) { self.legs = legs }
}
```

Circle, Country, Animal의 인스턴스를 objects라는 배열에 넣습니다.

```swift
let objects: [AnyObject] = [
    Circle(radius: 2.0),
    Country(area: 243_610),
    Animal(legs: 4)
]
```

objects 배열을 순회하며 `as? HasArea` 구문을 사용해 HasArea프로토콜을 따르는지 확인하고 따르는 경우 HasArea 타입으로 다운캐스트 합니다.

```swift
for object in objects {
    if let objectWithArea = object as? HasArea {
        print("Area is \(objectWithArea.area)")
    } else {
        print("Something that doesn't have an area")
    }
}
// Area is 12.5663708
// Area is 243610.0
// Something that doesn't have an area
```

결과로 처음 두 인스턴스는 HasArea를 따르기 때문에 area값이 반환되고 마지막 인스턴스는 HasArea를 따르지 않기 때문에 else 절이 실행됩니다.

## 선택적 프로토콜 요구조건 \(Optional Protocol Requirements\)

프로토콜을 선언하면서 필수 구현이 아닌 선택적 구현 조건을 정의할 수 있습니다. 이 프로토콜의 정의를 위해서 @objc키워드를 프로토콜 앞에 붙이고, 개별 함수 혹은 프로퍼티에는 @objc와 optional 키워드를 붙입니다. @objc 프로토콜은 클래스 타입에서만 채용될 수 있고 구조체나 열거형에서는 사용할 수 없습니다. 다음은 두가지 선택적 구현을 할 수 있는 CounterDataSource 프로토콜의 예 입니다.

```swift
@objc protocol CounterDataSource {
    @objc optional func increment(forCount count: Int) -> Int
    @objc optional var fixedIncrement: Int { get }
}
```

> NOTE  
> 엄격하게 말해 CounterDataSource를 따르면서 구현은 하나도 하지 않는 클래스를 선언할 수는 있습니다. 기술적으로는 가능하지만 이렇게 구현하는 것은 좋은 방법이 아닙니다. 만약 하나도 구현하지 않는다면 필요하지 않다는 것 이므로 굳이 CounterDataSource 프로토콜을 따르도록 선언할 필요가 없습니다.

아래 코드는 CounterDataSource 를 따르는 dataSource를 선언한 예입니다.

```swift
class Counter {
    var count = 0
    var dataSource: CounterDataSource?
    func increment() {
        if let amount = dataSource?.increment?(forCount: count) {
            count += amount
        } else if let amount = dataSource?.fixedIncrement {
            count += amount
        }
    }
}
```

`increment?(forCount: count)`와 `fixedIncrement`는 옵셔널이므로 구현이 안돼있을 수 있기 때문에 옵셔널 체이닝을 이용해 확인해 봅니다. 다음 코드는 CounterDataSource를 따르는 예입니다.

```swift
class ThreeSource: NSObject, CounterDataSource {
    let fixedIncrement = 3
}
```

Counter 인스턴스의 dataSource를 ThreeSource로부터 입력받아 그 값을 증가시킬 수 있습니다.

```swift
var counter = Counter()
counter.dataSource = ThreeSource()
for _ in 1...4 {
    counter.increment()
    print(counter.count)
}
// 3
// 6
// 9
// 12
```

다음은 조금 더 복잡한 예제로 CounterDataSource를 이용해 어떤 값을 0에 수렴하도록 만드는 클래스를 선언합니다.

```swift
class TowardsZeroSource: NSObject, CounterDataSource {
    func increment(forCount count: Int) -> Int {
        if count == 0 {
            return 0
        } else if count < 0 {
            return 1
        } else {
            return -1
        }
    }
}
```

counter.count는 -4에서 시작하지만 TowardsZeroSource의 increment에 의해 `counter.increment()`가 호출될때마다 0에 가까워지면서 결국 0에 수렴하게 됩니다.

```swift
counter.count = -4
counter.dataSource = TowardsZeroSource()
for _ in 1...5 {
    counter.increment()
    print(counter.count)
}
// -3
// -2
// -1
// 0
// 0
```

## 프로토콜 익스텐션 \(Protocol Extensions\)

익스텐션을 이용해 프로토콜을 확장할 수 있습니다. 아래 코드는 `random()`을 따르는 RandomNumberGenerator에 `randomBool()`을 따르도록 추가한 예입니다.

```swift
extension RandomNumberGenerator {
    func randomBool() -> Bool {
        return random() > 0.5
    }
}
```

아래 코드와 같이 generator에서 `generator.random()`과 `generator.randomBool()`를 둘다 이용할 수 있음을 확인할 수 있습니다.

```swift
let generator = LinearCongruentialGenerator()
print("Here's a random number: \(generator.random())")
// Prints "Here's a random number: 0.3746499199817101"
print("And here's a random Boolean: \(generator.randomBool())")
// Prints "And here's a random Boolean: true"
```

익스텐션을 이용해 구현을 추가할 수는 있어도 다른 프로토콜로 확장/상속할 수는 없습니다. 만약 그렇게 하고자 한다면 익스텐션이 아닌 프로토콜 자체에 구현해야 합니다.

### 기본 구현 제공 \(Providing Default Implementations\)

익스텐션을 기본 구현을 제공하는데 사용할 수 있습니다. 특정 프로토콜을 따르는 타입 중에서 그 프로토콜의 요구사항에 대해 자체적으로 구현한게 있으면 그것을 사용하고 아니면 기본 구현을 사용하게 됩니다. 즉, 프로토콜에서는 선언만 할 수 있는데 익스텐션을 이용해 기본 구현을 제공할 수 있습니다.

> NOTE  
> 프로토콜 익스텐션에 의해 구현된 기본 구현은 옵셔널 요구조건과 다릅니다. 둘다 꼭 구현하지 않아도 되는 것은 같지만 사용시 기본 구현은 옵셔널 체이닝을 사용하지 않아도 됩니다.

아래 예제는 PrettyTextRepresentable의 prettyTextualDescription 프로퍼티의 구현을 그냥 textualDescription을 반환하도록 구현한 예 입니다.

```swift
extension PrettyTextRepresentable  {
    var prettyTextualDescription: String {
        return textualDescription
    }
}
```

### 프로토콜 익스텐션에 제약 추가 \(Adding Constraints to Protocol Extensions\)

프로토콜 익스텐션이 특정 조건에서만 적용되도록 선언할 수 있습니다. 이 선언에는 where 절을 사용합니다. 다음은 Collection 엘리먼트가 Equatable인 경우에만 적용되는 `allEqual()`메소드를 구현한 예입니다.

```swift
extension Collection where Element: Equatable {
    func allEqual() -> Bool {
        for element in self {
            if element != self.first {
                return false
            }
        }
        return true
    }
}
```

Collection의 모든 엘리먼트가 같은지 비교하는 것이기 때문에 비교 기준을 first원소로 잡고 확인합니다.

위 예제를 실행하기 위해 아래와 같이 숫자 배열 2개를 선언합니다.

```swift
let equalNumbers = [100, 100, 100, 100, 100]
let differentNumbers = [100, 100, 200, 100, 200]
```

각 배열의 엘리먼트는 모두 Equatable프로토콜을 따르기 때문에 `allEqual()`를 호출할 수 있습니다.

```swift
print(equalNumbers.allEqual())
// Prints "true"
print(differentNumbers.allEqual())
// Prints "false"
```



