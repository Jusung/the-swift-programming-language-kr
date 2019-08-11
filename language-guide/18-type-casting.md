# 타입캐스팅 \(Type Casting\)

타입캐스팅은 인스턴스의 타입을 확인하거나 인스턴스를 같은 계층\(hierachy\)에 있는 다른 `superclass`나 `subclass`로 취급하는 방법입니다. 타입캐스팅에는 `is`와 `as` 두 연산자를 사용합니다. 타입캐스팅을 이용하면 특정 프로토콜을 따르는지\(conforms\) 확인할 수도 있습니다.

## 타입캐스팅을 위한 클래스 계층구조 선언 \(Defining a Class Hierarchy for Type Casting\)

타입캐스팅의 동작을 확인하기 위해 클래스를 하나 만들어 보겠습니다.

```swift
class MediaItem {
    var name: String
    init(name: String) {
        self.name = name
    }
}
```

`MediaItem`이라는 클래스를 선언했는데 이 클래스를 서브클래싱 해서 두개의 다른 서브클래스를 만들어 보겠습니다.

```swift
class Movie: MediaItem {
    var director: String
    init(name: String, director: String) {
        self.director = director
        super.init(name: name)
    }
}

class Song: MediaItem {
    var artist: String
    init(name: String, artist: String) {
        self.artist = artist
        super.init(name: name)
    }
}
```

`MediaItem`클래스의 서브클래스 `Movie`와 `Song` 두개의 클래스를 선언했습니다. 마지막으로 이 `Movie`와 `Song` 두개의 클래스를 아이템으로 갖는 `library`배열을 선언합니다.

```swift
let library = [
    Movie(name: "Casablanca", director: "Michael Curtiz"),
    Song(name: "Blue Suede Shoes", artist: "Elvis Presley"),
    Movie(name: "Citizen Kane", director: "Orson Welles"),
    Song(name: "The One And Only", artist: "Chesney Hawkes"),
    Song(name: "Never Gonna Give You Up", artist: "Rick Astley")
]
// the type of "library" is inferred to be [MediaItem]
```

`library`가 갖고 있는 `Movie`,`Song`인스턴스의 공통 부모는 `MediaItem`이기 때문에 `library`는 타입 추론에 의해 `[MediaItem]` 배열의 형을 갖게 됩니다. `library`를 순회\(iterate\)하면 배열의 아이템은 `Movie`, `Song` 타입이 아니라 `MediaItem`타입이라는 것을 확인할 수 있습니다. 타입 지정을 위해서는 `downcasting`을 이용해야 합니다.

## 형 확인 \(Checking Type\)

`is`연산자를 이용해 특정 인스턴스의 타입을 확인할 수 있습니다. 아래 코드는 `library`배열을 순회하고 아이템이 특정 타입일때마다 그 숫자를 증가하는 예제 코드입니다.

```swift
var movieCount = 0
var songCount = 0

for item in library {
    if item is Movie {
        movieCount += 1
    } else if item is Song {
        songCount += 1
    }
}

print("Media library contains \(movieCount) movies and \(songCount) songs")
// "Media library contains 2 movies and 3 songs" 출력
```

## 다운캐스팅 \(Downcasting\)

특정 클래스 타입의 상수나 변수는 특정 서브클래스의 인스턴스를 참조하고 있을 수 있습니다. `as?`와 `as!`연산자를 이용해 어떤 타입의 인스턴스인지 확인할 수 있습니다. `as?`는 특정 타입이 맞는지 확신할 수 없을때 사용하고 `as!`는 특정 타입이라는 것이 확실한 경우에 사용합니다. 단 `as!`으로 다운캐스팅을 했는데 지정한 타입이 아니라면 런타임 에러가 발생합니다. 다음 예제는 `library`배열의 `mediaItem`이 `Movie` 인스턴스 일수도 있고 `Song`인스턴스일 수도 있기 때문에 다운캐스팅을 위해 `as?`연산자를 사용했습니다.

```swift
for item in library {
    if let movie = item as? Movie {
        print("Movie: \(movie.name), dir. \(movie.director)")
    } else if let song = item as? Song {
        print("Song: \(song.name), by \(song.artist)")
    }
}

// Movie: Casablanca, dir. Michael Curtiz
// Song: Blue Suede Shoes, by Elvis Presley
// Movie: Citizen Kane, dir. Orson Welles
// Song: The One And Only, by Chesney Hawkes
// Song: Never Gonna Give You Up, by Rick Astley
```

> NOTE  
> 캐스팅은 실제 인스턴스나 값을 바꾸는 것이 아니라 지정한 타입으로 취급하는 것 뿐입니다.

## Any, AnyObject의 타입 캐스팅 \(Type Casting for Any and AnyObject\)

Swift에서는 두가지 특별한 타입을 제공합니다.

* Any : 함수 타입을 포함해 모든 타입을 나타냅니다.
* AnyObject : 모든 클래스 타임의 인스턴스를 나타냅니다.

  `Any`타입의 예제입니다. `things`라는 `Any`타입 배열을 선언해 여러 타입의 값을 저장합니다. 여기에는 `Int`, `String`, 함수, 클로저까지 포함됩니다.

```swift
var things = [Any]()

things.append(0)
things.append(0.0)
things.append(42)
things.append(3.14159)
things.append("hello")
things.append((3.0, 5.0))
things.append(Movie(name: "Ghostbusters", director: "Ivan Reitman"))
things.append({ (name: String) -> String in "Hello, \(name)" })
```

`things`를 순회하며 타입캐스팅이 되는지 `switch case`문에 `as` 연산자로 확인해 타입캐스팅 되는 배열의 원소의 값을 적절히 출력합니다.

```swift
for thing in things {
    switch thing {
    case 0 as Int:
        print("zero as an Int")
    case 0 as Double:
        print("zero as a Double")
    case let someInt as Int:
        print("an integer value of \(someInt)")
    case let someDouble as Double where someDouble > 0:
        print("a positive double value of \(someDouble)")
    case is Double:
        print("some other double value that I don't want to print")
    case let someString as String:
        print("a string value of \"\(someString)\"")
    case let (x, y) as (Double, Double):
        print("an (x, y) point at \(x), \(y)")
    case let movie as Movie:
        print("a movie called \(movie.name), dir. \(movie.director)")
    case let stringConverter as (String) -> String:
        print(stringConverter("Michael"))
    default:
        print("something else")
    }
}
```

`Int`, `Double`뿐만 아니라 튜플, 함수도 `Any`타입에 포함될 수 있다는 것을 확인할 수 있습니다.

> NOTE  
> `Any` 타입은 옵셔널 타입을 포함합니다. 하지만 Swift에서는 `Any`타입을 사용해야 하는 곳에 옵셔널을 사용하면 경고를 발생 시킵니다. let optionalNumber: Int? = 3 things.append\(optionalNumber\) // 경고 things.append\(optionalNumber as Any\) // 경고 없음

