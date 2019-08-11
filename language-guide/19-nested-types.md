# 중첩 타입 \(Nested Types\)

열거형은 특정 구조체나 클래스의 기능을 처리하기 위해 자주 사용됩니다. 이와 비슷하게 특정 문맥에서 좀 더 복잡한 타입을 위해 사용할 수 있는 유틸리티 클래스나 구조체를 정의할 수 있습니다. Swift에서는 이 기능을 위해 중첩 타입\(nested types\)을 지원합니다. 열거형, 클래스, 구조체를 그 타입 안에서 다시 정의할 수 있습니다.

## 중첩 타입의 사용

다음 코드는 블랙잭 게임에서 사용되는 카드를 모델링한 BlackjackCard라는 구조체를 정의한 \(예\)입니다. BlackjackCard 구조체는 Suit과 Rank라고 부르는 두개의 중첩 열거 타입을 포함 합니다.

블랙잭에서는 Ace 카드는 1이나 11로 사용될 수 있습니다. 이 기능은 Rank 열겨형의 중첩 타입인 구조체의 Values라고 하는 프로퍼티로 표현돼 있습니다.

```swift
struct BlackjackCard {

     // nested Suit enumeration
       // struct 안에 enum이 들어갈 수 있습니다.
     enum Suit: Character {
         case spades = "♠", hearts = "♡", diamonds = "♢", clubs = "♣"
     }

     // nested Rank enumeration
     enum Rank: Int {
         case two = 2, three, four, five, six, seven, eight, nine, ten
         case jack, queen, king, ace
         struct Values { // enum안에 struct가 들어가는 것도 가능합니다.
             let first: Int, second: Int?
         }
         var values: Values {
             switch self {
             case .ace:
                 return Values(first: 1, second: 11)
             case .jack, .queen, .king:
                 return Values(first: 10, second: nil)
             default:
                 return Values(first: self.rawValue, second: nil)
             }
         }
     }

     // BlackjackCard properties and methods
     let rank: Rank, suit: Suit
     var description: String {
         var output = "suit is \(suit.rawValue),"
         output += " value is \(rank.values.first)"
         if let second = rank.values.second {
             output += " or \(second)"
         }
         return output
     }
 }
```

Suit 열거 값은 카드에서 사용하는 4가지 모양을 표현 합니다. raw값은 그 모양의 기호를 나타냅니다. Rank 열거 값은 카드에서 사용 가능한 13가지 카드 등급을 표현 합니다. raw값은 그 등급의 값을 나타냅니다. \(이 raw Int값은 Jack, Queen, King 그리고 Ace 카드에서는 사용되지 않습니다.\)

위에서 언급했던 것 같이, Rank 값은 그 값 자체의 중첩 구조체인 Values라는 값을 정의합니다. 이 구조는 대부분의 카드가 하나의 값만 갖지만 Ace 카드는 두개를 갖는데 이것을 캡슐화 해줍니다. Values 구조체는 이것을 표현하기 위해

* first : Int 타입
* second : Int? \(옵셔널 Int\)

  를 정의합니다.

Rank는 또 계산된 프로퍼티\(computed property\)를 정의해 사용합니다. ace, jack, queen, king등 first와 second 값을 모두 갖는 카드는 그것에 맞게 값을 반환하고 나머지 보통 숫자에 대해서는 first값만 존재하고 second값은 nil인 값을 반환합니다.

BlackjackCard는 rank와 suit 두개의 프로퍼티를 소유하고 description이라고 부르는 계산된 프로퍼티도 정의합니다. BlackjackCard는 커스텀 초기자\(custom initializer\)가 없는 구조체이기 때문에 암시적인 맴버쪽 초기자\(implicit member wise initializer\)를 갖습니다. 그래서 아래 코드와 같이 rank와 suit를 인자로 받아 BlackjackCard를 초기화 할 수 있습니다.

```swift
let theAceOfSpades = BlackjackCard(rank: .ace, suit: .spades)
print("theAceOfSpades: \(theAceOfSpades.description)")
// Prints "theAceOfSpades: suit is ♠, value is 1 or 11"
```

.ace, .spades가 BlackjackCard안에 중첩 타입으로 선언돼 있기 때문에 타입 추론이 가능 하기 때문에 BlackjackCard 초기화시 타입형의 전체 명시 없이 .ace, .spades 로 사용할 수 있습니다.

## 중첩 타입의 언급 \(Referring to Nested Types\)

중첩 타입을 선언 밖에서 사용하려면 선언된 곳의 시작부터 끝까지 적어줘야 합니다.

```swift
let heartsSymbol = BlackjackCard.Suit.hearts.rawValue
// heartsSymbol is "♡"
```

