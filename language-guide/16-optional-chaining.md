# 옵셔널 체이닝 \(Optional Chaining\)

옵셔널 체이닝은 nil일 수도 있는 프로퍼티나, 메소드 그리고 서브스크립트에 질의\(query\)를하는 과정을 말합니다. 만약 옵셔널이 프로퍼티나 메소드 혹은 서브스크립트에 대한 값을 갖고 있다면 그 값을 반환하고 만약 값이 nil이면 nil을 반환 합니다. 여러 질의를 연결해서 할 수도 있는데, 연결된 질의에서 어느 하나라도 nil이면 전체 결과는 nil이 됩니다.

> NOTE  
> Swift에서 옵셔널 체이닝은 Objective-C에서의 nil 메시징과 유사합니다. 차이점은 Swift에서는 옵셔널 체이닝은 reference type뿐만 아니라 primitive타입에서 사용도 가능하고 값을 가져오는데 성공했는지 실패했는지 확인할 수 있습니다.

## 강제 언래핑의 대체로써의 옵셔널 체이닝 \(Optional Chaining as an Alternative to Forced Unwrapping\)

옵셔널 체이닝은 옵셔널 값 뒤에 물음표\(?\)를 붙여서 표현 가능합니다. 옵셔널을 사용할 수 있는 값에는 프로퍼티, 메소드 그리고 서브스크립트가 포함 됩니다. 옵셔널 값을 강제 언래핑 하기위해서 뒤에 느낌표\(!\)를 붙이는 것과 문법이 비슷한데, 가장 큰 차이는 강제 언레핑을 했는데 만약 그 값이 없으면 런타임 에러가 발생하지만, 옵셔널 체이닝을 사용하면 런타임 에러 대신 nil이 반환 된다는 것입니다.

옵셔널 체이닝에 의해 nil 값이 호출 될 수 있기 때문에 옵셔널 체이닝의 값은 항상 옵셔널 값이 됩니다. 옵셔널 값을 반환하지 않는 프로퍼티, 메소드 혹은 서브스크립트를 호출하더라도 옵셔널 체이닝에 의해 옵셔널 값이 반환 됩니다. 이 옵셔널 리턴 값을 이용해 옵셔널 체이닝이 성공적으로 실행 됐는지 아니면 nil을 반환 했는지 확인할 수 있습니다.

보다 구체적으로는 옵셔널 체이닝에 의해 호출되면 반환 값과 같은 타입에 옵셔널이 붙어 반환 됩니다. 예를 들어, Int를 반환하는 메소드 경우 옵셔널 체이닝이 성공적으로 실행되면 Int?를 반환합니다.

다음 코드는 옵셔널 체이닝이 강제 언래핑과 어떻게 다른지 설명해 줍니다.

```swift
class Person {
    var residence: Residence?
}

class Residence {
    var numberOfRooms = 1
}
```

Residence 인스턴스는 numberOfRooms이라는 Int 프로퍼티 하나를 소유하고 있고 Person 인스턴스는 옵셔널 residence 프로퍼티를 Residence?로 소유합니다. 만약 Person이라는 인스턴스를 생성하면 residence 프로퍼티는 옵셔널의 기본값인 nil로 초기화 됩니다. 아래 코드에서 john은 residence 프로퍼티가 nil인 값을 소유합니다.

`let john = Person()`

만약 이 person의 residence에 numberOfRooms프로퍼티에 접근하기 위해 느낌표를 이용해 강제 언래핑을 한다면 residence가 언래핑 할 값이 없기 때문에 런타임 에러가 발생합니다.

```swift
let roomCount = john.residence!.numberOfRooms
// this triggers a runtime error
```

numberOfRooms의 값에 접근하기 위해 강제 언래핑 대신 옵셔널 체이닝을 사용할 수 있습니다. 옵셔널 체이닝을 사용하기 위해 느낌표 대신 물음표를 사용합니다.

```swift
if let roomCount = john.residence?.numberOfRooms {
    print("John's residence has \(roomCount) room(s).")
} else {
    print("Unable to retrieve the number of rooms.")
}
// Prints "Unable to retrieve the number of rooms."
```

옵셔널 체이닝의 체인 이라는 단어처럼 이 residence 프로퍼티들은 묶여 있습니다. 그래서 위 코드에서 numberOfRooms 프로퍼티는 옵셔널이 아니지만 이 값에 접근하기 위해 사용했던 인스턴스의 프로퍼티 residence가 옵셔널이기 때문에 최종 값은 옵셔널 값이 됩니다.

nil이었던 residence 값에 Residence 인스턴스를 생성해 추가할 수 있습니다. `john.residence = Residence()`

john.residence는 이제 nil이 아니라 Residence 인스턴스를 갖습니다. 그래서 옵셔널 체이닝으로 numberOfRooms 값에 접근 했을 때 nil 대신 Int? 값인 1을 리턴합니다.

```swift
if let roomCount = john.residence?.numberOfRooms {
    print("John's residence has \(roomCount) room(s).")
} else {
    print("Unable to retrieve the number of rooms.")
}
// Prints "John's residence has 1 room(s)."
```

## 옵셔널 체이닝을 위한 모델 클래스 정의 \(Defining Model Classes for Optional Chaining\)

옵셔널 체이닝을 프로퍼티, 메소드 그리고 서브스크립트에서 사용할 수 있는데, 한 단계가 아닌 여러 level로\(multilevel optional chaining\) 사용할 수 있습니다.

아래 코드는 위의 Person, Residence 모델을 확장해 Room과 Address 클래스를 추가한 4가지 모델을 정의합니다. Person 클래스는 전과 같습니다.

```swift
class Person {
    var residence: Residence?
}
```

Residence는 전보다 더 복잡해 졌습니다. 이번에 Residence는 rooms라는 프로퍼티를 소유합니다. 이 프로퍼티는 \[Room\]타입의 빈 배열로 초기화 됩니다.

```swift
class Residence {
    var rooms = [Room]()
    var numberOfRooms: Int {
        return rooms.count
    }
    subscript(i: Int) -> Room {
        get {
            return rooms[i]
        }
        set {
            rooms[i] = newValue
        }
    }
    func printNumberOfRooms() {
        print("The number of rooms is \(numberOfRooms)")
    }
    var address: Address?
}
```

이번 버전의 Residence 가 Room인스턴스의 배열을 소유하고 있기 때문에 numberOfRooms 프로퍼티는 계산된 프로퍼티로 선언됩니다. rooms 배열을 접근하기 위한 단축\(shortcut\)으로 서브스크립트\(subscript\)를 선언했습니다.

rooms 배열의 Room 클래스는 이름 하나를 초기화 때 인자로 받는 간단한 클래스 입니다.

```swift
class Room {
    let name: String
    init(name: String) { self.name = name }
}
```

마지막 클래스는 Address입니다. 이 클래스는 3개의 String? 옵셔널 프로퍼티를 갖습니다. 처음 두개는 buildingName, buildingNumber이고, 빌딩 주소를 구분하는 대체 수단\(alternative\)입니다. 마지막 street프로퍼티는 거리의 주소를 나타내는데 사용됩니다.

```swift
class Address {
    var buildingName: String?
    var buildingNumber: String?
    var street: String?
    func buildingIdentifier() -> String? {
        if let buildingNumber = buildingNumber, let street = street {
            return "\(buildingNumber) \(street)"
        } else if buildingName != nil {
            return buildingName
        } else {
            return nil
        }
    }
}
```

Address 클래스는 buildingIdentifier\(\)라는 String?타입의 메소드를 지원합니다. 이 메소드는 buildingNumber와 street을 확인해 값이 있으면 buildingNumber와 결합된 street 값을 반환하고 값이 없는 경우 nil을 반환 합니다.

## 옵셔널 체이닝을 통한 프로퍼티의 접근 \(Accessing Properties Through Optional Chaining\)

옵셔널 체이닝을 이용해 프로퍼티에 접근 할 수 있습니다.

```swift
let john = Person()
if let roomCount = john.residence?.numberOfRooms {
    print("John's residence has \(roomCount) room(s).")
} else {
    print("Unable to retrieve the number of rooms.")
}
// Prints "Unable to retrieve the number of rooms."
```

위 코드의 경우 residence?가 nil이기 때문에 옵셔널 체이닝 결과 nil을 호출하게 됩니다. 옵셔널 체이닝을 값을 할당하는데 사용할 수도 있습니다.

```swift
let someAddress = Address()
someAddress.buildingNumber = "29"
someAddress.street = "Acacia Road"
john.residence?.address = someAddress
```

위 예제에서는 Address\(\) 인스턴스를 생성해 john.residence의 address로 할당하는 코드입니다. john.residence?가 nil이기 때문에 address 할당은 실패합니다. 할당도 할당받는 왼쪽 항이 nil이면 아예 오른쪽 항이 실행되지 않습니다. 확인을 위해 createAddress\(\) 메소드를 만들고 메소드 실행 첫줄에 "Function was called.”를 출력하도록 했습니다.

```swift
func createAddress() -> Address {
    print("Function was called.")
  let someAddress = Address()
    someAddress.buildingNumber = "29"
    someAddress.street = "Acacia Road"    

    return someAddress
}
john.residence?.address = createAddress()
```

이 코드를 `john.residence?.address = createAddress()`에서 호출하면 "Function was called.”가 출력되지 않는 것을 확인함으로써 이 메소드가 아예 실행되지 않았음을 확인할 수 있습니다.

## 옵셔널 체이닝을 통한 메소드 호출 \(Calling Methods Through Optional Chaining\)

옵셔널 체이닝을 이용해 메소드를 호출 할 수 있습니다.

```swift
func printNumberOfRooms() {
    print("The number of rooms is \(numberOfRooms)")
}
```

위 메소드는 리턴 값이 명시되지 않았습니다. 하지만 함수나 메소드는 리턴 값이 없는 경우암시적으로 Void라는 값을 갖습니다. \(참조 [Functions Without Return Values](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Functions.html#//apple_ref/doc/uid/TP40014097-CH10-ID163) . \) 그래서 이 메소드가 옵셔널 체이닝에서 호출되면 반환 값은 Void가 아니라 Void? 가 타입이 반환됩니다.

관련된 예제 코드를 살펴 보겠습니다.

```swift
if john.residence?.printNumberOfRooms() != nil {
    print("It was possible to print the number of rooms.")
} else {
    print("It was not possible to print the number of rooms.")
}
// Prints "It was not possible to print the number of rooms."
```

위 코드는 `john.residence?.printNumberOfRooms()` 메소드 호출의 결과가 nil인지 아닌지 비교를 하고 그 결과에 대한 처리를 합니다. 위에서 언급했던 것처럼 printNumberOfRooms\(\) 에는 직접적인 반환 값이 명시돼 있지 않지만 암시적으로 Void를 반환하고 이 메소드 호출이 옵셔널 체이닝에서 이루어 지기 때문에 Void? 가 반환되서 nil비교를 할 수 있습니다.

옵셔널 체이팅을 통해 값을 할당하면 값을 Void? 값을 반환합니다. 그래서 이 값이 nil인지 아닌지 비교할 수 있습니다. 아래 코드는 이와 관련한 예제 입니다.

```swift
if (john.residence?.address = someAddress) != nil {
    print("It was possible to set the address.")
} else {
    print("It was not possible to set the address.")
}
// Prints "It was not possible to set the address."
```

## 옵셔널 체이닝을 통한 서브스크립트 접근 \(Accessing Subscripts Through Optional Chaining\)

옵셔널 체이닝을 이용해 옵셔널 값을 서브스크립트로 접근할 수 있습니다.

> NOTE  
> 옵셔널 값을 서브스크립트로 접근 하기 위해서는 \[\] 괄호 전에 물음표\(?\) 기호를 붙여서 사용합니다.

아래 예제는 서브스크립트를 이용해 rooms에서 첫 rooms의 name을 요청하는 코드입니다. 현재 john.residence가 nil이기 때문에 서브스크립트 접근은 실패합니다.

```swift
if let firstRoomName = john.residence?[0].name {
    print("The first room name is \(firstRoomName).")
} else {
    print("Unable to retrieve the first room name.")
}
// Prints "Unable to retrieve the first room name."
```

옵셔널 체이닝에서 서브스크립트로 값을 가져오는 것과 유사한 형태로 값을 할당할 수 있습니다. `john.residence?[0] = Room(name: "Bathroom")` 이 코드는 첫 room 값을 할당하는 코드인데 room을 할당 하지 못하고 fail이 발생합니다. 이유는 residence가 현재 nil이기 때문입니다.

아래 코드와 같이 Residence 인스턴스를 할당하면 residence 서브스크립트를 사용할 수 있습니다.

```swift
let johnsHouse = Residence()
johnsHouse.rooms.append(Room(name: "Living Room"))
johnsHouse.rooms.append(Room(name: "Kitchen"))
john.residence = johnsHouse

 if let firstRoomName = john.residence?[0].name {
    print("The first room name is \(firstRoomName).")
} else {
    print("Unable to retrieve the first room name.")
}
// Prints "The first room name is Living Room."
```

### 옵셔널 타입의 서브스크립트 접근 \(Accessing Subscripts of Optional Type\)

만약 Swift의 사전 타입\(Dictionary Type\)같이 서브스크립트의 결과로 옵셔널을 반환 한다면 그 뒤에 물음표\(?\)를 붙여 줍니다. 사전 타입은 key-value로 동작하기 때문에 항상 그 사전에 key가 존재한다는 보장이 없기 때문에 옵셔널 값을 반환합니다.

```swift
var testScores = ["Dave": [86, 82, 84], "Bev": [79, 94, 81]]
testScores["Dave"]?[0] = 91
testScores["Bev"]?[0] += 1
testScores["Brian"]?[0] = 72
// the "Dave" array is now [91, 82, 84] and the "Bev" array is now [80, 94, 81]
```

위 예제는 testScores라는 사전에서 특정 key에 대한 값을 get 혹은 set하는 코드입니다. Dave, Bev와 관련한 값은 처리가 되고 Brian은 등록된 key가 없기 때문에 관련한 아무런 일도 처리도 일어나지 않습니다.

## 체이닝의 다중 레벨 연결 \(Linking Multiple Levels of Chaining\)

옵셔널 체이닝이 여러 단계에 걸쳐 연결 될 수 있습니다. 상위 값이 옵셔널 값인 경우 하위도 옵셔널 값이라고 해서 더 옵셔널 해지진 않습니다. 옵셔널은 옵셔널입니다.

* 옵셔널 체이닝의 상위 레벨 값이 옵셔널인 경우 현재 값이 옵셔널이 아니더라도 그 값은 옵셔널값이 됩니다.
* 옵셔널 체이닝의 상위 레벨 값이 옵셔널이고 현재 값이 옵셔널 이라고 해서 더 옵셔널하게 되진 않습니다. 
* 옵셔널 체이닝을 통해 값을 검색하거나 메소드를 호출하면 몇 단계를 거치는지 상관없이 옵셔널을 반환합니다.

예제를 보시겠습니다.

```swift
if let johnsStreet = john.residence?.address?.street {
    print("John's street name is \(johnsStreet).")
} else {
    print("Unable to retrieve the address.")
}
// Prints "Unable to retrieve the address."
```

john.residence는 valid한 Residence 인스턴스를 갖고 있지만 address가 nil이어서 이 옵셔널 체이닝은 fail이 됩니다. 비록 상위의 옵셔널 값인 john.residence가 true여도 하위 값이 nil 이면 체이닝은 fail이 됩니다.

Address 인스턴스를 생성에서 할당하고나서 다시 옵셔널 체이닝을 해보겠습니다.

```swift
let johnsAddress = Address()
johnsAddress.buildingName = "The Larches"
johnsAddress.street = "Laurel Street"
john.residence?.address = johnsAddress

if let johnsStreet = john.residence?.address?.street {
    print("John's street name is \(johnsStreet).")
} else {
    print("Unable to retrieve the address.")
}
// Prints "John's street name is Laurel Street."
```

Residence인스턴스와 Address인스턴스가 모두 존재하기 때문에 옵셔널 체이닝은 success가 됩니다.

## 체이닝에서 옵셔널 값을 반환하는 메소드 \(Chaining on Methods with Optional Return Values\)

아래 예제와 같이 옵셔널 체이닝에서 반환 값이 있는 메소드를 호출할 수 있습니다.

```swift
if let buildingIdentifier = john.residence?.address?.buildingIdentifier() {
    print("John's building identifier is \(buildingIdentifier).")
}
// Prints "John's building identifier is The Larches."
```

만약 메소드의 반환 값을 갖고 추가적인 행동을 더 하고 싶다면 메소드 호출 표현 뒤에 물음표\(?\)를 붙이고 그 행동을 적어주시면 됩니다. 앞에서 언급 했던 것 처럼 옵셔널 체이닝에 물려 있기 때문에 메소드의 반환 값도 옵셔널이 돼서 그 표시를 해줘야 합니다.

```swift
if let beginsWithThe =
    john.residence?.address?.buildingIdentifier()?.hasPrefix("The") {
    if beginsWithThe {
        print("John's building identifier begins with \"The\".")
    } else {
        print("John's building identifier does not begin with \"The\".")
}
}
// Prints "John's building identifier begins with "The"."
```

> NOTE  
> 옵셔널이 반환 값에 걸려있고 메소드 자체에 걸려 있는 것이 아니기 때문에 메소드 괄호 뒤에 “?”를 붙여 줍니다.

