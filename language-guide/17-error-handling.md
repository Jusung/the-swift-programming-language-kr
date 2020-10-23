# 에러 처리\(Error Handling\)

프로그램 실행시 에러가 발생하면 그 상황에 대해 적절한 처리가 필요합니다. 이 과정을 에러 처리라고 부릅니다. Swift에서는 런타임에 에러가 발생한 경우 그것의 처리를 위해 에러의 발생\(throwing\), 감지\(catching\), 증식\(propagating\), 조작\(manipulating\)을 지원하는 일급 클래스\(first-class\)를 제공합니다.

어떤 명령은 항상 완전히 실행되는 것이 보장되지 않는 경우가 있습니다. 그런 경우에 옵셔널을 사용해 에러가 발생해 값이 없다는 것을 표시할 수 있지만, 어떤 종류의 에러가 발생했는지 확인할 수는 없습니다. 이럴 때는 구제적으로 발생한 에러를 확인할 수 있어야 코드를 작성하는 사람이 각 에러의 경우에 따른 적절한 처리를 할 수 있습니다.

예를들어, 디스크에서 파일을 읽어 데이터를 처리하는 일을 한다고 할 때 이 작업이 실패할 경우의 수는 여러가지가 존재합니다. 파일이 특정 경로에 존재하지 않거나, 읽기 권한이 없거나 혹은 파일의 데이터가 식별할 수 있는 포맷으로 적절히 인코딩 되지 않은 경우 등 말이죠. 이런 종류별 에러 상황을 식별해 사용자에게 제공해 주면 프로그램 실행중 발생할 각 에러별로 사용자가 적절히 대응할 수 있도록 도울 수 있습니다.

> NOTE  
> Swift에서 에러 처리는 Cocoa의 NSError 클래스와 상호 호환되는 에러 핸들링 패턴을 사용합니다.

## 에러의 표시와 발생\(Representing and Throwing Errors\)

Swift에서 에러는 Error 프로토콜을 따르는 타입의 값으로 표현됩니다. 비어있는\(Empty\) 이 프로토콜은 프로토콜을 따르는 타입이 에러 처리를 위해 사용 될 수 있다는 것을 가르킵니다.

Swift의 열거형은 특별히 이런 관련된 에러를 그룹화\(Grouping\)하고 추가적인 정보를 제공하기에 적합합니다. 예를들어, 게임 안에서 판매기기 동작의에러 상황을 다음과 같이 표현할 수 있습니다.

```swift
enum VendingMachineError: Error {
     case invalidSelection
     case insufficientFunds(coinsNeeded: Int)
     case outOfStock
}
```

에러를 발생시킴으로써 무언가 기대하지 않았던 동작이 발생했고 작업을 계속 수행할 수 없다는 것을 알려줄 수 있습니다. 에러를 발생시키기 위해 throw 구문을 사용할 수 있습니다. 예를들어, 다음 코드는 판매 기기에서 5개의 코인이 더 필요하다는 에러를 발생시킵니다.

```swift
throw VendingMachineError.insufficientFunds(coinsNeeded: 5)
```

## 에러 처리\(Handling Errors\)

에러가 발생하면 특정 코드영역이 해당 에러를 처리하도록 해야합니다. 예를들어, 문제를 해결하거나, 혹은 우회할 수 있는 방법을 시도하거나 아니면 사용자에게 실패 상황을 알리는 것이 에러 처리의 방법이 될 수 있습니다.

Swift에서 4가지 에러를 처리 방법이 있습니다. 첫째로 에러가 발생한 함수에서 리턴값으로 에러를 반환해 해당 함수를 호출한 코드에서 에러를 처리하도록 하는 방법, 두번째로 do-catch 구문을 사용하는 방법, 세째로 옵셔널 값을 반환하는 방법, 마지막으로 assert를 사용해 강제로 크래쉬를 발생시키는 방법입니다. 각각의 방법에 대한 설명은 이 후 섹션에 설명돼 있습니다.

> Note  
> Swift에서의 에러 처리는 다른 언어의 exception 처리와 닮았습니다. try-catch와 throw 키워드를 사용합니다. Objective-C를 포함해 다른 언어의 exception 처리와 다른 점은 Swift에서 에러 처리는 많은 계산이 필요할 수 있는 콜스택\(call stack\) 되돌리기\(unwinding\)와 관련이 없다는 것 입니다. 그렇기 때문에 에러를 반환하는 throw 구문은 일반적인 반환 구문인 return 구문과 비슷한 성능을 보여줍니다.

### 에러를 발생시키는 함수 사용하기\(Propagating Errors Using Throwing Fuctions\)

어떤 함수, 메소드 혹은 초기자가 에러를 발생 시킬 수 있다는 것을 알리기 위해서 throw 키워드를 함수 선언부의 파라미터 뒤에 붙일 수 있습니다. throw 키워드로 표시된 함수를 throwing function이라고 부릅니다. 만약 함수가 리턴 값을 명시했다면 throw 키워드는 리턴 값 표시 기호인 -&gt; 전에 적습니다.

```swift
func canThrowErrors() throws -> String

func cannotThrowErrors() -> String
```

throwing function은 함수 내부에서 에러를\(throw\)를 만들어 함수가 호출된 곳에 전달합니다.

> Note  
> 오직 throwing function만이 에러를 발생시킬 수 있습니다. 만약 throwing function이 아닌 함수에서 throw가 발생한다면 반드시 그 함수내에서 throw에 대해 처리돼야 합니다. 아래 예제에서 VendingMachine 클래스는 요청한 아이템이 이용가능하지 않거나, 재고가 없거나, 현재 계좌를 초과하는 비용이 발생했을 때를 구분해 적절한 에러를 발생시키는 VendingMachineError throw를 발생시키는 vend\(itemNamed:\) 메소드를 갖고 있습니다.

```swift
struct Item {
    var price: Int
    var count: Int
}

class VendingMachine {
    var inventory = [
        "Candy Bar": Item(price: 12, count: 7),
        "Chips": Item(price: 10, count: 4),
        "Pretzels": Item(price: 7, count: 11)
    ]
    var coinsDeposited = 0

    func vend(itemNamed name: String) throws {
        guard let item = inventory[name] else {
            throw VendingMachineError.invalidSelection
        }

        guard item.count > 0 else {
            throw VendingMachineError.outOfStock
        }

        guard item.price <= coinsDeposited else {
            throw VendingMachineError.insufficientFunds(coinsNeeded: item.price - coinsDeposited)
        }

        coinsDeposited -= item.price

        var newItem = item
        newItem.count -= 1
        inventory[name] = newItem

        print("Dispensing \(name)")
    }
}
```

vend\(itemNamed:\) 메소드의 구현에서 guard 구문을 사용해 snack을 구매하는 과정에서 에러가 발생하면 함수에서 에러를 발생시키고 빠르게 함수를 탈출할 수 있도록 합니다\(early exit\). vend\(itemNamed:\) 메소드는 에러를 발생시키기 때문에 이 메소드를 호출하는 메소드는 반드시 do-catch, try?, try! 등의 구문을 사용해 에러를 처리해야 합니다. 예를 들어, 아래 예제의 buyFavoriteSnack\(person:vendingMachine:\) 또한 에러를 발생시키는 함수인데 vend\(itemNamed:\) 메소드에서 발생한 에러는 buyFavoriteSnack\(person:vendingMachine:\) 함수가 실행되는 곳에까지 전해집니다.

```swift
let favoriteSnacks = [
    "Alice": "Chips",
    "Bob": "Licorice",
    "Eve": "Pretzels",
]
func buyFavoriteSnack(person: String, vendingMachine: VendingMachine) throws {
     let snackName = favoriteSnacks[person] ?? "Candy Bar"
     try vendingMachine.vend(itemNamed: snackName)
}
```

이 예제에서는 buyFavoriteSnack\(person:vendingMachine:\) 함수가 주어진 사람의 가장 좋아하는 스낵이 뭔지 확인하고 그것을 vend\(itemNamed:\) 메소드를 호출해 구매 시도를 합니다. 왜냐하면 vend\(itemNamed:\) 메소드는 에러를 발생 시킬 수 있기 때문에 메소드 호출 앞에 try 키워드를 사용합니다. 에러 발생 초기자는 throwing function과 같은 방법으로 에러를 발생시킬 수 있습니다. 예를 들어, 아래 예제의 PurchasedSnack 구조체의 초기자는 초기화 단계의 일부분으로써 에러를 발생시킬 수 있는 함수입니다. 그리고 초기자가 실행될 때 발생한 에러는 이 초기자를 호출한 곳에 전달 됩니다.

```swift
struct PurchasedSnack {
    let name: String
    init(name: String, vendingMachine: VendingMachine) throws {
        try vendingMachine.vend(itemNamed: name)
        self.name = name
    }
}
```

### Do-Catch를 이용해 에러를 처리하기\(Handling Error Using Do-Catch\)

do-catch를 이용해 에러를 처리하는 코드 블럭을 작성할 수 있 수 있습니다. 만약 에러가 do 구문 안에서 발생한다면 발생하는 에러의 종류를 catch 구문으로 구분해 처리할 수 있습니다. 다음은 do-catch 구문의 일반적인 형태입니다.

```swift
do {
    try expression
    statements
} catch pattern 1 {
    statements
} catch pattern 2 where condition {
    statements
} catch {
    statements
}
```

catch 구문 뒤에 어떤 에러인지 적고 그것을 어떻게 처리할지 명시할 수 있고 만약 catch 구문 뒤에 에러 종류를 명시하지 않으면 발생하는 모든 에러를 지역 상수인 error로 바인딩 합니다. 보다 자세한 정보는 패턴 매칭을 참조하세요. 예를 들어, 다음 코드는 VendingMachineError 열거형의 모든 세 가지 에러 종류에 대해 처리하는 코드입니다.

```swift
var vendingMachine = VendingMachine()
vendingMachine.coinsDeposited = 8
do {
    try buyFavoriteSnack(person: "Alice", vendingMachine: vendingMachine)
    print("Success! Yum.")
} catch VendingMachineError.invalidSelection {
    print("Invalid Selection.")
} catch VendingMachineError.outOfStock {
    print("Out of Stock.")
} catch VendingMachineError.insufficientFunds(let coinsNeeded) {
    print("Insufficient funds. Please insert an additional \(coinsNeeded) coins.")
} catch {
    print("Unexpected error: \(error).")
}
// Prints "Insufficient funds. Please insert an additional 2 coins."
```

위 예제에서 buyFavoriteSnack\(person:vendingMachine:\) 함수는 try 표현 안에서 호출됩니다. 왜냐하면 이 함수가 에러를 발생시킬 수 있기 때문에 에러가 발생하자마자 catch 구문에 전달해 적절한 처리를 할 수 있게 하기위해서 입니다. 만약 발생한 에러 종류를 처리하는 catch 구문이 없다면 에러는 마지막 catch 구문에 걸리게 되서 지역 에러 상수인 error로 처리할 수 있습니다. 만약 아무런 에러도 발생하지 않는다면 do 구문이 실행됩니다. catch 구문에서 발생 가능한 모든 에러에 대해 반드시 종류 별로 처리할 필요는 없습니다. 만약 에러를 처리하는 적절한 catch 구문이 없다면 그 코드에 둘러 쌓인 곳에 에러가 발생합니다. 하지만 발생 되는 에러는 반드시 관련된 특정 코드 영역에서 처리돼야 합니다. 에러를 발생 시키지 않는 함수에서는 관련된 do-catch 구문에서 그 에러를 반드시 처리해야 하고, 에러를 발생 시키는 함수에서는 에러를 do-catch 구문에서 처리하거나 함수를 호출한 곳에서 반드시 에러를 처리해야 합니다. 만약 에러가 발생한 곳에서 에러에 대해 아무런 처리도 하지 않으면 런타임 에러가 발생하게 됩니다. 예를 들어, 위 코드는 모든 VendingMachineError에 대해 기술하는 것 대신 다음과 같이 처리 할 수 있습니다.

```swift
func nourish(with item: String) throws {
    do {
        try vendingMachine.vend(itemNamed: item)
    } catch is VendingMachineError {    // 모든 VendingMachineError 구분을 위해 is를 사용
        print("Invalid selection, out of stock, or not enough money.")
    }
}

do {
    try nourish(with: "Beet-Flavored Chips")
} catch {
    print("Unexpected non-vending-machine-related error: \(error)")
      // 여기에서 처럼 catch를 그냥 if-else에서 else 같이 사용 가능
}
// Prints "Invalid selection, out of stock, or not enough money."
```

nourish\(with:\) 함수에서 만약 vend\(itemNamed:\) 초기자에서 VendingMachineError 열거형 중 한가지의 에러가 발생한 경우, nourish\(with:\) 함수가 에러를 처리해 메시지를 출력합니다. 반면 nourish\(with:\) 함수는 그것을 호출한 곳에 에러를 발생 시킵니다. 발생한 에러는 그리고 나서 일반적인 catch 구문에 의해 처리 됩니다.

### 에러를 옵셔널 값으로 변환하기 \(Converting Errors to Optional Values\)

try? 구문을 사용해 에러를 옵셔널 값으로 변환할 수 있습니다. 만약 에러가 try? 표현 내에서 발생한다면, 그 표현의 값은 nil이 됩니다. 예를들어 다음 코드의 x와 y는 같은 값을 갖습니다.

```swift
func someThrowingFunction() throws -> Int {
    // ...
}

let x = try? someThrowingFunction()

let y: Int?
do {
    y = try someThrowingFunction()
} catch {
    y = nil
}
```

만약 someThrowingFunction\(\) 이 에러를 발생시키면 x와 y는 nil이 됩니다. 그렇지 않으면 x와 y는 함수의 리턴 값을 갖습니다. x와 y는 someThrowingFunction\(\)의 타입이 어떤 것이든 상관없이 옵셔널이 됩니다. 이 함수에서는 integer를 리턴하기 때문에 x와 y는 옵셔널 integer입니다. try?는 만약 발생하는 모든 에러를 같은 방법으로 처리하고 싶을 때 사용합니다. 예를 들어, 다음 코드는 데이터를 가져오는 여러 접근 방법을 시도하는데 접근 방법이 모두 실패하면 nil을 반환 합니다.

```swift
func fetchData() -> Data? {
    if let data = try? fetchDataFromDisk() { return data }
    if let data = try? fetchDataFromServer() { return data }
    return nil
}
```

### 에러 발생을 중지하기 \(Disabling Error Propagation\)

함수나 메소드에서 에러가 발생되지 않을 것이라고 확신하는 경우 try!를 사용할 수 있습니다. 혹은 runtime assertion을 사용해 에러가 발생하지 않도록 할 수 있습니다. 하지만 만약 에러가 발생하면 런타임 에러가 발생하게 됩니다. 예를 들어, 다음 코드는 loadImage\(atPath:\) 함수를 사용해 주어진 경로에서 이미지 리소스를 불러오거나 이미지를 불러오는데 실패한 경우 에러를 발생 시킵니다. 이 경우에는 앱이 배포될때 이미지가 포함되 배포되기 때문에 런타임에는 아무 에러도 발생되지 않을 것이라 확신할 수 있어 try!를 사용하는 것이 적절합니다. `let photo = try! loadImage(atPath: "./Resources/John Appleseed.jpg")`

## 정리 액션 기술 \(Specifying Cleanup Actions\)

defer 구문을 이용해 함수가 종료 된 후 파일 스트림을 닫거나, 사용했던 자원을 해지 하는 등의 일을 할 수 있습니다. defer가 여러개가 있는 경우 가장 마지막 줄부터 실행 됩니다. 즉 bottom-up 순으로 실행 됩니다.

```swift
func processFile(filename: String) throws {
    if exists(filename) {
        let file = open(filename)
        defer {
            close(file) // block이 끝나기 직전에 실행, 주로 자원 해제나 정지에 사용
        }
        while let line = try file.readline() {
            // Work with the file.
        }
        // close(file) is called here, at the end of the scope.
    }
}
```

위 예제는 defer 구문을 이용해 open\(_:\) 함수와 짝을 이루는 close\(_:\) 함수를 실행한다.

> Note  
> defer 구문을 에러 처리 이외의 경우에도 사용할 수 있습니다.

