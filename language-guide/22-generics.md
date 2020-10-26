# 제네릭 \(Generics\)

제네릭은 더 유연하고 재사용 가능한 함수와 타입의 코드를 작성하는 것을 가능하게 해 줍니다.

## 제네릭이 풀려고 하는 문제 \(The Problem That Generics Solve\)

아래와 같이 두 Int 값을 바꾸는 함수가 있습니다. 인자로 넣는 두개의 파라미터는 inout 파라미터입니다. 그래서 두 값의 원본을 변경하게 됩니다.

```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

선언한 함수 `swapTwoInts`를 선언하면 입력한 두 Int값이 변경된 것을 확인할 수 있습니다.

```swift
var someInt = 3
var anotherInt = 107
swapTwoInts(&someInt, &anotherInt)
print("someInt is now \(someInt), and anotherInt is now \(anotherInt)")
// Prints "someInt is now 107, and anotherInt is now 3"
```

만약 Int형이 아니라 String 값을 변경하려면 어떻게 해야할까요? 다음과 같이 swapTwoStrings라는 함수를 새로 선언해야 합니다. 값이 Double이면 이것도 마찬가지로 swapTwoDoubles라는 새 함수를 선언해야 합니다.

```swift
func swapTwoStrings(_ a: inout String, _ b: inout String) {
    let temporaryA = a
    a = b
    b = temporaryA
}

func swapTwoDoubles(_ a: inout Double, _ b: inout Double) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

위 예를 보면 아마 알아차리셨을 것입니다. 각 함수는 인자값의 타입만 다르고 함수의 내용을 동일하다는 사실을. 제네릭을 사용하면 이렇게 인자값의 타입만 다르고 수행하는 기능이 동일한 것을 하나의 함수로 만들 수 있습니다.

> NOTE  
> 위 세 함수에서 모두 a와 b의 타입이 동일해야 합니다. 만약 a와 b가 동일하지 않다면 두 값을 바꿔치기 할 수 없습니다. Swift는 타입-세이프 언어여서 `String`과 `Double`를 바꾸려고 하면 컴파일 에러가 발생합니다.

## 제네릭 함수 \(Generic Functions\)

위 세 함수를 하나의 제네릭 함수로 만들어 보겠습니다. 함수명 뒤에 타입 이름 플레이스 홀더인 를 선언하고 a, b 파라미터를 T로 선언합니다. 플레이스 홀더인 T는 타입이 어떤 타입이어야 하는지 명시하지 않습니다. 다만 두 인자의 타입 같다는 것을 알려줍니다.

```swift
func swapTwoValues<T>(_ a: inout T, _ b: inout T) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

Swift는 실제 실행하는 타입 T가 어떤 타입인지 보지 않습니다. swapTwoValues 함수가 실행되면 T에 해당하는 값을 함수에 넘깁니다.

```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int)
func swapTwoValues<T>(_ a: inout T, _ b: inout T)
```

제네릭으로 선언한 함수를 실행하면 기대했던 대로 동작하는 것을 확인할 수 있습니다.

```swift
var someInt = 3
var anotherInt = 107
swapTwoValues(&someInt, &anotherInt)
// someInt is now 107, and anotherInt is now 3

var someString = "hello"
var anotherString = "world"
swapTwoValues(&someString, &anotherString)
// someString is now "world", and anotherString is now "hello"
```

> NOTE  
> 제네릭의 예로 생성한 `swapTwoValues`함수는 Swift에서 `swap` 기본적으로 제공합니다.

## 타입 파라미터 \(Type Parameters\)

위에서 사용한 플레이스 홀더 T는 타입 파라미터의 예입니다. 타입 파라미터는 플레이스 홀더 타입의 이름을 명시하고 함수명 바로 뒤에 적어줍니다. 그리고 와 같이 꺽쇄로 묶어 줍니다. 타입 파라미터를 한번 선언하면 이 것을 함수의 타입으로 사용할 수 있습니다. 복수의 타입 파라미터를 사용할때는 와 같이 콤마로 구분해 줍니다.

## 파라미터 이름짓기 \(Naming Type Parameters\)

Dictionary의 Key, Value와 같이 엘리먼트 간의 서로 상관관계가 있는 경우 의미가 있는 이름을 파라미터 이름으로 붙이고 그렇지 않은 경우는 T, U, V와 같은 단일 문자로 파라미터 이름을 짓습니다.

> 항상 파라미터 이름은 T나 MyTypeParameter와 같이 대문자 카멜 케이스로 이름 짓습니다. 대문자로 된 이름은 값\(value\)이 아니라 타입\(type\)을 의미합니다.

## 제네릭 타입 \(Generic Types\)

제네릭 함수에 추가로 Swift에서는 제네릭 타입을 정의할 수 있습니다. 이후 섹션에서는 Stack이라는 제네릭 콜렉션 타입을 어떻게 구현하는지 보여줄 예정입니다.

> NOTE  
> 스택은 `UINavigationController`에서 사용하는 자료구조입니다. 네비게이션 계층에 뷰 컨트롤러를 `pushViewController`와 `popViewController`를 이용해 추가하거나 뺄 수 있습니다.

다음 그림은 스택이 어떻게 동작하는지 설명해 줍니다.

![](../.gitbook/assets/stackpushpop_2x.png)

Int 값을 스택에 넣고\(push\) 빼는\(pop\) 함수 `IntStack`의 구현은 다음과 같습니다.

```swift
struct IntStack {
    var items = [Int]()
    mutating func push(_ item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
}
```

위 IntStack을 제네릭 형태로 구현하면 다음과 같이 구현할 수 있습니다.

```swift
struct Stack<Element> {
    var items = [Element]()
    mutating func push(_ item: Element) {
        items.append(item)
    }
    mutating func pop() -> Element {
        return items.removeLast()
    }
}
```

제네릭으로 구현된 스택을 실행해 보겠습니다.

```swift
var stackOfStrings = Stack<String>()
stackOfStrings.push("uno")
stackOfStrings.push("dos")
stackOfStrings.push("tres")
stackOfStrings.push("cuatro")
// the stack now contains 4 strings
```

아래 그림과 같이 스택에 4개의 `String`값이 들어가게 됩니다.

![](../.gitbook/assets/stackpushedfourstrings_2x.png)

스택에서 값을 빼기 위해 `pop()`함수를 실행합니다.

```swift
let fromTheTop = stackOfStrings.pop()
// fromTheTop is equal to "cuatro", and the stack now contains 3 strings
```

스택의 최상단에 있는 값을 결고로 뱉습니다.

![](../.gitbook/assets/stackpoppedonestring_2x.png)

## 제네릭 타입의 확장 \(Extending a Generic Type\)

익스텐션을 이용해 제네릭 타입을 확장할 수 있습니다. 이때 원래 선언한 파라미터 이름을 사용합니다. 여기서는 `Element`라는 파라미터를 사용합니다.

```swift
extension Stack {
    var topItem: Element? {
        return items.isEmpty ? nil : items[items.count - 1]
    }
}
```

익스텐션으로 추가한 `topItem` 프로퍼티에 접근합니다.

```swift
if let topItem = stackOfStrings.topItem {
    print("The top item on the stack is \(topItem).")
}
// Prints "The top item on the stack is tres."
```

실행결과 스택의 최상단의 값을 확인할 수 있습니다.

## 타입 제한 \(Type Constraints\)

Swift의 Dictionary 타입은 key값을 사용합니다. 이때 key는 유일한 값이어야 하기 때문에 `hashable`이라는 프로토콜을 반드지 따라야 합니다. 그렇지 않으면 key로 value에 접근했을 때 적절한 value를 얻지 못할 수 있습니다. 이와 같이 특정 타입이 반드시 어떤 프로토콜을 따라야 하는 경우가 있습니다. 네릭에서도 이런 경우가 필요할 수 있습니다. 제네릭에서는 특정 클래스를 상속하거나 특정 프로토콜을 따르거나 합성하도록 명시할 수 있습니다.

### 타입 제한 문법 \(Type Constraint Syntax\)

제네릭 함수를 선언할 때, 파라미터 뒤에 상속 받아야 하는 클래스를 선언하거나, 반드시 따라야 하는 프로토콜을 명시할 수 있습니다.

```swift
func someFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U) {
    // function body goes here
}
```

### 타입 제한의 실 사용 \(Type Constraints in Action\)

다음과 같이 한 배열에서 특정 문자를 검색하는 `findIndex` 함수를 선언합니다.

```swift
func findIndex(ofString valueToFind: String, in array: [String]) -> Int? {
    for (index, value) in array.enumerated() {
        if value == valueToFind {
            return index
        }
    }
    return nil
}
```

이 함수를 실행하면 `strings`배열에서 찾기 원하는 문자열 `llama`의 인덱스 위치를 찾는 것이 잘 동작함을 확인할 수 있습니다.

```swift
let strings = ["cat", "dog", "llama", "parakeet", "terrapin"]
if let foundIndex = findIndex(ofString: "llama", in: strings) {
    print("The index of llama is \(foundIndex)")
}
// Prints "The index of llama is 2"
```

위 함수를 제네릭으로 구현하면 다음과 같습니다.

```swift
func findIndex<T>(of valueToFind: T, in array:[T]) -> Int? {
    for (index, value) in array.enumerated() {
        if value == valueToFind {
            return index
        }
    }
    return nil
}
```

위 코드는 에러가 발생합니다. 이유는 `value == valueToFind`의 코드에서 두 값을 비교하게 되는데 두 값을 비교하는 경우에는 즉, `==` 등호 메소드를 사용하기 위해서는 두 값 혹은 객체가 반드시 `Equatable` 프로토콜을 따라야 하기 때문입니다. 이 문제를 해결하기 위해 T는 `Equatable`프로토콜을 따른다고 표시합니다. 타입 플레이스 홀더에 `<T: Equatable>`같이 적어줍니다. 이것으로 T가 `Equatable`프로토콜을 따른 다는 것을 알려줍니다.

```swift
func findIndex<T: Equatable>(of valueToFind: T, in array:[T]) -> Int? {
    for (index, value) in array.enumerated() {
        if value == valueToFind {
            return index
        }
    }
    return nil
}
```

그래서 위 함수는 에러없이 빌드되고 아래와 같이 적절히 사용할 수 있습니다.

```swift
let doubleIndex = findIndex(of: 9.3, in: [3.14159, 0.1, 0.25])
// doubleIndex is an optional Int with no value, because 9.3 isn't in the array
let stringIndex = findIndex(of: "Andrea", in: ["Mike", "Malcolm", "Andrea"])
// stringIndex is an optional Int containing a value of 2
```

## 연관 타입 \(Associated Types\)

연관타입은 프로토콜의 일부분으로 타입에 플레이스홀더 이름을 부여합니다. 다시말해 특정 타입을 동적으로 지정해 사용할 수 있습니다.

### 연관 타입의 실 사용 \(Associated Types in Action\)

아래와 같이 Item에 `associatedtype`를 사용할 수 있습니다. 이렇게 지정하면 Item은 어떤 타입도 될 수 있습니다.

```swift
protocol Container {
    associatedtype Item
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}
```

아래 코드에서는 Item을 Int형으로 선언해 사용합니다.

```swift
struct IntStack: Container {
    // original IntStack implementation
    var items = [Int]()
    mutating func push(_ item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
    // conformance to the Container protocol
    typealias Item = Int
    mutating func append(_ item: Int) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Int {
        return items[i]
    }
}
```

아래 예제에서는 Item을 Element형으로 지정해 사용합니다.

```swift
struct Stack<Element>: Container {
    // original Stack<Element> implementation
    var items = [Element]()
    mutating func push(_ item: Element) {
        items.append(item)
    }
    mutating func pop() -> Element {
        return items.removeLast()
    }
    // conformance to the Container protocol
    mutating func append(_ item: Element) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Element {
        return items[i]
    }
}
```

### 존재 하는 타입에 연관 타입을 확장 \(Extending an Existing Type to Specify an Associated Type\)

아래와 같이 기존의 타입 Array이에 특정 연관 타입을 추가할 수 있습니다.

```swift
extension Array: Container {}
```

### Adding Constraints to an Associated Type

이것이 가능한 이유는 Array 타입은 아래 Container에 선언된 `append`, `count`, `subscript`가 모두 정의돼 있기 때문입니다.

```swift
protocol Container {
    associatedtype Item: Equatable
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}
```

### 프로토콜에 연관 타입의 제한 사용하기 \(Using a Protocol in Its Associated Type’s Constraints\)

연관 타입을 적용할 수 있는 타입에 조건을 걸어 제한을 둘 수 있습니다. 조건을 붙일때는 where구문을 사용합니다. 이 조건에는 특정 탑입인지의 여부, 특정 프로토콜을 따르는지 여부 등이 사용될 수 있습니다. 다음은 Suffix가 SuffixableContainer프로토콜을 따르고 Item타입이 반드시 Container의 Item타입이어야 한다는 조건을 추가한 것입니다.

```swift
protocol SuffixableContainer: Container {
    associatedtype Suffix: SuffixableContainer where Suffix.Item == Item
    func suffix(_ size: Int) -> Suffix
}
```

이렇게 선언한 Suffix를 따르는 코드를 실행하는 예는 다음과 같습니다.

```swift
extension Stack: SuffixableContainer {
    func suffix(_ size: Int) -> Stack {
        var result = Stack()
        for index in (count-size)..<count {
            result.append(self[index])
        }
        return result
    }
    // Inferred that Suffix is Stack.
}
var stackOfInts = Stack<Int>()
stackOfInts.append(10)
stackOfInts.append(20)
stackOfInts.append(30)
let suffix = stackOfInts.suffix(2)
// suffix contains 20 and 30
```

위 예제에서는 Stack의 연관 타입인 Suffix 또한 Stack입니다. 그래서 Stack의 suffix의 실행으로 또 다른 Stack을 반환하게 됩니다. 아래와 같이 IntStack에 Stack를 사용해 SuffixableContainer를 따르는 익스텐션을 선언할 수도 있습니다.

```swift
extension IntStack: SuffixableContainer {
    func suffix(_ size: Int) -> Stack<Int> {
        var result = Stack<Int>()
        for index in (count-size)..<count {
            result.append(self[index])
        }
        return result
    }
    // Inferred that Suffix is Stack<Int>.
}
```

## 제네릭의 Where절 \(Generic Where Clauses\)

제네릭에서도 where절을 사용할 수 있습니다. 다음은 Container C1, C2를 비교하며 모든 값이 같을 때 `true`를 반환하는 `allItemsMatch` 함수를 구현하면 다음과 같습니다.

```swift
func allItemsMatch<C1: Container, C2: Container>
    (_ someContainer: C1, _ anotherContainer: C2) -> Bool
    where C1.Item == C2.Item, C1.Item: Equatable {

        // Check that both containers contain the same number of items.
        if someContainer.count != anotherContainer.count {
            return false
        }

        // Check each pair of items to see if they're equivalent.
        for i in 0..<someContainer.count {
            if someContainer[i] != anotherContainer[i] {
                return false
            }
        }

        // All items match, so return true.
        return true
}
```

위에서 Container 종류 자체는 달라도 상관없습니다. 단지 각 Container의 같은 인덱스의 모든 값이 같기만 하면 결고로 `true`를 얻게 됩니다. 다음은 다른 타입의 Container, 하나는 스택, 다른 하나는 배열을 내용을 `allItemsMatch`함수로 비교한 결과 입니다.

```swift
var stackOfStrings = Stack<String>()
stackOfStrings.push("uno")
stackOfStrings.push("dos")
stackOfStrings.push("tres")

var arrayOfStrings = ["uno", "dos", "tres"]

if allItemsMatch(stackOfStrings, arrayOfStrings) {
    print("All items match.")
} else {
    print("Not all items match.")
}
// Prints "All items match."
```

Container 종류는 다르지만 안의 내용이 같으므로 모든 아이템일 일치한다는 결과를 반환합니다.

## Where절을 포함하는 제네릭의 익스텐션 \(Extensions with a Generic Where Clause\)

네익의 익스텐션을 선언할때 where절을 포함시킬 수 있습니다. 다음은 isTop함수를 익스텐션으로 추가하면서 이 함수가 추가되는 Stack은 반드시 `Equatable` 프로토콜을 따라야 한다고 제한을 부여한 코드입니다.

```swift
extension Stack where Element: Equatable {
    func isTop(_ item: Element) -> Bool {
        guard let topItem = items.last else {
            return false
        }
        return topItem == item
    }
}
```

아래의 예제에서 String은 `Equatable`프로토콜을 따르므로 `true`에 해당하는 분기가 실행됩니다.

```swift
if stackOfStrings.isTop("tres") {
    print("Top element is tres.")
} else {
    print("Top element is something else.")
}
// Prints "Top element is tres."
```

만약 아래와 같이 `Equatable`을 따르지 않는 Stack에서 익스텐션에 선언된 함수 isTop을 실행하면 에러가 발생하는 것을 확인할 수 있습니다.

```swift
struct NotEquatable { }
var notEquatableStack = Stack<NotEquatable>()
let notEquatableValue = NotEquatable()
notEquatableStack.push(notEquatableValue)
notEquatableStack.isTop(notEquatableValue)  // Error
```

다음 예제는 Container의 Item이 `Equatable`프로토콜을 따르는 제약을 추가한 예제입니다. `startsWith`함수의 인자인 Item은 Container의 특정 아이템이 입력한 Item으로 시작하는지 비교하기 위해서는 Container의 첫 아이템이 입력한 Item과 같은지 비교해야 하기 때문에 `Equatable` 프로토콜을 따라야 합니다.

```swift
extension Container where Item: Equatable {
    func startsWith(_ item: Item) -> Bool {
        return count >= 1 && self[0] == item
    }
}
```

Container프로토콜을 따르는 Array에서 `startsWith`를 실행합니다. Int값인 42는 `Equatable` 프로토콜을 따르므로 `startsWith`이 실행되고 42는 배열의 첫 값인 9와 같지 않으므로 같지 않다는 분기가 실행됩니다.

```swift
if [9, 9, 9].startsWith(42) {
    print("Starts with 42.")
} else {
    print("Starts with something else.")
}
// Prints "Starts with something else."
```

where절에서 특정 프로토콜을 따르는 것 뿐 아니라 특정 값 타입인지 비교하는 구분을 사용할 수도 있습니다. 아래는 Item이 Double형인지를 비교하는 구문을 사용한 예입니다.

```swift
extension Container where Item == Double {
    func average() -> Double {
        var sum = 0.0
        for index in 0..<count {
            sum += self[index]
        }
        return sum / Double(count)
    }
}
print([1260.0, 1200.0, 98.6, 37.0].average())
// Prints "648.9"
```

Container의 Item `[1260.0, 1200.0, 98.6, 37.0]`이 Double형이기 때문에 익스텐션에서 구현된 average\(\)를 사용할 수 있습니다.

## 제네릭의 연관 타입에 where절 적용 \(Associated Types with a Generic Where Clause\)

연관 타입에도 where절을 적용해 제한을 둘 수 있습니다. 아래는 연관 타입 Iterator에 Iterator의 Element가 Item과 같아야 한다는 조건을 건 예입니다.

```swift
protocol Container {
    associatedtype Item
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }

    associatedtype Iterator: IteratorProtocol where Iterator.Element == Item
    func makeIterator() -> Iterator
}
```

다른 프로토콜을 상속하는 프로토콜에도 where절로 조건을 부여할 수 있습니다.

```swift
protocol ComparableContainer: Container where Item: Comparable { }
```

## 제네릭 서브스크립트 \(Generic Subscripts\)

제네릭의 서브스크립트에도 조건을 걸 수 있습니다. 아래 예제는 `Indices.Iterator.Element`가 Int 형이어야 한다는 조건을 건 예입니다.

```swift
extension Container {
    subscript<Indices: Sequence>(indices: Indices) -> [Item]
        where Indices.Iterator.Element == Int {
            var result = [Item]()
            for index in indices {
                result.append(self[index])
            }
            return result
    }
}
```

