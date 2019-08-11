# 초기화 해지 \(Deinitialization\)

디이니셜라이저는 초기자\(Initializer\)와 반대로 클래스 인스턴스가 소멸되기 직전에 호출됩니다. 초기자는 선언 키워드로 `init`를 사용하는데 디이니셜라이저는 선언을 위해 `deinit`키워드를 사용합니다. 디이니셜라이저는 오직 클래스 타입에서만 사용 가능합니다.

## 초기화 해지의 동작 \(How Deinitilization Works\)

일반적으로 Swift가 자원의 해제를 자동으로 알아서 해주는데, 열었던 파일을 사용이 끝나고 닫는 것 같이 사용자가 자원 해지를 위해 수동으로 작업 해야하는 경우도 있습니다. 이럴 때 사용하는 것이 디이니셜라이저입니다.디이니셜라이저는 클래스당 오직 하나만 선언할 수 있고 파라미터를 받을 수 없습니다. 디이니셜라이저의 기본적인 선언 형태는 다음과 같습니다.

```swift
deinit {
    // perform the deinitialization
}
```

디이니셜라이저는 자동으로 호출되고 수동으로 호출할 수 없습니다. Superclass의 디이니셜라이저는 Subclass에서 디이니셜라이저를 선언하지 않아도 자동으로 호출 됩니다.

## 디이니셜라이저의 사용 \(Deinitializers in Action\)

디이니셜라이저의 동작확인을 위해 예제코드를 하나 만들어 보겠습니다.

```swift
class Bank {
    static var coinsInBank = 10_000
    static func distribute(coins numberOfCoinsRequested: Int) -> Int {
        let numberOfCoinsToVend = min(numberOfCoinsRequested, coinsInBank)
        coinsInBank -= numberOfCoinsToVend
        return numberOfCoinsToVend
    }
    static func receive(coins: Int) {
        coinsInBank += coins
    }
}
```

은행 클래스를 하나 선언하고 은행이 소유한 코인수 `coinsInBank` 코인을 배포하는 `distribute`메소드와 코인을 받는 `receive` 메소드를 선언했습니다. `distribute`메소드에서는 현재 은행에 남은 코인수를 확인해 요청한 코인수와 비교해 더 작은 것을 반환합니다. 만약 은행에 남은 코인이 0이라면 요청한 코인 대신 0을 반환합니다. `receive` 메소드는 코인을 받아 은행에 추가하는 메소드입니다. 이 코인을 활용해 게임은 한다고 가정해 봅시다. 사용자는 게임을 하는데 처음에 일정 코인을 은행으로 부터 받고 시작하고, 게임에서 이길 때마다 은행에서 코인을 받아 사용자의 지갑에 저장합니다.

```swift
class Player {
    var coinsInPurse: Int
    init(coins: Int) {
        coinsInPurse = Bank.distribute(coins: coins)
    }
    func win(coins: Int) {
        coinsInPurse += Bank.distribute(coins: coins)
    }
    deinit {
        Bank.receive(coins: coinsInPurse)
    }
}
```

게임이 끝나면 은행에서 받았던 돈을 다시 은행에 돌여주기 위해 위 코드에서 `deinit`안에 `Bank.receive(coins: coinsInPurse)` 코드를 넣어 사용했던 돈은 모두 은행에 다시 반환하도록 했습니다. 자 게임을 시작해보죠.

```swift
var playerOne: Player? = Player(coins: 100)
print("A new player has joined the game with \(playerOne!.coinsInPurse) coins")
// Prints "A new player has joined the game with 100 coins"
// 사용자는 100 코인을 갖고 시작합니다.
print("There are now \(Bank.coinsInBank) coins left in the bank")
// Prints "There are now 9900 coins left in the bank"
// 현 시점에 은행은 9900의 코인을 소유하고 있습니다.
```

사용자가 게임에 이겨 2000 코인을 받게 됩니다.

```swift
playerOne!.win(coins: 2_000)
print("PlayerOne won 2000 coins & now has \(playerOne!.coinsInPurse) coins")
// Prints "PlayerOne won 2000 coins & now has 2100 coins"
// 사용자가 게임에 이겨 2000코인을 받아 처음에 갖고 있던 100 코인과 더불어 현재 총 2100 코인을 소유하게 됩니다.
print("The bank now only has \(Bank.coinsInBank) coins left")
// Prints "The bank now only has 7900 coins left"
// 사용자에게 2100 코인을 나눠준 은행에는 현재 7900 코인이 남았습니다.
```

게임이 끝났습니다.

```swift
playerOne = nil
print("PlayerOne has left the game")
// Prints "PlayerOne has left the game"
// 사용자가 게임에서 나갔습니다.
print("The bank now has \(Bank.coinsInBank) coins")
// Prints "The bank now has 10000 coins"
```

`playerOne = nil`이라는 것은 더 이상 이 인스턴스를 사용하지 않는다는 것입니다. 그래서 앞의 디이니셜라이저\(deinit\)이 실행돼 그곳에 선언한 지갑에 있는 돈을 은행에 반납하는 코드가 실행돼 은행은 다시 처음에 갖고 있던 10000 코인을 갖게 됩니다.

