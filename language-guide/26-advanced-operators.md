# 고급 연산자 \(Advanced Operators\)

## 비트 연산자 \(Bitwise Operators\)

Swift에서도 여러 비트 연산자를 지원합니다.

### 비트 연산자 NOT \(Bitwise NOT Operator\)

비트 연산자 NOT은 비트를 뒤집습니다.

![](../.gitbook/assets/bitwisenot_2x.png)

```swift
let initialBits: UInt8 = 0b00001111
let invertedBits = ~initialBits  // equals 11110000
```

### 비트 연산자 AND \(Bitwise AND Operator\)

비트 연산자 AND는 두 비트가 같은 값인 경우 1 다른경우 0으로 변환합니다.

![](../.gitbook/assets/bitwiseand_2x.png)

```swift
let firstSixBits: UInt8 = 0b11111100
let lastSixBits: UInt8  = 0b00111111
let middleFourBits = firstSixBits & lastSixBits  // equals 00111100
```

### 비트 연산자 OR \(Bitwise OR Operator\)

비트 연산자 OR는 두 비트중 하나라도 1이면 1 값을 갖습니다.

![](../.gitbook/assets/bitwiseor_2x.png)

```swift
let someBits: UInt8 = 0b10110010
let moreBits: UInt8 = 0b01011110
let combinedbits = someBits | moreBits  // equals 11111110
```

### 비트 연산자 XOR \(Bitwise XOR Operator\)

비트 연산자 XOR은 두 비트가 다른 경우에 1, 같은 경우에 0을 갖습니다.

![](../.gitbook/assets/bitwisexor_2x.png)

```swift
let firstBits: UInt8 = 0b00010100
let otherBits: UInt8 = 0b00000101
let outputBits = firstBits ^ otherBits  // equals 00010001
```

### 비트 시프트 연산자 \(Bitwise Left and Right Shift Operators\)

비트를 왼쪽 혹은 오른쪽으로 미는 시프트 연산자도 지원합니다.

## 부호없는 Integer의 시프팅 \(Shifting Behavior for Unsigned Integers\)

![](../.gitbook/assets/bitshiftunsigned_2x.png)

```swift
let shiftBits: UInt8 = 4   // 00000100 in binary
shiftBits << 1             // 00001000
shiftBits << 2             // 00010000
shiftBits << 5             // 10000000
shiftBits << 6             // 00000000
shiftBits >> 2             // 00000001
```

아래 예제에서 pink와 16진수와 AND 연산을 하게 되는데 여기서 FF는 윈도우 역할을 해서 AND연산에서 FF의 위치에 있는 값만 취해 시프트 연산을 하게 됩니다.

```swift
let pink: UInt32 = 0xCC6699
let redComponent = (pink & 0xFF0000) >> 16    // redComponent is 0xCC, or 204
let greenComponent = (pink & 0x00FF00) >> 8   // greenComponent is 0x66, or 102
let blueComponent = pink & 0x0000FF           // blueComponent is 0x99, or 153
```

## 부호있는 Integer의 시프팅 \(Shifting Behavior for Signed Integers\)

부호가 있는 정수형에서는 최상단의 비트가 부호를 표현합니다. 0일때는 양수 1일때는 음수를 의미합니다. 연산의 결과는 각각 다음 그림과 같습니다.

![](../.gitbook/assets/bitshiftsignedfour_2x.png)

![](../.gitbook/assets/bitshiftsignedminusfour_2x.png)

![](../.gitbook/assets/bitshiftsignedminusfourvalue_2x.png)

![](../.gitbook/assets/bitshiftsignedaddition_2x.png)

![](../.gitbook/assets/bitshiftsigned_2x.png)

## 오버플로우 연산자 \(Overflow Operators\)

Int16의 값은 범위는 -32768~32767 값을 갖습니다. Int16의 최대값인 Int16.max에 값을 하나 더하면 Int16이 저장할 수 있는 값의 범위를 벗어나므로 에러가 발생합니다.

```swift
var potentialOverflow = Int16.max
// potentialOverflow equals 32767, which is the maximum value an Int16 can hold
potentialOverflow += 1
// this causes an error
```

만약 값이 넘치는 오버플로우가 발생하더라도 그 뒷 값을 자르고 연산을 수행하려면 다음과 같이 각 연산자 앞에 &를 붙여 수행하면 됩니다.

* 오버플로우 덧셈 \(&+\)
* 오버플로우 뺄셈 \(&-\)
* 오버플로우 곱셈 \(&\*\)  

**값 오버플로우 \(Value Overflow\)**

값은 양수쪽 혹은 음수쪽 양쪽으로 넘칠 수 있습니다.

```swift
  var unsignedOverflow = UInt8.max
  // unsignedOverflow equals 255, which is the maximum value a UInt8 can hold
  unsignedOverflow = unsignedOverflow &+ 1
  // unsignedOverflow is now equal to 0
```

여기서 오버플로우 덧셈을 하면 그결과는 다음과 같이 값이 초기화 됩니다.

![](../.gitbook/assets/overflowaddition_2x.png)

* 만약 오버플로우 뺄셈을 하면 최대 값을 같습니다.

  ```swift
  var unsignedOverflow = UInt8.min
  // unsignedOverflow equals 0, which is the minimum value a UInt8 can hold
  unsignedOverflow = unsignedOverflow &- 1
  // unsignedOverflow is now equal to 255
  ```

![](../.gitbook/assets/overflowunsignedsubtraction_2x.png)

만약 부호를 갖는 Int형의 경우 오버플로우 뺄셈 연산을 하면 결과는 다음과 같습니다.

```swift
var signedOverflow = Int8.min
// signedOverflow equals -128, which is the minimum value an Int8 can hold
signedOverflow = signedOverflow &- 1
// signedOverflow is now equal to 127
```

![](../.gitbook/assets/overflowsignedsubtraction_2x.png)

**연산자 수행의 우선순위 \(Precedence and Associativity\)**

연산자 별로 우선순위가 있어서 어떤 등식의 순서대로 연산이 수행되는 것이 아니라 연산자의 우선순위 별로 연산을 수행합니다. 아래와 같은 경우 결과 값은 17입니다.

```swift
2 + 3 % 4 * 5
// this equals 17
```

이유는 몫 연산자인 3%4가 우선 수행되고 그다음 곰셈 연산다 \* 5 그리고 마지막으로 덧셈 연산자가 수행돼 결과는 17이 됩니다.

```swift
2 + ((3 % 4) * 5)
(3 % 4) is 3, so this is equivalent to:
2 + (3 * 5)
(3 * 5) is 15, so this is equivalent to:
2 + 15
```

**연산자 메소드 \(Operator Methods\)**

+,++같은 연산자는 메소드로 수행되고 이 메소드를 오버라이딩 할 수 있습니다. 아래 예제는 2D Vector 구조체를 더하는 + 메소드를 선언한 예입니다.

```swift
struct Vector2D {
    var x = 0.0, y = 0.0
}

extension Vector2D {
    static func + (left: Vector2D, right: Vector2D) -> Vector2D {
        return Vector2D(x: left.x + right.x, y: left.y + right.y)
    }
}
```

```swift
let vector = Vector2D(x: 3.0, y: 1.0)
let anotherVector = Vector2D(x: 2.0, y: 4.0)
let combinedVector = vector + anotherVector
// combinedVector is a Vector2D instance with values of (5.0, 5.0)
```

vector + anotherVector를 수행하면 +메소드가 수행되 두 2차원 좌표의 x, y 값을 각각 더합니다.

![](../.gitbook/assets/vectoraddition_2x.png)

### 접두, 접미 연산자 \(Prefix and Postfix Operators\)

어떤 피 연산자 앞에 붙이는 접두 연산자를 정의 할 수 있습니다. 아래와 같이 -를 붙이면 이후 값을 음수로 변환하도록 함수를 정의합니다.

```swift
extension Vector2D {
    static prefix func - (vector: Vector2D) -> Vector2D {
        return Vector2D(x: -vector.x, y: -vector.y)
    }
}
```

아래 코드를 수행하면 2차원 좌표가 음수로 변한 것을 확인할 수 있습니다.

```swift
let positive = Vector2D(x: 3.0, y: 4.0)
let negative = -positive
// negative is a Vector2D instance with values of (-3.0, -4.0)
let alsoPositive = -negative
// alsoPositive is a Vector2D instance with values of (3.0, 4.0)
```

### 할당 연산자의 합성 \(Compound Assignment Operators\)

할당 연산자를 오버라이딩 할 수 있습니다. 아래는 `+=`연산자를 재정의 한 예 입니다. 좌변은 in-out 파라미터로 좌변에 좌변과 우변을 더한 값을 할당합니다.

```swift
extension Vector2D {
    static func += (left: inout Vector2D, right: Vector2D) {
        left = left + right
    }
}
```

위 할당 연산자를 실행하면 아래와 같이 원래 값에 더한 값이 추가된 것을 확인할 수 있습니다.

```swift
var original = Vector2D(x: 1.0, y: 2.0)
let vectorToAdd = Vector2D(x: 3.0, y: 4.0)
original += vectorToAdd
// original now has values of (4.0, 6.0)
```

> NOTE  
> 할당 연산자중 기본 할당연산자 `=` 는 오버로딩 할 수 없습니다. 삼중 조건 연산자인 `a ? b : c` 또한 오버로딩 할 수 없습니다.

### 등위 연산자 \(Equivalence Operators\)

두 값 혹은 객체를 비교하는 등위 연산자 `==`를 오버라이딩 할 수 있습니다. 다음은 2차원 좌표의 등위를 비교하는 `==` 연산자를 오버라이딩 한 예입니다.

```swift
extension Vector2D: Equatable {
    static func == (left: Vector2D, right: Vector2D) -> Bool {
        return (left.x == right.x) && (left.y == right.y)
    }
}
```

두 좌표의 x, y가 각각 같은 경우에만 같다고 판별 합니다.

```swift
let twoThree = Vector2D(x: 2.0, y: 3.0)
let anotherTwoThree = Vector2D(x: 2.0, y: 3.0)
if twoThree == anotherTwoThree {
    print("These two vectors are equivalent.")
}
// Prints "These two vectors are equivalent."
```

Swift에서는 등위 연산자의 구현을 자동으로 합성해 주기도 합니다. 다음의 경우 Swift에서 등위 연산에 대해 자동으로 제공해 줍니다.

* 구조체가 저장 프로퍼티만 갖는 경우 Equatable 프로토콜을 따르게 됩니다.
* 열거형이 연관 타입만 갖는 경우 Equatable 프로토콜을 따르게 됩니다.
* 열거형이 연관 타입이 없는 경우

  Swift가 자동으로 제공하는 등위연산을 이용해 3차원 좌표의 등위도 아래와 같이 비교할 수 있습니다. 3차원 좌표 구조체는 저장 프로퍼티만 있기 때문에 Swift에서 자동으로 등위연산을 제공해 줍니다.

```swift
struct Vector3D: Equatable {
    var x = 0.0, y = 0.0, z = 0.0
}

let twoThreeFour = Vector3D(x: 2.0, y: 3.0, z: 4.0)
let anotherTwoThreeFour = Vector3D(x: 2.0, y: 3.0, z: 4.0)
if twoThreeFour == anotherTwoThreeFour {
    print("These two vectors are also equivalent.")
}
// Prints "These two vectors are also equivalent."
```

## 커스텀 연산자 \(Custom Operators\)

기본 연산자에는 커스텀 전위 연산자 +++를 정의해 사용할 수 있습니다.

```swift
prefix operator +++
```

이 연산은 자신의 x, y값을 각각 2배하는 연산을 수행하도록 정의합니다.

```swift
extension Vector2D {
    static prefix func +++ (vector: inout Vector2D) -> Vector2D {
        vector += vector
        return vector
    }
}

var toBeDoubled = Vector2D(x: 1.0, y: 4.0)
let afterDoubling = +++toBeDoubled
// toBeDoubled now has values of (2.0, 8.0)
// afterDoubling also has values of (2.0, 8.0)
```

정의 한대로 `+++`연산자가 동작하는 것을 확인할 수 있습니다.

### 중위 연산자의 수행 \(Precedence for Custom Infix Operators\)

다음과 같이 두 피연산자 사이에 존재하는 중위 연산자를 정의해 사용할 수 있습니다. x 값은 더하고 y 값은 서로 빼는 연산으로 정의한 예는 다음과 같습니다.

```swift
infix operator +-: AdditionPrecedence
extension Vector2D {
    static func +- (left: Vector2D, right: Vector2D) -> Vector2D {
        return Vector2D(x: left.x + right.x, y: left.y - right.y)
    }
}
let firstVector = Vector2D(x: 1.0, y: 2.0)
let secondVector = Vector2D(x: 3.0, y: 4.0)
let plusMinusVector = firstVector +- secondVector
// plusMinusVector is a Vector2D instance with values of (4.0, -2.0)
```

`+-` 중위 연산이 정의한 대로 동작하는 것을 확인할 수 있습니다.

