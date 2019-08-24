# 문자열과 문자 \(Strings and Characters\)

Swift의 `String`은 `Foundation` 프레임워크의 `NSString`이 bridge된 타입이기 때문에 `NSString`의 메소드를 `String`에서 캐스팅 없이 사용 가능합니다.

## 문자열 리터럴

문자열은 큰 따옴표\(`“`\)로 묶어 표현 합니다.

```swift
let something = "Some string literal value"
```

### 여러줄 문자열 리터럴

여러줄의 문자열을 사용하고 싶은 경우 큰 따옴표 3개\(“””\)로 묶어서 사용할 수 있습니다.

```text
let quotation = """
The White Rabbit put on his spectacles.  "Where shall I begin,
please your Majesty?" he asked.

"Begin at the beginning," the King said gravely, "and go on
till you come to the end; then stop."
"""
```

여러줄 문자열을 사용할 때는 첫 시작의 `"""` 다음 줄부터 마지막 `"""`의 직전까지를 문자열로 봅니다. 그래서 아래 두 줄의 표현으로 이루어진 `singleLineString`과 `multilineString`은 같은 값을 갖게 됩니다.

```swift
let singleLineString = "These are the same."
let multilineString = """These are the same."""
```

여러줄 문자열을 사용하며 줄바꿈을 하고 싶으면 백슬래쉬\(`\`\)를 사용합니다.

```swift
let softWrappedQuotation = """
The White Rabbit put on his spectacles.  "Where shall I begin, \
please your Majesty?" he asked.

"Begin at the beginning," the King said gravely, "and go on \
till you come to the end; then stop."
"""
```

문자열의 시작과 끝에 각각 빈줄을 넣고 싶다면 한 줄을 띄어서 문자열을 입력하면 됩니다.

```swift
let lineBreaks = """

This string starts with a line break.
It also ends with a line break.

"""
```

들여쓰기도 가능합니다. 들여쓰기 기준은 끝나는 지점의 `"""`의 위치입니다. 아래의 경우에는 닫는 `"""`위치 앞에 있는 문자들은 전부 무시되고 그 이후의 공백은 문자열에 반영됩니다.

![](../.gitbook/assets/45ce334f-7bb5-42ce-bde5-998c708e567a.png)

### 문자열 리터럴의 특수 문자

문자열 리터럴은 다음과 같은 특수 문자를 포함할 수 있습니다.

* `\0`, `\`, `\t`, `\n`, `\r`, `\”`, `\’`
* `\u{n}`, n은 1-8자리 십진수 형태로 구성된 유니코드

  ```swift
  let wiseWords = "\"Imagination is more important than knowledge\" - Einstein"
  // "Imagination is more important than knowlege" - Einstein
  let dollaSign = "\u{24}"            // $, 유니코트 U+0024
  let blackHeart = "\u{2665}"         // ♥, 유니코드 U+2665
  let sparklingHeart = "\u{1F496}" // 💖,유니코드 U+1F496
  ```

## 빈 문자열 초기화

아래 예의 두 변수의 문자열 값은 같습니다.

```swift
var emtpryString = ""
var anotherEmptyString = String()
```

문자열이 비어있는지 여부를 확인하기 위해서는 `isEmpty` 프로퍼티를 이용합니다.

```swift
if emptyString.isEmpty {
    print("Nothing to see here")
}
// Prints "Nothing to see here"
```

### 문자열 수정

```swift
var variableString = "Horse"
variableString += " and carriage"
// variableString = Horse and carriage

let constantString = "Highlander"
constantString += " and another Highlander"
// 문자열 상수(let)로 선언돼 있어 에러발생!
```

## 값 타입 문자열

Swift의 `String`은 값 타입\(value type\)입니다. 그래서 `String`이 다른 함수 혹은 메소드로 부터 생성되면 `String`값이 할당 될때, 이전 `String`의 레퍼런스를 할당하는 것이 아니라 값을 복사해서 생성합니다. 반대로 이야기 하면 다른 메소드에서 할당 받은 문자열은 그 문자열을 수정해도 원본 문자열이 변하지 않기 때문에 편하게 사용하셔도 됩니다.

## 문자

문자열의 개별 문자를 `for-in` loop을 사용해 접근할 수 있습니다.

```swift
for character in "Dog!🐶" {
    print(character)
}
// D
// o
// g
// !
// 🐶
```

다음과 같이 문자 상수를 선언할 수 있습니다.

```swift
let exclamationMark: Character = "!"
```

문자 배열을 이용해 문자열의 초기화 메소드에 인자로 넣어 문자열을 생성할 수 있습니다.

```swift
let catCharacters: [Character] = ["C", "a", "t", "!", "🐱"]
let catString = String(catCharacters)
print(catString)
// Prints "Cat!🐱"
```

## 문자열과 문자의 결합

```swift
let string1 = "hello"
let string2 = " there"
var welcome = string1 + string2
// welcome : "hello there"
```

```swift
var instruction = "look over"
instruction += string2
// instruction : "look over there"
```

```swift
let exclamationMark: Character = "!"
welcome.append(exclamationMark)
// welcome : "hello there!"
```

## 문자열 삽입

백슬래쉬 괄호를 이용해 상수, 변수, 리터럴 값을 문자열에 추가할 수 있습니다.

```swift
let mutiplier = 3
let message = "\(multiplier) times 2.5 is \(Double(multiplier) * 2.5)"
// message : "3 times 2.5 is 7.5"
```

## 유니코드

* 유니코드는 전 세계의 모든 문자를 컴퓨터에서 일관되게 표현하고 다룰 수 있도록 설계된 국제 표준입니다. Swift의 문자열과 문자 타입은 유니코드에 순응\(compliant\)합니다.

**유니코드 스칼라**

Swift의 네이티브 문자열 타입은 유니코드 스칼라 값으로 만들어 졌습니다. 하나의 유니코드는 21비트의 숫자로 구성돼 있습니다. 예를 들면 `U+0061`는 라틴어의 소문자 `a`를 나타내고 `U+1F425`는 정면의 병아리 🐥 를 나타냅니다.

**자모 그룹의 확장**

유니코드를 결합해서 사용할 수 있습니다.

```swift
  let eAcute: Character = "\u{E9}"  // é
  let combinedEAcute: Character = "\u{65}\u{301}"  // e +  ́
  // eAcute : é, combinedEAcute : é
```

아래는 한글의 `“한”`자를 단독으로 사용했을 때와 `ㅎ`,`ㅏ`,`ㄴ`의 자모를 따로 결합해서 사용한 예 입니다.

```swift
  let precomposed: Character = "\u{D55C}"                        // 한
  let decomposed: Character = "\u{1112}\u{u1161}\u{11AB}"    // ㅎ, ㅏ,ㄴ
  // precomposed : 한, decomposed 한
```

아래는 `é`\(E9\)와 원심볼\(20DD\)을 결합한 결과입니다.

```swift
  let enclosedEAcute: Character = "\u{E9}\u{20DD}"
  // enclosedEAcute : é⃝
```

아래는 지역심볼문자인 `U`\(1F1FA\)와 `S`\(1F1F8\)를 결합한 결과입니다.

```swift
  let regionalIndicatorForUS: Character = "\u{1F1FA}\u{1F1F8}"
  // regionalIndicatorForUS : 🇺🇸
```

**문자 세기**

문자열의 문자의 순자를 세기 위해서는 문자열의 `count` 프로퍼티를 이용합니다.

```swift
  let unusualMenagerie = "Koala 🐨, Snail 🐌, Penguin 🐧, Dromedary 🐪"
  print("unusualMenagerie has \(unusualMenagerie.count) characters")
  // "unusualMenagerie의 문자는 40개"
```

**문자열의 접근과 수정**

문자열의 수정과 접근은 문자열 메소드 혹은 프로퍼티를 이용하거나 서브스크립트\(subscript\) 문법을 이용해 할 수 있습니다.

**문자열 인덱스**

아래와 같이 `startIndex`, `endIndex`, `index(before:)`, `index(after:)`, `index(_:offsetBy:)` 메소드 등을 이용해 문자열에서 특정 문자에 접근할 수 있습니다.

> _주의_  
> 위 메소드들은 Collection 프로토콜을 따르는 Array, Dictionary, Set 등에서도 동일하게 사용할 수 있습니다.

```swift
let greeting = "Guten Tag!"
greeting[greeting.startIndex]
// G
greeting[greeting.index(before: greeting.endIndex)]
// !
greeting[greeting.index(after: greeting.startIndex)]
// u
let index = greeting.index(greeting.startIndex, offsetBy: 7)
greeting[index]
// a
```

문자열의 인덱스를 벗어나는 문자를 가져오려고 하면 런타임 에러가 발생합니다.

```swift
greeting[greeting.endIndex] // 에러!
greeting.index(after: greeting.endIndex) // 에러!
```

문자열의 개별 문자를 접근하기 위해서는 indices 프로퍼티를 사용합니다.

```swift
for index in greeting.indices {
    print("\(greeting[index]) ", terminator: "")
// G u t e n  T a g !
```

### 문자의 삽입과 삭제

문자의 삽입과 삭제에는 `insert(:at:)`_,_ `insert(contentsOf:at:)`_,_ `remove(at:)`_,_ `removeSubrange(:)` 메소드를 사용할 수 있습니다.

> _주의_  
> 위 메소드들은 RangeReplaceableCollection 프로토콜을 따르는 Array, Dictionary, Set 등에서도 동일하게 사용할 수 있습니다.

```swift
var welcome = "hello"
welcome.insert("!", at: welcome.endIndex)
// welcome : hello!

welcome.insert(contentsOf: " there", at: welcome.index(before: welcome.endIndex))
// welcome : hello there!
```

```swift
welcome.remove(at: welcome.index(before: welcome.endIndex))
// welcome : hello there

let range = welcome.index(welcome.endIndex, offsetBy: -6)..<welcome.endIndex
welcome.removeSubrange(range)
// welcome : hello
```

## 부분 문자열

문자열에서 부분문자를 얻기 위해 `prefix (_:)`와 같은 서브스크립트 메소드를 이용할 수 있는데, 그렇게 얻은 부분 문자열은 문자열\(String\) 인스턴스가 아니라 부분문자열\(SubString\) 인스턴스 입니다. 만약 부분 문자열을 단기간에 사용하는게 아니라 오랜기간 사용한다면 문자열 인스턴스로 바꿔서 사용하는게 좋습니다.

```swift
let greeting = "Hello, World!"
let index = greeting.index(of: ",") ?? greeting.endIndex
let beginning = greeting[..<index]
// beginning : Hello

// SubString인 beginning을 String으로 변환
let newString = String(beginning)
```

위와 같이 사용해야하는 것이 좋은 이유는 메모리 관리 때문입니다. SubString은 해당 문자를 직접 갖고 있는 것이 아니라 원본 String의 메모리를 참조해 사용합니다.

![](../.gitbook/assets/99d5e388-2e8f-4b46-8ff4-86d558154224.png)

그래서 SubString을 계속 이용하는 이상은 원본 String이 계속 메모리에 남아 있게 됩니다. 사용하지 않는 문자열까지도 남게 되는 것이죠. 그렇게 때문에 SubString을 오래 사용하고자 한다면 위 예제처럼 String에서 인스턴스로 만들어 사용하고자 하는 문자만 메모리에 올려놓고 사용하는 것이 관리 효율면에서 좋다고 할 수 있습니다.

> _주의_  
> String과 Substring 모두 StringProtocol을 따릅니다. 그래서 문자 조작에 필요한 편리한 매소스들을 공통으로 사용할 수 있습니다.

문자열 비교 Swift에서는 문자열과 문자 , 접두사, 접미사를 비교하는 방법을 제공합니다.

### 문자열과 문자 비교

문자열과 문자 비교에는 `==` 혹은 `!=` 연산자를 사용합니다.

```swift
let quotation = "We're a lot alike, you and I."
let sameQuotation = "We're a lost alike, you and I."
if quotation == sameQuotation {
    print("These two strings are considered equal")
}
// These two strings are considered equal 출력
```

유니코드는 결합된 문자열을 갖고 비교하게 됩니다.

```swift
// "Voulez-vous un café?" using LATIN SMALL LETTER E WITH ACUTE
let eAcuteQuestion = "Voulez-vous un caf\u{E9}?"

// "Voulez-vous un café?" using LATIN SMALL LETTER E and COMBINING ACUTE ACCENT
let combinedEAcuteQuestion = "Voulez-vous un caf\u{65}\u{301}?"

if eAcuteQuestion == combinedEAcuteQuestion {
    print("These two strings are considered equal")
}
// These two strings are considered equal 출력
```

같은 유니코드 문자여도 유니코드가 다르면 다른 문자로 판별합니다. 아래 예제는 영어의 알파벳 대문자 `A(U+0041)`와 러시아어에서 사용되는 대문자 `A(U+0410)`를 비교한 것입니다.

```swift
let latinCapitalLetterA: Character = "\u{41}"

let cyrillicCapitalLetterA: Character = "\u{0410}"

if latinCapitalLetterA != cyrillicCapitalLetterA {
    print("These two characters are not equivalent.")
}
// Prints "These two characters are not equivalent."
```

> _주의_  
> Swift에서 문자열과 문자의 비교는 언어를 고려하지 않습니다. 다시말해, 언어와 상관없이 같은 문자면 같은 문자로 취급합니다.

### 접두사와 접미사 비교

접두사와 접미사의 비교를 위해 `hasPrefix(:)`_,_ `hasSuffix(:)` 메소드를 사용할 수 있습니다.

```swift
let romeoAndJuliet = [
    "Act 1 Scene 1: Verona, A public place",
    "Act 1 Scene 2: Capulet's mansion",
    "Act 1 Scene 3: A room in Capulet's mansion",
    "Act 1 Scene 4: A street outside Capulet's mansion",
    "Act 1 Scene 5: The Great Hall in Capulet's mansion",
    "Act 2 Scene 1: Outside Capulet's mansion",
    "Act 2 Scene 2: Capulet's orchard",
    "Act 2 Scene 3: Outside Friar Lawrence's cell",
    "Act 2 Scene 4: A street in Verona",
    "Act 2 Scene 5: Capulet's mansion",
    "Act 2 Scene 6: Friar Lawrence's cell"
]
```

다음 코드는 문자열 배열에서 접두어 `Act 1`가 몇개 들어있는지 확인하는 코드 입니다.

```swift
var act1SceneCount = 0
for scene in remeoAndJuliet {
    if scene.hasPrefix("Act 1 ") {
        act1SceneCount += 1
    }
}
print("There are \(act1SceneCount) scenes in Act 1")
// There are 5 scenes in Act 1
```

다음 코드는 문자열 배열에서 접미어 `Capulet's mansion` 과 `Friar Lawrences' cell` 이 각각 몇개 들어있는지 확인하는 코드 입니다.

```swift
var mansionCount = 0
var cellCount = 0
for scene in remeoAndJuliet {
    if scene.hasSuffix("Capulet's mansion") {
        mansionCount += 1
    } else if scene.hasSuffix("Friar Lawrence's cell") {
        cellCount += 1
    }
}
print("\(mansionCount) mansion scenes; \(cellCount) cell scenes")
// 6 mansion scenes; 2 cell scenes
```

## 문자열의 유니코드 표현

유니코드 문자가 텍스트 파일이나 다른 저장소에 쓰여질 때 유니코드 스칼라는 UTF-8, UTF-16, UTF-32 등 다양한 유니코드 인코딩 방식이 사용됩니다.

```text
let dogString = "Dog!!🐶"
```

### UTF-8 표현

![](../.gitbook/assets/ff0945a1-9577-4526-b668-d4b27904e037.png)

```swift
for codeUnit in dogString.utf8 {
    print("\(codeUnit) ", terminator: "")
}
print("")
// 68 111 103 226 128 188 240 159 144 182
```

UTF-16 표현

![](../.gitbook/assets/0e2c8fe1-94f3-4b1d-8059-e31b0e0670ac.png)

```swift
for codeUnit in dogString.utf16 {
    print("\(codeUnit) ", terminator: "")
}
print("")
// 68 111 103 8252 55357 56374
```

### 유니코드 스칼라 표현

![](../.gitbook/assets/5f6b5aa5-57d6-41e3-bc95-89e1f396ea1a.png)

```swift
for scalar in dogString.unicodeScalars {
    print("\(scalar.value) ", terminator: "")
}
print("")
// 68 111 103 8252 128054
```

```swift
for scalar in dogString.unicodeScalars {
    print("\(scalar) ")
}
// D
// o
// g
// !!
// 🐶
```

  


