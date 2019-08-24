# 제어문 \(Control Flow\)

Swift에서는 `while loop,` `if guard`, `switch`, `for-in` 문 등 많은 제어문을 제공합니다.

## For-In 문 \(For-In Loops\)

`for-in`문는 배열, 숫자, 문자열을 순서대로 순회\(iterate\)하기 위해 사용합니다.

```swift
let names = ["Anna", "Alex", "Brian", "Jack"]
for name in names {
    print("Hello, \(name)!")
}
// Hello, Anna!
// Hello, Alex!
// Hello, Brian!
// Hello, Jack!
```

사전\(dictionary\)에서 반환된 키\(key\)-값\(value\) 쌍으로 구성된 튜플을 순회하며 제어할 수도 있습니다.

```swift
let numberOfLegs = ["spider": 8, "ant": 6, "cat": 4]
for (animalName, legCount) in numberOfLegs {
    print("\(animalName)s have \(legCount) legs")
}
// ants have 6 legs
// spiders have 8 legs
// cats have 4 legs
```

사전\(dictionary\)에 담긴 콘텐츠는 정렬이 되지 않은 상태입니다. 사전에 넣었던 순서대로 순회되지 않습니다. 아래와 같이 숫자 범위를 지정해 순회할 수 있습니다.

```swift
for index in 1...5 {
    print("\(index) times 5 is \(index * 5)")
}
// 1 times 5 is 5
// 2 times 5 is 10
// 3 times 5 is 15
// 4 times 5 is 20
// 5 times 5 is 25
```

`for-in` 문을 순서대로 제어할 필요가 없다면, 변수자리에 `_`키워드를 사용하면 성능을 높일 수 있습니다.

```swift
let base = 3
let power = 10
var answer = 1
for _ in 1...power {
    answer *= base
}
print("\(base) to the power of \(power) is \(answer)")
// Prints "3 to the power of 10 is 59049"
```

범위 연산자와 함께 사용할 수 있습니다.

```swift
let minutes = 60
for tickMark in 0..<minutes {
    // render the tick mark each minute (60 times)
}
```

`stride(from:to:by:)` 함수와 함께 사용할 수 있습니다. 다음은 구간을 5로 설정한 경우입니다.

```swift
let minuteInterval = 5
for tickMark in stride(from: 0, to: minutes, by: minuteInterval) {
    // render the tick mark every 5 minutes (0, 5, 10, 15 ... 45, 50, 55)
}
```

stride 구간을 3으로 설정한 경우

```swift
let hours = 12
let hourInterval = 3
for tickMark in stride(from: 3, through: hours, by: hourInterval) {
    // render the tick mark every 3 hours (3, 6, 9, 12)
}
```

## While 문 \(While Loops\)

Swift에서는 `while`과 `repeat-while` 두 가지 종류의 `while` 문을 지원합니다.

### While

조건\(condition\)이 거짓\(false\)일때까지 구문\(statements\)을 반복합니다.

```swift
while condition {
    statements
}
```

`while` 문의 \(예\)

```swift
var square = 0
var diceRoll = 0
while square < finalSquare {
    // roll the dice
    diceRoll += 1
    if diceRoll == 7 { diceRoll = 1 }
    // move by the rolled amount
    square += diceRoll
    if square < board.count {
        // if we're still on the board, move up or down for a snake or a ladder
        square += board[square]
    }
}
print("Game over!")
```

### Repeat-While 문

> `reapeat-while`문은 다른 언어의 `do-while`문과 유사한 `while`문입니다.

구문\(statements\)을 최소 한번 이상 실행하고 `while` 조건이 거짓일 때까지 반복합니다.

```swift
repeat {
    statements
} while condition
```

`repeat-while` 문의 \(예\)

```swift
repeat {
    // move up or down for a snake or ladder
    square += board[square]
    // roll the dice
    diceRoll += 1
    if diceRoll == 7 { diceRoll = 1 }
    // move by the rolled amount
    square += diceRoll
} while square < finalSquare
print("Game over!")
```

## 조건적 구문 \(Conditional Statements\)

Swift에서는 `if`와 `switch`문 두 가지의 조건 구문을 제공합니다.

### If 문

\(예1\) `If`만 사용

```swift
var temperatureInFahrenheit = 30
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
}
// Prints "It's very cold. Consider wearing a scarf."
```

\(예2\) `else`를 사용

```swift
temperatureInFahrenheit = 40
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
} else {
    print("It's not that cold. Wear a t-shirt.")
}
// Prints "It's not that cold. Wear a t-shirt."
```

\(예3\) `else`, `else-if`를 사용

```swift
temperatureInFahrenheit = 90
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
} else if temperatureInFahrenheit >= 86 {
    print("It's really warm. Don't forget to wear sunscreen.")
} else {
    print("It's not that cold. Wear a t-shirt.")
}
// Prints "It's really warm. Don't forget to wear sunscreen."
```

\(예4\) `else-if` 만 사용

```swift
temperatureInFahrenheit = 72
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
} else if temperatureInFahrenheit >= 86 {
    print("It's really warm. Don't forget to wear sunscreen.")
}
```

### Switch

Switch문의 기본 형태는 다음과 같습니다.

```swift
switch some value to consider {
case value 1:
    respond to value 1
case value 2,
     value 3:
    respond to value 2 or 3
default:
    otherwise, do something else
}
```

문자를 비교해 처리하는 경우 아래와 같이 사용할 수 있습니다.

```swift
let someCharacter: Character = "z"
switch someCharacter {
case "a":
    print("The first letter of the alphabet")
case "z":
    print("The last letter of the alphabet")
default:
    print("Some other character")
}
// Prints "The last letter of the alphabet"
```

### 암시적인 진행을 사용하지 않음 \(No Implicit Fallthrough\)

C와 Objective-C의 `switch` 구문과는 달리 Swift의 `switch`구문은 암시적인 진행을 하지 않습니다. C나 Objective-C에서는 `switch` 구문이 기본적으로 모든 `case`를 순회하여 `default`를 만날 때까지 진행됩니다. 그래서 그것을 진행하지 않기 위해 `break`라는 문구를 명시적으로 적어야 했습니다. Swift에서는 `break`를 적지 않아도 특정 `case`가 완료되면 자동으로 `switch`구문을 빠져 나오게 됩니다. 이런 사용법으로 인해 실수로 `break`를 적지않아 의도하지 않은 `case`문이 실행되는 것을 방지해 줍니다.

> _주의_  
> `break`가 Swift에서 필수적이지는 않지만 `case`안에 특정 지점에서 멈추도록 하기 위해 `break`를 사용할 수 있습니다. 자세한 내용은 `Break`문을 참조해주세요.

`case` 안 에 최소 하나의 실행 구문이 반드시 있어야 합니다.

```swift
let anotherCharacter: Character = "a"
switch anotherCharacter {
case "a": // Invalid, case문에 body가 없으므로 에러가 발생합니다.
case "A":
    print("The letter A")
default:
    print("Not the letter A")
}
// 컴파일 에러 발생!
```

case 안에 콤마\(,\)로 구분해서 복수의 case 조건을 혼합\(compound\)해 사용할 수 있습니다.

```swift
let anotherCharacter: Character = "a"
switch anotherCharacter {
case "a", "A":
    print("The letter A")
default:
    print("Not the letter A")
}
// Prints "The letter A"
```

가독성 때문에 혼합해 사용하는 경우를 여러 코드라인에 나눠서 적을 수 있습니다. 더 많은 정보는 혼합 케이스 \(Compound Cases\)에서 확인할 수 있습니다.

> _주의_  
> 명시적으로 switch-case 문의 특정 지점의 끝까지 실행하고 싶다면 `fallthrough` 키워드를 사용할 수 있습니다. 자세한 정보는 Fallthrough에서 확인할 수 있습니다.

### 인터벌 매칭 \(Interval Matching\)

숫자의 특정 범위를 조건으로 사용할 수 있습니다.

```swift
let approximateCount = 62
let countedThings = "moons orbiting Saturn"
let naturalCount: String
switch approximateCount {
case 0:
    naturalCount = "no"
case 1..<5:
    naturalCount = "a few"
case 5..<12:
    naturalCount = "several"
case 12..<100:
    naturalCount = "dozens of"
case 100..<1000:
    naturalCount = "hundreds of"
default:
    naturalCount = "many"
}
print("There are \(naturalCount) \(countedThings).")
// Prints "There are dozens of moons orbiting Saturn."
```

### 튜플 \(Tuple\)

튜플을 조건으로 사용할 수 있습니다.

```swift
let somePoint = (1, 1)
switch somePoint {
case (0, 0):
    print("\(somePoint) is at the origin")
case (_, 0):
    print("\(somePoint) is on the x-axis")
case (0, _):
    print("\(somePoint) is on the y-axis")
case (-2...2, -2...2):
    print("\(somePoint) is inside the box")
default:
    print("\(somePoint) is outside of the box")
}
// Prints "(1, 1) is inside the box"
```

### 값 바인딩 \(Value Bindings\)

특정 x, y 값을 각각 다른 case에 정의하고 그 정의된 상수를 또 다른 case에서 사용할 수 있습니다. 이런 기법을 값-바인딩\(value bindings\)라 부릅니다.

```swift
let anotherPoint = (2, 0)
switch anotherPoint {
case (let x, 0):
    print("on the x-axis with an x value of \(x)")
case (0, let y):
    print("on the y-axis with a y value of \(y)")
case let (x, y):
    print("somewhere else at (\(x), \(y))")
}
// Prints "on the x-axis with an x value of 2"
```

### Where 문

`case`에 `where` 조건을 사용할 수 있습니다.

```swift
let yetAnotherPoint = (1, -1)
switch yetAnotherPoint {
case let (x, y) where x == y:
    print("(\(x), \(y)) is on the line x == y")
case let (x, y) where x == -y:
    print("(\(x), \(y)) is on the line x == -y")
case let (x, y):
    print("(\(x), \(y)) is just some arbitrary point")
}
// Prints "(1, -1) is on the line x == -y"
```

### 혼합 케이스 \(Compound Cases\)

`case`에 콤마\(,\)로 구분해 여러 조건을 혼합해 사용할 수 있습니다.

```swift
let someCharacter: Character = "e"
switch someCharacter {
case "a", "e", "i", "o", "u":
    print("\(someCharacter) is a vowel")
case "b", "c", "d", "f", "g", "h", "j", "k", "l", "m",
     "n", "p", "q", "r", "s", "t", "v", "w", "x", "y", "z":
    print("\(someCharacter) is a consonant")
default:
    print("\(someCharacter) is not a vowel or a consonant")
}
// Prints "e is a vowel"
```

혼합 케이스에서도 값-바인딩을 사용할 수 있습니다.

```swift
let stillAnotherPoint = (9, 0)
switch stillAnotherPoint {
case (let distance, 0), (0, let distance):
    print("On an axis, \(distance) from the origin")
default:
    print("Not on an axis")
}
// Prints "On an axis, 9 from the origin"
```

## 제어 전송 구문 \(Control Transfer Statements\)

제어 전송 구문은 코드의 진행을 계속 할지 말지를 결정하거나, 실행되는 코드의 흐름을 바꾸기 위해 사용합니다. Swift에서는 다음 다섯 가지의 제어 전송 구문을 제공합니다.

* continue
* break
* fallthrough
* return
* throw

### continue 문

`continue`문은 현재 `loop`를 중지하고 다음 `loop`를 수행하도록 합니다.

```swift
let puzzleInput = "great minds think alike"
var puzzleOutput = ""
let charactersToRemove: [Character] = ["a", "e", "i", "o", "u", " "]
for character in puzzleInput {
    if charactersToRemove.contains(character) {
        continue
    } else {
        puzzleOutput.append(character)
    }
}
print(puzzleOutput)
// Prints "grtmndsthnklk"
```

### Break 문

`break`문은 전체 제어문의 실행을 즉각 중지 시킵니다. `break`문은 `loop`나 `switch`문에서 사용할 수 있습니다.

```swift
let numberSymbol: Character = "三"  // 중국어로 3을 의미하는 문자입니다.
var possibleIntegerValue: Int?
switch numberSymbol {
case "1", "١", "一", "๑":
    possibleIntegerValue = 1
case "2", "٢", "二", "๒":
    possibleIntegerValue = 2
case "3", "٣", "三", "๓":
    possibleIntegerValue = 3
case "4", "٤", "四", "๔":
    possibleIntegerValue = 4
default:
    break
}
if let integerValue = possibleIntegerValue {
    print("The integer value of \(numberSymbol) is \(integerValue).")
} else {
    print("An integer value could not be found for \(numberSymbol).")
}
```

### fallthrough 문

`fallthrough` 키워드는 이후의 `case`에 대해서도 실행하게 합니다. 앞에서 언급했던 것 처럼 Swift에서는 한번 특정 `case`를 타면 바로 그 switch 문은 종료됩니다. 마치 `case` 안에 `break`를 자동으로 넣은 것과 같은 기능을 하는 것이죠. 하지만 이 `fallthrough` 를 사용하면 이 자동으로 `break`가 사용되는 것을 막는 효과를 가져옵니다.

```swift
let integerToDescribe = 5
var description = "The number \(integerToDescribe) is"
switch integerToDescribe {
case 2, 3, 5, 7, 11, 13, 17, 19:
    description += " a prime number, and also"
    fallthrough
default:
    description += " an integer."
}
print(description)
// Prints "The number 5 is a prime number, and also an integer."
```

> _주의_   
> fallthrough 는 case 조건을 확인하지 않고 그냥 다음 case를 실행하게 만듭니다.

### 레이블 구문 \(Labeled Statements\)

아래와 같은 형태로 `label` 이름과 `while` 조건을 넣어 특정 구문을 실행하는 구문으로 사용할 수 있습니다.

```swift
label name: while condition {
    statements
}
```

switch 문과 함께 사용할 수 있습니다.

```swift
gameLoop: while square != finalSquare {
    diceRoll += 1
    if diceRoll == 7 { diceRoll = 1 }
    switch square + diceRoll {
    case finalSquare:
        // diceRoll will move us to the final square, so the game is over
        break gameLoop
    case let newSquare where newSquare > finalSquare:
        // diceRoll will move us beyond the final square, so roll again
        continue gameLoop
    default:
        // this is a valid move, so find out its effect
        square += diceRoll
        square += board[square]
    }
}
print("Game over!")
```

### 이른 탈출 \(Early Exit\)

`guard`문을 이용해 특정 조건을 만족하지 않으면 이 후 코드를 실행하지 않도록 방어코드를 작성할 수 있습니다.

```swift
func greet(person: [String: String]) {
    guard let name = person["name"] else {
        return
    }

    print("Hello \(name)!")

    guard let location = person["location"] else {
        print("I hope the weather is nice near you.")
        return
    }

    print("I hope the weather is nice in \(location).")
}

greet(person: ["name": "John"])
// Prints "Hello John!"
// Prints "I hope the weather is nice near you."
greet(person: ["name": "Jane", "location": "Cupertino"])
// Prints "Hello Jane!"
// Prints "I hope the weather is nice in Cupertino."
```

## 이용가능한 API 버전 확인 \(Checking API Availability\)

Swift에서는 기본으로 특정 플랫폼 \(iOS, macOS, tvOS, watchOS\)과 특정 버전을 확인하는 구문을 제공해 줍니다. 이 구문을 활용해 특정 플랫폼과 버전을 사용하는 기기에 대한 처리를 따로 할 수 있습니다. 구문의 기본 형태는 다음과 같습니다.

```swift
if #available(platform name version, ..., *) {
    statements to execute if the APIs are available
} else {
    fallback statements to execute if the APIs are unavailable
}
```

실제 사용 \(예\)

```swift
if #available(iOS 10, macOS 10.12, *) {
    // Use iOS 10 APIs on iOS, and use macOS 10.12 APIs on macOS
} else {
    // Fall back to earlier iOS and macOS APIs
}
```

