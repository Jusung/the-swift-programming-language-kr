# 함수 \(Functions\)

## **정의와 호출 \(Defining and Calling Functions\)**

함수를 선언할 때는 가장 앞에 `func` 키워드를 붙이고 `(person: String)` 파라미터와 형 그리고 `-> String` 형태로 반환형을 정의합니다.

```swift
func greet(person: String) -> String {
 let greeting = "Hello, " + person + "!"
 return greeting
}
```

정의한 함수에 인자 값을 넣어 호출한 \(예\)

```swift
print(greet(person: "Anna"))
// Prints "Hello, Anna!"
print(greet(person: "Brian"))
// Prints "Hello, Brian!"
```

위 함수에서 메시지를 결합하는 부분과 반환하는 부분을 합쳐서 더 짧게 만들 수 있습니다.

```swift
func greetAgain(person: String) -> String {
    return "Hello again, " + person + "!"
}
print(greetAgain(person: "Anna"))
// Prints "Hello again, Anna!"
```

## 함수 파라미터와 반환 값 \(Function Parameters and Return Values\)

### 파라미터가 없는 함수 \(Functions Without Parameters\)

```swift
func sayHelloWorld() -> String {
    return "hello, world"
}
print(sayHelloWorld())
// Prints "hello, world"
```

### 복수의 파라미터를 사용하는 함수 \(Functions With Multiple Parameters\)

```swift
func greet(person: String, alreadyGreeted: Bool) -> String {
    if alreadyGreeted {
        return greetAgain(person: person)
    } else {
        return greet(person: person)
    }
}
print(greet(person: "Tim", alreadyGreeted: true))
// Prints "Hello again, Tim!"
```

### 반환 값이 없는 함수 \(Functions Without Return Values\)

```swift
func greet(person: String) {
    print("Hello, \(person)!")
}
greet(person: "Dave")
// Prints "Hello, Dave!"
```

> _주의_  
> 엄밀히 말하면 위 함수는 반환 값을 선언하지 않았지만 반환 값이 있습니다. 반환 값이 정의 되지 않은 함수는 Void라는 특별한 형을 반환합니다. Void는 간단히 \(\)를 사용한 빈 튜플입니다.

함수의 반환 값은 아래와 같이 호출 될때 무시될 수 있습니다.

```swift
func printAndCount(string: String) -> Int {
    print(string)
    return string.count
}
func printWithoutCounting(string: String) {
    let _ = printAndCount(string: string)
}
printAndCount(string: "hello, world")
// prints "hello, world" and returns a value of 12
printWithoutCounting(string: "hello, world")
// prints "hello, world" but does not return a value
```

### 복수의 값을 반환하는 함수 \(Functions with Multiple Return Values\)

튜플을 함수의 반환 값으로 사용할 수 있습니다.

```swift
func minMax(array: [Int]) -> (min: Int, max: Int) {
    var currentMin = array[0]
    var currentMax = array[0]
    for value in array[1..<array.count] {
        if value < currentMin {
            currentMin = value
        } else if value > currentMax {
            currentMax = value
        }
    }
    return (currentMin, currentMax)
}
```

반환 값의 인자를 반환 값을 접근하는 접근자로 사용할 수 있습니다.

```swift
let bounds = minMax(array: [8, -6, 2, 109, 3, 71])
print("min is \(bounds.min) and max is \(bounds.max)")
// Prints "min is -6 and max is 109"
```

### 옵셔널 튜플 반환형 \(Optional Tuple Return Types\)

위의 반환 값과 달리 반환 값에 `?` 물음표가 붙었습니다. `(min: Int, max: Int)?`

```swift
func minMax(array: [Int]) -> (min: Int, max: Int)? {
    if array.isEmpty { return nil }
    var currentMin = array[0]
    var currentMax = array[0]
    for value in array[1..<array.count] {
        if value < currentMin {
            currentMin = value
        } else if value > currentMax {
            currentMax = value
        }
    }
    return (currentMin, currentMax)
}
```

실제 반환 값에 접근하기 위해서는 `if let`과 같은 옵셔널 체인을 사용하거나 강제 unwrapping을 해야 합니다. 아래는 옵셔널 체인을 사용한 \(예\)입니다.

```swift
if let bounds = minMax(array: [8, -6, 2, 109, 3, 71]) {
    print("min is \(bounds.min) and max is \(bounds.max)")
}
// Prints "min is -6 and max is 109"
```

### 함수 인자 라벨과 파라미터 이름 \(Function Argument Labels and Parameter Names\)

함수 호출시 적절한 파라미터 이름을 지정해 함수 내부와 함수 호출시 사용할 수 있습니다.

```swift
func someFunction(firstParameterName: Int, secondParameterName: Int) {
    // 함수 내부에서 firstParameterName와 secondParameterName의 인자를 사용합니다.
}
someFunction(firstParameterName: 1, secondParameterName: 2)
```

### 인자 라벨 지정 \(Specifying Argument Labels\)

파라미터 앞에 인자 라벨을 지정해 실제 함수 내부에서 해당 인자를 식별하기 위한 이름과 함수 호출시 사용하는 이름을 다르게 해서 사용할 수 있습니다.

```swift
func someFunction(argumentLabel parameterName: Int) {
    // 함수 안애서 parameterName로 argumentLabel의 인자값을 참조할 수 있습니다.
}
```

인자 라벨을 지정해서 함수 내부에서는 `hometown`으로 값을 제어하고 함수 호출시에는 인자 값으로 `from`을 사용한 \(예\) 입니다.

```swift
func greet(person: String, from hometown: String) -> String {
    return "Hello \(person)!  Glad you could visit from \(hometown)."
}
print(greet(person: "Bill", from: "Cupertino"))
// Prints "Hello Bill!  Glad you could visit from Cupertino."
```

### 인자 생략 \(Omitting Argument Labels\)

파라미터 앞에 `_`를 붙여 함수 호출시 인자값을 생략할 수 있습니다.

```swift
func someFunction(_ firstParameterName: Int, secondParameterName: Int) {
    // 함수 안에서 firstParameterName, secondParameterName
    // 인자로 입력받은 첫번째, 두번째 값을 참조합니다.
}
someFunction(1, secondParameterName: 2)
```

### 기본 파라미터 값 \(Default Parameter Values\)

함수의 파라미터 값에 기본 값\(`: Int = 12`\)을 설정할 수 있습니다. 기본 값이 설정 되어 있는 파라미터는 함수 호출시 생략할 수 있습니다. 기본 값을 사용하지 않는 파라미터를 앞에 위치 시켜야 함수를 의미있게 사용하기 쉽습니다.

```swift
func someFunction(parameterWithoutDefault: Int, parameterWithDefault: Int = 12) {
    // 함수 호출시 두번째 인자를 생략하면 함수안에서
    // parameterWithDefault값은 12가 기본 값으로 사용됩니다.
}
someFunction(parameterWithoutDefault: 3, parameterWithDefault: 6) // parameterWithDefault는 6
someFunction(parameterWithoutDefault: 4) // parameterWithDefault는 12
```

### 집합 파라미터 \(Variadic Parameters\)

인자 값으로 특정 형\(type\)의 집합 값을 사용할 수 있습니다.

```swift
func arithmeticMean(_ numbers: Double...) -> Double {
    var total: Double = 0
    for number in numbers {
        total += number
    }
    return total / Double(numbers.count)
}
arithmeticMean(1, 2, 3, 4, 5)
// returns 3.0, which is the arithmetic mean of these five numbers
arithmeticMean(3, 8.25, 18.75)
// returns 10.0, which is the arithmetic mean of these three numbers
```

### 인-아웃 파라미터 \(In-Out Parameters\)

인자 값을 직접 변경하는 파라미터 입니다. 선언을 위해 파라미터 앞에 `inout` 이라는 키워드를 사용합니다. 아래는 인자 두 값을 변경하는 함수입니다.

```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

아래는 실제로 사용하는 \(예\) 입니다. 함수의 인자에 변수를 넣을때 `&` 키워드를 넣었습니다. C언어를 아시는 분은 `inout`파라미터는 포인터를 넣는다고 생각하시면 이해하기 편하실 것입니다.

```swift
var someInt = 3
var anotherInt = 107
swapTwoInts(&someInt, &anotherInt)
print("someInt is now \(someInt), and anotherInt is now \(anotherInt)")
// Prints "someInt is now 107, and anotherInt is now 3"
```

두 변수의 실제 값이 변경 되었습니다.

> _주의_  
> 인-아웃 파라미터는 기본 값을 갖을 수 없고, 집합 파라미터는 `inout`으로 선언될 수 없습니다. 인-아웃 파라미터를 사용하는 것은 함수의 반환 값을 사용하지 않고 함수 scope 밖에 영향을 줄 수 있는 또 하나의 방법입니다.

## 함수 형 \(Function Types\)

함수의 형은 파라미터 형과\(parameter types\) 반환 형\(return type\)으로 구성 돼 있습니다. 아래 두 함수는 `Int`값 두 개를 입력받고 `Int`를 반환하는 함수입니다.

```swift
func addTwoInts(_ a: Int, _ b: Int) -> Int {
    return a + b
}
func multiplyTwoInts(_ a: Int, _ b: Int) -> Int {
    return a * b
}
```

아래는 입력 받는 파라미터와 반환 값이 없는 함수입니다.

```swift
func printHelloWorld() {
    print("hello, world")
}
```

### 함수 형의 사용 \(Using Function Types\)

아래와 같이 함수를 변수처럼 정의해서 사용할 수 있습니다.

```swift
var mathFunction: (Int, Int) -> Int = addTwoInts
```

변수 `mathFunction`는 `addTwoInts` 함수의 인자 값과 반환 값이 같으므로 이 함수가 변수로 할당 될 수 있습니다. 아래는 이렇게 변수에 함수를 할당해 사용한 \(예\) 입니다.

```swift
print("Result: \(mathFunction(2, 3))")
// Prints "Result: 5"
```

`multiplyTwoInts` 함수도 mathFunction과 함수 형이 같으므로 할당해 사용할 수 있습니다.

```swift
mathFunction = multiplyTwoInts
print("Result: \(mathFunction(2, 3))")
// Prints "Result: 6"
```

변수나 상수에 함수를 할당할 때 직접 함수 형을 선언하지 않아도 Swift가 형을 추론해\(Type Inferred\) 자동으로 함수를 할당할 수 있습니다.

```swift
let anotherMathFunction = addTwoInts
// anotherMathFunction is inferred to be of type (Int, Int) -> Int
```

### 파라미터 형으로써의 함수 형 \(Function Types as Parameter Types\)

파라미터에 함수 형을 사용할 수 있습니다.

```swift
func printMathResult(_ mathFunction: (Int, Int) -> Int, _ a: Int, _ b: Int) {
    print("Result: \(mathFunction(a, b))")
}
printMathResult(addTwoInts, 3, 5)
// Prints "Result: 8"
```

### 반환 형으로써의 함수 형 \(Function Types as Return Types\)

함수를 반환하는 함수를 만들수도 있습니다.

```swift
func stepForward(_ input: Int) -> Int {
    return input + 1
}
func stepBackward(_ input: Int) -> Int {
    return input - 1
}
```

입력한 step에 하나를 더하거나 빼는 함수를 선언했습니다. 이 함수를 리턴값으로 사용할 수 있습니다. 아래 코드는 `backward`함수가 `true`냐 `false`냐에 따라 위에서 선언한 적절한 함수를 반환하는 함수입니다.

```swift
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    return backward ? stepBackward : stepForward
}
```

위 함수를 사용한 \(예\)는 아래와 같습니다.

```swift
var currentValue = 3
let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
// moveNearerToZero는 이제 stepBackward() 함수를 가르키고 있습니다.
```

`moveNearerToZero`를 호출할 때마다 `stepBackward()` 함수가 호출돼 입력 값이 1씩 줄어들어 결국 0이 됩니다.

```swift
print("Counting to zero:")
// Counting to zero:
while currentValue != 0 {
    print("\(currentValue)... ")
    currentValue = moveNearerToZero(currentValue)
}
print("zero!")
// 3...
// 2...
// 1...
// zero!
```

## 중첩 함수 \(Nested Functions\)

지금까지 함수는 전역적으로 동작하도록 선언했습니다. 함수 중에는 다른 함수 안의 `body`에서 동작하는 함수가 있는데 이 함수를 중첩 함수\(Nested Function\) 이라 합니다. 중첩함수는 함수 밖에서는 감춰져 있고 함수의 `body`내에서 접근 가능합니다. 위의 `chooseStepFunction`을 중첩 함수를 이용해 아래처럼 다시 작성할 수 있습니다.

```swift
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    func stepForward(input: Int) -> Int { return input + 1 }
    func stepBackward(input: Int) -> Int { return input - 1 }
    return backward ? stepBackward : stepForward
}
var currentValue = -4
let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
// moveNearerToZero는 이제 중첩 돼 있는 stepForward() 함수를 가르킵니다.
while currentValue != 0 {
    print("\(currentValue)... ")
    currentValue = moveNearerToZero(currentValue)
}
print("zero!")
// -4...
// -3...
// -2...
// -1...
// zero!
```

