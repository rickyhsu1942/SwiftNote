# Type Casting 型別檢查
---
內容：
* [定義一個類別層次作為範例](#defining_a_class_hierarchy_for_type_casting)
* [檢查型別](#checking_type)
* [向下轉型](#downcasting)
* [Any和AnyObject的型別檢查](#type_casting_for_any_and_anyobject)

---
型別檢查是一種檢查類別實例的方式，並且或者也是讓實例作為它的父類別或者子類別的一種方式。在Swift中使用`is`和`as`運算子實作。



## <a name='defining_a_class_hierarchy_for_type_casting'></a> 定義一個類別層次作為範例
```swift
class MediaItem {
    var name: String
    init(name: String) {
        self.name = name
    }
}

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

let library = [
    Movie(name: "Casablanca", director: "Michael Curtiz"),
    Song(name: "Blue Suede Shoes", artist: "Elvis Presley"),
    Movie(name: "Citizen Kane", director: "Orson Welles"),
    Song(name: "The One And Only", artist: "Chesney Hawkes"),
    Song(name: "Never Gonna Give You Up", artist: "Rick Astley")
]
// the type of "library" is inferred to be MediaItem[]
```

---

## <a name='checking_type'></a> 檢查型別

用型別檢查運算子`is`來檢查一個實例是否屬於特定子型別。若實例屬於那個子型別，型別檢查運算子回傳 `true`，否則回傳 `false`。

```swift
var movieCount = 0
var songCount = 0

for item in library {
    if item is Movie {
        ++movieCount
    } else if item is Song {
        ++songCount
    }
}

print("Media library contains \(movieCount) movies and \(songCount) songs")
// 輸出: Media library contains 2 movies and 3 songs
```

---

## <a name='downcasting'></a> 向下轉型

某型別的一個常數或變數可能在幕後實際上屬於一個子類別。你可以相信，上面就是這種情況。你可以嘗試向下轉到它的子型別，用型別檢查運算子`as`

```swift
for item in library {
    if let movie = item as? Movie {
        print("Movie: '\(movie.name)', dir. \(movie.director)")
    } else if let song = item as? Song {
        print("Song: '\(song.name)', by \(song.artist)")
    }
}

// Movie: 'Casablanca', dir. Michael Curtiz
// Song: 'Blue Suede Shoes', by Elvis Presley
// Movie: 'Citizen Kane', dir. Orson Welles
// Song: 'The One And Only', by Chesney Hawkes
// Song: 'Never Gonna Give You Up', by Rick Astley
```
嘗試將 `item` 轉為 `Movie`型別。若成功，設置一個新的臨時常數 `movie` 來儲存回傳的可選`Movie`

>注意：<br>
轉換沒有真的改變實例或它的值。潛在的根本的實例保持不變；只是簡單地把它作為它被轉換成的類別來使用。

## <a name='type_casting_for_any_and_anyobject'></a> Any和AnyObject的型別檢查

Swift提供兩種特殊型別
* AnyObject：可以代表任意class的實例
* Any：可以代表認識類型的實例，除了方法型別(function types)

### AnyObject

下面的示例定義了一個`AnyObject[]`型別的陣列並填入三個`Movie`型別的實例
```swift
let someObjects: [AnyObject] = [
    Movie(name: "2001: A Space Odyssey", director: "Stanley Kubrick"),
    Movie(name: "Moon", director: "Duncan Jones"),
    Movie(name: "Alien", director: "Ridley Scott")
]
```
```swift
for object in someObjects {
    let movie = object as! Movie
    print("Movie: '\(movie.name)', dir. \(movie.director)")
}
// Movie: '2001: A Space Odyssey', dir. Stanley Kubrick
// Movie: 'Moon', dir. Duncan Jones
// Movie: 'Alien', dir. Ridley Scott
```
或是更短的形式
```swift
for movie in someObjects as! [Movie] {
    print("Movie: '\(movie.name)', dir. \(movie.director)")
}
// Movie: '2001: A Space Odyssey', dir. Stanley Kubrick
// Movie: 'Moon', dir. Duncan Jones
// Movie: 'Alien', dir. Ridley Scott
```

### Any
使用 `Any` 型別來和混合的不同型別一起工作，包括非`class`型別。它創建了一個可以儲存`Any`型別的陣列 `things`。
```swift
var things = [Any]()

things.append(0)
things.append(0.0)
things.append(42)
things.append(3.14159)
things.append("hello")
things.append((3.0, 5.0))
things.append(Movie(name: "Ghostbusters", director: "Ivan Reitman"))
```
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
        print("a movie called '\(movie.name)', dir. \(movie.director)")
    default:
        print("something else")
    }
}

// zero as an Int
// zero as a Double
// an integer value of 42
// a positive double value of 3.14159
// a string value of "hello"
// an (x, y) point at 3.0, 5.0
// a movie called 'Ghostbusters', dir. Ivan Reitman
```
>注意：<br>
在一個switch語句的case中使用強制形式的型別檢查運算子（as, 而不是 as?）來檢查和轉換到一個明確的型別。在 switch case 語句的內容中這種檢查總是安全的。