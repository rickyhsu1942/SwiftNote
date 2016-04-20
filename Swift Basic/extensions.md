# Extensions 擴充
---
內容：
* [擴展語法 (Extension Syntax)](#extension_syntax)
* [計算型屬性 (Computed Properties)](#computed_properties)
* [建構器 (Initializers)](#initializers)
* [方法 (Methods)](#methods)
* [下標 (Subscripts)](#subscripts)
* [巢狀型別 (Nested Types)](#nested_types)

---

擴展就是向一個已有的類別、結構或列舉型別添加新功能（functionality）。<br>
Swift 中的擴展可以：
* 添加計算型屬性和計算靜態屬性
* 定義實例方法和型別方法
* 提供新的建構器
* 定義下標
* 定義和使用新的巢狀型別
* 使一個已有型別符合某個協定

>注意：<br>
如果你定義了一個擴展向一個已有型別添加新功能，那麼這個新功能對該型別的所有已有實例中都是可用的，即使它們是在你的這個擴展的前面定義的。

## <a name='extension_syntax'></a> 擴展語法 (Extension Syntax)

使用關鍵字`extension`
```swift
extension SomeType {
    // 加到SomeType的新功能寫到這裡
}
```
一個擴展可以擴展一個已有型別，使其能夠適配一個或多個協定（protocol）。當這種情況發生時，協定的名字應該完全按照類別或結構的名字的方式進行書寫
```swift
extension SomeType: SomeProtocol, AnotherProctocol {
    // 協定實作寫到這裡
}
```

---

## <a name='computed_properties'></a> 計算型屬性 (Computed Properties)

擴展可以向已有型別添加計算型實例屬性和計算型型別屬性。
```swift
extension Double {
    var km: Double { return self * 1_000.0 }
    var m : Double { return self }
    var cm: Double { return self / 100.0 }
    var mm: Double { return self / 1_000.0 }
    var ft: Double { return self / 3.28084 }
}
let oneInch = 25.4.mm
print("One inch is \(oneInch) meters")
// 輸出：One inch is 0.0254 meters
let threeFeet = 3.ft
print("Three feet is \(threeFeet) meters")
// 輸出：Three feet is 0.914399970739201 meters
```
這些屬性是唯讀的計算型屬性，所有從簡考慮它們不用get關鍵字表示。它們的回傳值是Double型，而且可以用於所有接受Double的數學計算中：
```swift
let aMarathon = 42.km + 195.m
print("A marathon is \(aMarathon) meters long")
// 輸出：A marathon is 42495.0 meters long
```
>注意：<br>
擴展可以添加新的計算屬性，但是不可以添加儲存屬性，也不可以向已有屬性添加屬性觀測器(property observers)。

---

## <a name='initializers'></a> 建構器 (Initializers)

擴充可以替「型別」新增建構器，但在「類別」有點不同，條件如下
* 可以替類別新增「便利建構器」
* 不可以替類別新增「指定建構器」和「解構器」

```swift
struct Size {
    var width = 0.0, height = 0.0
}
struct Point {
    var x = 0.0, y = 0.0
}
struct Rect {
    var origin = Point()
    var size = Size()
}

let defaultRect = Rect()
let memberwiseRect = Rect(origin: Point(x: 2.0, y: 2.0),
    size: Size(width: 5.0, height: 5.0))
```
```swift
extension Rect {
    init(center: Point, size: Size) {
        let originX = center.x - (size.width / 2)
        let originY = center.y - (size.height / 2)
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
}

let centerRect = Rect(center: Point(x: 4.0, y: 4.0),
    size: Size(width: 3.0, height: 3.0))
// centerRect的原點是 (2.5, 2.5)，大小是 (3.0, 3.0)
```
>注意：<br>
如果你使用擴展提供了一個新的建構器，你依舊有責任保證建構過程能夠讓所有實例完全初始化。

---

## <a name='methods'></a> 方法 (Methods)

擴展可以向已有型別添加新的實例方法和型別方法
```swift
extension Int {
    func repetitions(task: () -> ()) {
        for _ in 0..<self {
            task()
        }
    }
}
```
```swift
3.repetitions({
    print("Hello!")
})
// Hello!
// Hello!
// Hello!
```
更簡潔的呼叫閉包
```swift
3.repetitions{
    println("Goodbye!")
}
// Goodbye!
// Goodbye!
// Goodbye!
```

### 修改實例方法（Mutating Instance Methods）

通過擴展添加的實例方法也可以修改該實例本身。結構和列舉型別中修改`self`或其屬性的方法必須將該實例方法標注為`mutating`
```swift
extension Int {
    mutating func square() {
        self = self * self
    }
}
var someInt = 3
someInt.square()
// someInt 現在值是 9
```

---

## <a name='subscripts'></a> 下標 (Subscripts)

擴展可以向一個已有型別添加新下標。
```swift
extension Int {
    subscript(digitIndex: Int) -> Int {
        var decimalBase = 1
        for _ in 0..<digitIndex {
            decimalBase *= 10
        }
        return (self / decimalBase) % 10
    }
}

746381295[0]
// returns 5
746381295[1]
// returns 9
746381295[2]
// returns 2
746381295[8]
// returns 7
```

---

## <a name='nested_types'></a> 巢狀型別 (Nested Types)

擴展可以向已有的類別、結構和列舉添加新的巢狀型別
```swift
extension Character {
    enum Kind {
        case Vowel, Consonant, Other
    }
    var kind: Kind {
        switch String(self).lowercaseString {
        case "a", "e", "i", "o", "u":
            return .Vowel
        case "b", "c", "d", "f", "g", "h", "j", "k", "l", "m",
        "n", "p", "q", "r", "s", "t", "v", "w", "x", "y", "z":
            return .Consonant
        default:
            return .Other
        }
    }
}

func printLetterKinds(word: String) {
    print("'\(word)' is made up of the following kinds of letters:")
    for character in word.characters {
        switch character.kind {
        case .Vowel:
            print("vowel ")
        case .Consonant:
            print("consonant ")
        case .Other:
            print("other ")
        }
    }
    print("\n")
}
printLetterKinds("Hello")

/*
'Hello' is made up of the following kinds of letters:
consonant 
vowel 
consonant 
consonant 
vowel 
*/
```

---