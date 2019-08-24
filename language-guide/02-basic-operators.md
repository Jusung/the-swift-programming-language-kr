# 기본 연산자 \(Basic Operators\)

Swift에서는 통상적으로 이용하는 `+`, `-`, `/`, `%` 같은 산술연산자와 `&&`, `||` 같은 논리 연산자, 그리고 C에서 지원하지 않는 `a..<b` 나 `a…b`같이 값의 범위를 지정할 수 있는 범위 연산자를 지원합니다.

## 용어\(Terminology\)

연산자에는 단항\(unary\), 이항\(binary\) 그리고 삼항\(ternary\) 연산자가 있습니다.

* 단항 연산자는 `-a`, `!b`, `c!`와 같이 하나의 대상에 앞뒤에 바로 붙여 사용하는 연산자입니다.
* 이항 연산자는 `2 + 3` 같이 두 대상 사이에 위치하는 연산자입니다.
* 삼항 연산자는 `a ? b : c` 형태로 Swift에 삼항 연산자는 이 연산자 단 하나만 존재합니다.

## 할당 연산자\(Assignment Operator\)

할당 연산자는 값을 초기화 시키거나 변경합니다. 아래와 같이 상수, 변수 모두에 사용 가능합니다.

```text
let b = 10
var a = 5
a = b
// a 값은 10
```

아래와 같이 튜플을 이용해 여러 값을 한번에 할당할 수 있습니다.

```text
let (x, y) = (1, 2)
// x 는 1, y 값은 2 가 됩니다.
```

C나 Objective-C와는 다르게 Swift에서는 할당 연산자는 값을 반환하지 않습니다.

```text
if x = y {
    //  x= y 는 값을 반환하지 않기 때문에 이 문법은 올바르지 않습니다.
}
```

할당 연산자가 값을 반환하지 않는 이유는 동등비교연산자\(==\)를 사용해야 하는 곳에 할당연산자\(=\)가 실수로 사용되는 것을 막기 위해서 입니다.

## 사칙 연산자\(Arithmetic Operators\)

Swift는 모든 숫자 형에서 사용 가능한 4가지 표준 사칙 연산자를 지원합니다.

* 덧셈 \(+\)
* 뺄셈 \(-\)
* 곱셉 \(\*\)
* 나눗셈 \(/\)

```swift
1 + 2       // 3
5 - 3       // 2
2 * 3       // 6
10.0 / 2.5  // 4.0
```

C나 ObjectiveC와 달리 Swift는 사칙 연산의 값이 오버플로우 되는 것을 허용하지 않습니다. 만약 이것을 허용하고 싶으면 Swift의 오버플로우 연산자를 이용해 지원할 수 있습니다. 덧셈 연산자는 아래와 같이 문자열을 합치기 위해 사용할 수 있습니다.

```swift
"hello, " + "world"  // equals "hello, world"
```

### 나머지 연산자\(Remainder Operator\)

`a % b`와 같이 나머지 연산을 지원합니다.

```text
9 % 4    // 1
-9 % 4   // -1
```

![](../.gitbook/assets/remainderinteger_2x.png)

### 단항 음수 연산자\(Unary Minus Operator\)

숫자 값은 `-` 로 표현되는 단항 음수 연산자에 의해 부호가 변합니다.

```swift
let three = 3
let minusThree = -three       // minusThree는 -3
let plusThree = -minusThree   // plusThree는 3, 혹은 "minus minus 3"
```

### 단항 양수 연산자\(Unary Plus Operator\)

`+`로 표현되는 단항 양수 연산자는 부호에 아무런 영향을 끼치지 않습니다.

```swift
let minusSix = -6
let alsoMinusSix = +minusSix  // alsoMinusSix는 -6
```

## 합성 할당 연산자 \(Compound Assignment Operators\)

a = a + 2와 같이 할당연산\(=\)과 덧셈연산\(+\)으로 구성된 연산을 합성해 `+=`형태로 축약해 사용 가능 합니다.

```swift
var a = 1
a += 2
// a는 3
```

> _주의_  
> 합성 할당 연산자는 값을 반환하지 않습니다. 즉, `let b = a+=2` 와 같은 문법은 사용할 수 없습니다.

더 많은 정보는 [연산자 선언\(Operator Declarations\)](https://developer.apple.com/documentation/swift/swift_standard_library/operator_declarations) 에서 확인할 수 있습니다.

## 비교 연산자\(Comparison Operators\)

Swift에서는 표준 C에서 제공하는 비교 연산자를 모두 지원합니다.

* 같다 \(a == b\)
* 같지않다 \(a != b\)
* 크다 \(a &gt; b\)
* 작다 \(a &lt; b\)
* 크거나 같다 \(a &gt;= b\)
* 작거나 같다 \(a &lt;= b\)

> _주의_   
> Swift는 객체 비교를 위해 식별 연산자 `===` 과 `!==`를 제공합니다.

각 비교연산은 true 혹은 false 값을 반환합니다.

```swift
1 == 1   // true
2 != 1   // true
2 > 1    // true
1 < 2    // true
1 >= 1   // true
2 <= 1   // false
```

비교 연산은 if-else와 같은 조건 구문에서 자주 사용 됩니다.

```swift
let name = "world"
if name == "world" {
    print("hello, world")
} else {
    print("I'm sorry \(name), but I don't recognize you")
}
// Prints "hello, world", because name is indeed equal to "world".
```

if문과 관련한 더 많은 정보는 [제어문 ****\(Control Flow\)](https://jusung.gitbook.io/the-swift-language-guide/language-guide/05-control-flow) ****에서 확인할 수 있습니다.

같은 타입의 값을 갖는 두 개의 튜플을 비교할 수 있습니다. 튜플의 비교는 왼쪽에서 오른쪽 방향으로 이뤄지고 한번에 한개의 값만 비교합니다. 이 비교를 다른 두 값을 비교하게 될 때까지 수행합니다.

```swift
(1, "zebra") < (2, "apple")   // true, 1이 2보다 작고; zebra가 apple은 비교하지 않기 때문
(3, "apple") < (3, "bird")    // true 왼쪽 3이 오른쪽 3과 같고; apple은 bird보다 작기 때문
(4, "dog") == (4, "dog")      // true 왼쪽 4는 오른쪽 4와 같고 왼쪽 dog는 오른쪽 dog와 같기 때문
```

첫 번째 줄의 튜플의 비교는 1이 2와 같지 않기 때문에 이 시점에 튜플의 비교는 종료됩니다. 반면 두 번째와 세 번째 줄의 튜플의 비교는 3과 4 숫자가 비교하는 튜플이 각각 같기 때문에 문자도 비교해봅니다.

튜플은 비교 연산자가 해당 값을 비교할 수 있는 경우에만 비교를 수행할 수 있습니다.

```swift
("blue", -1) < ("purple", 1)        // 비교가능, 결과 : true
("blue", false) < ("purple", true)  // 에러, Boolean 값은 < 연산자로 비교할 수 없기 때문에
```

> Swift 표준 라이브러리에서는 7개 요소 미만을 갖는 튜플만 비교할 수 있습니다. 만약 7개 혹은 그 이상의 요소를 갖는 튜플을 비교하고 싶으면 직접 비교 연산자를 구현해야 합니다.

## 삼항 조건 연산자\(Ternary Conditional Operator\)

삼항 조건 연산자는 `question ? answer1 : answer2`의 구조를 갖습니다. 그래서 question 조건이 참인경우 answer1이 거짓인 경우 answer2가 실행됩니다. 삼항 조건 연산자는 아래 코드의 축약입니다.

```swift
if question {
    answer1
} else {
    answer2
}
```

삼항 조건 연산의 실제 예 입니다.

```swift
let contentHeight = 40
let hasHeader = true
let rowHeight = contentHeight + (hasHeader ? 50 : 20)
// rowHeight는 90 (40 + 50)
```

위에서 삼항 조건 연산을 다음과 같이 풀어 쓸 수 있습니다.

```swift
let contentHeight = 40
let hasHeader = true
let rowHeight: Int
if hasHeader {
    rowHeight = contentHeight + 50
} else {
    rowHeight = contentHeight + 20
}
// rowHeight는 90입니다.
```

삼항 조건 연산자는 코드를 짧게 만들어 가독성을 높여줍니다.

## Nil 병합 연산자\(Nil-Coalescing Operator\)

nil 병합 연산자는 `a ?? b` 형태를 갖는 연산자 입니다. 옵셔널 `a`를 벗겨서\(unwraps\) 만약 `a`가 `nil` 인 경우 `b`를 반환합니다. 이 `nil` 병합 연산자는 다음 코드의 축약형입니다.

```swift
a != nil ? a! : b
```

삼항 조건 연산자를 사용했는데요. 옵셔널 `a`가 `nil`이 아니면 `a`를 unwrap하고`nil`이면 `b`를 반환하라는 의미입니다.

예제를 보시겠습니다.

```swift
let defaultColorName = "red"
var userDefinedColorName: String?   // 이 값은 defaults 값 nil입니다.

var colorNameToUse = userDefinedColorName ?? defaultColorName
// userDefinedColorNam이 nil이므로 colorNameToUse 값은 defaultColorName인 "red"가 설정 됩니다.
```

```swift
userDefinedColorName = "green"
colorNameToUse = userDefinedColorName ?? defaultColorName
// userDefinedColorName가 nil이 아니므로 colorNameToUse 는 "green"이 됩니다.
```

## 범위 연산자\(Range Operators\)

### 닫힌 범위 연산자\(Closed Range Operator\)

`(a..b)`의 형태로 범위의 시작과 끝이 있는 연산자 입니다. for-in loop에 자주 사용됩니다.

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

for-in loop에 관한 더 많은 정보는 [제어문 \(Control Flow\)](https://jusung.gitbook.io/the-swift-language-guide/language-guide/05-control-flow)에서 보실 수 있습니다.

### 반 닫힌 범위 연산자\(Half-Open Range Operator\)

`(a..<b)`의 형태로 `a`부터 `b`보다 작을 때까지의 범위를 갖습니다. 즉, `a`부터 `b-1`까지 값을 갖습니다. 이건 어디서 많이 본 범위! 그렇습니다. 보통 배열이 배열의 크기 - 1의 인덱스를 갖기 때문에 이 반 닫힌 범위 연산자는 배열을 다루는데 유용합니다. 다음은 관련 예제입니다.

```swift
let names = ["Anna", "Alex", "Brian", "Jack"]
let count = names.count
for i in 0..<count {
    print("Person \(i + 1) is called \(names[i])")
}
// Person 1 is called Anna
// Person 2 is called Alex
// Person 3 is called Brian
// Person 4 is called Jack
```

### 단방향 범위\(One-Side Ranges\)

`[a..]` `[..a]`의 형태로 범위의 시작 혹은 끝만 지정해 사용하는 범위 연산자 입니다. 지정한 시작 값 혹은 끝 값은 범위에 포함됩니다. 다음은 관련 예제 입니다.

```swift
for name in names[2...] {
    print(name)
}
// Brian
// Jack

for name in names[...2] {
    print(name)
}
// Anna
// Alex
// Brian
```

위에서 설명했듯이 지정한 시작 혹은 끝 값이 범위에 포함된 결과입니다. 하지만 앞서 설명드린 반 닫힌 연산자는 다음과 같이 설정 값이 범위에 포함되지 않습니다.

```swift
for name in names[..<2] {
    print(name)
}
// Anna
// Alex
```

단방향 범위 연산자는 subscript뿐만 아니라 아래와 같이 특정 값을 포함하는지 여부를 확인할 때도 사용 가능합니다.

```swift
let range = ...5
range.contains(7)   // false
range.contains(4)   // true
range.contains(-1)  // true
```

## 논리 연산자\(Logical Operators\)

Swift에서는 세가지 표준 논리 연산자를 지원합니다.

* 논리 부정 NOT \(`!a`\)
* 논리 곱 AND \(`a && b`\)
* 논리 합 OR \(`a || b`\)

### 논리 부정 연산자\(Logical NOT Operator\)

```swift
let allowedEntry = false
if !allowedEntry {
    print("ACCESS DENIED")
}
// Prints "ACCESS DENIED"
```

### 논리 곱 연산자\(Logical AND Operator\)

```swift
let enteredDoorCode = true
let passedRetinaScan = false
if enteredDoorCode && passedRetinaScan {
    print("Welcome!")
} else {
    print("ACCESS DENIED")
}
// Prints "ACCESS DENIED"
```

### 논리 합\(OR\) 연산자\(Logical OR Operator\)

```swift
let hasDoorKey = false
let knowsOverridePassword = true
if hasDoorKey || knowsOverridePassword {
    print("Welcome!")
} else {
    print("ACCESS DENIED")
}
// Prints "Welcome!"
```

### 논리 연산자의 조합\(Combining Logical Operators\)

두 개 이상의 논리 연산자를 조합해서 사용할 수 있습니다.

```swift
if enteredDoorCode && passedRetinaScan || hasDoorKey || knowsOverridePassword {
    print("Welcome!")
} else {
    print("ACCESS DENIED")
}
// Prints "Welcome!"
```

> Swift의 논리 연산자 && 와 \|\| 는 왼쪽의 표현을 우선해서 논리 계산을 합니다.

### 명시적 괄호\(Explicit Parentheses\)

논리 연산자의 적용 우선 순위를 연산자에 맡지기 않고 명시적으로 괄호를 사용해 계산 순서를 지정할 수 있습니다.

```swift
if (enteredDoorCode && passedRetinaScan) || hasDoorKey || knowsOverridePassword {
    print("Welcome!")
} else {
    print("ACCESS DENIED")
}
// Prints "Welcome!"
```

이렇게 괄호를 사용하면 가독성이 높아져 코드의 의도를 더 명확하게 하는데 도움이 됩니다.

