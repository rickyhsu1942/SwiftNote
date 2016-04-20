# Protocols 協定
---
內容：
* [協定的語法 (Protocol Syntax)](#protocol_syntax)
* [屬性要求 (Property Requirements)](#property_requirements)
* [方法要求 (Method Requirements)](#method_requirements)
* [突變方法要求（Mutating Method Requirements）](#mutating_method_requirements)
* [協定型別 (Protocols as Types)](#protocols_as_types)
* [委托(代理)模式 (Delegation)](#delegation)
* [在擴展中添加協定成員 (Adding Protocol Conformance with an Extension)](#adding_protocol_conformance_with_an_extension)
* [通過擴展補充協定宣告 (Declaring Protocol Adoption with an Extension)](#declaring_protocol_adoption_with_an_extension)
* [協定的繼承 (Protocol Inheritance)](#protocol_inheritance)
* [協定合成 (Protocol Composition)](#protocol_composition)
* [檢驗協定的一致性 (Checking for Protocol Conformance)](#checking_for_protocol_conformance)
* [可選協定要求 (Optional Protocol Requirements)](#optional_protocol_requirements)

---

Protocol(協定)用於統一方法和屬性的名稱，而不實作任何功能。

## <a name='protocol_syntax'></a> 協定的語法 (Protocol Syntax)

協定的定義與類別，結構，列舉的定義非常相似
```swift
protocol SomeProtocol {
    // 協定內容
}
```
在類別，結構，列舉的名稱後加上協定名稱，中間以冒號`:`分隔即可實作協定；實作多個協定時，各協定之間用逗號`,`分隔
```swift
struct SomeStructure: FirstProtocol, AnotherProtocol {
    // 結構內容
}
```
當某個類別含有父類別的同時並實作了協定，應當把父類別放在所有的協定之前
```swift
class SomeClass: SomeSuperClass, FirstProtocol, AnotherProtocol {
    // 類別的內容
}
```

---

## <a name='property_requirements'></a> 屬性要求 (Property Requirements)

協定能夠要求其遵循者必須含有一些特定名稱和型別的實例屬性(instance property)或類別屬性 (type property)，也能夠要求屬性具有(設置權限)settable 和(存取權限)gettable<br><br>
但它不要求屬性是儲存型屬性(stored property)還是計算型屬性(calculate property)

```swift
protocol SomeProtocol {
    var musBeSettable : Int { get set }
    var doesNotNeedToBeSettable: Int { get }
}
```
用類別來實作協定時，使用`class`關鍵字來表示該屬性為類別成員；用結構或列舉實作協定時，則使用`static`關鍵字來表示
```swift
protocol AnotherProtocol {
    class var someTypeProperty: Int { get set }
}
```
```swift
protocol FullyNamed {
    var fullName: String { get }
}

struct Person: FullyNamed{
    var fullName: String
}
let john = Person(fullName: "John Appleseed")
//john.fullName 為 "John Appleseed"

class Starship: FullyNamed {
    var prefix: String?
    var name: String
    init(name: String, prefix: String? = nil ) {
        self.name = name
        self.prefix = prefix
    }
    var fullName: String {
        return (prefix != nil ? prefix! + " " : "") + name
    }
}
var ncc1701 = Starship(name: "Enterprise", prefix: "USS")
// ncc1701.fullName == "USS Enterprise"
```

---

## <a name='method_requirements'></a> 方法要求 (Method Requirements)

協定能夠要求其遵循者必備某些特定的實例方法和類別方法。協定方法的宣告與普通方法宣告相似，但它不需要方法內容。

```swift
protocol SomeProtocol {
    static func someTypeMethod()
}
```
一個叫做線性同余生成器(linear congruential generator)的偽隨機數算法。
```swift
protocol RandomNumberGenerator {
    func random() -> Double
}

class LinearCongruentialGenerator: RandomNumberGenerator {
    var lastRandom = 42.0
    let m = 139968.0
    let a = 3877.0
    let c = 29573.0
    func random() -> Double {
        lastRandom = ((lastRandom * a + c) % m)
        return lastRandom / m
    }
}
let generator = LinearCongruentialGenerator()
print("Here's a random number: \(generator.random())")
// 輸出 : "Here's a random number: 0.37464991998171"
print("And another one: \(generator.random())")
// 輸出 : "And another one: 0.729023776863283"
```
---

## <a name='mutating_method_requirements'></a> 突變方法要求（Mutating Method Requirements）

能在方法或函式內部改變實例型別的方法稱為突變方法。
```swift
protocol Togglable {
    mutating func toggle()
}

enum OnOffSwitch: Togglable {
    case Off, On
    mutating func toggle() {
        switch self {
        case Off:
            self = On
        case On:
            self = Off
        }
    }
}
var lightSwitch = OnOffSwitch.Off
lightSwitch.toggle()
// lightSwitch 現在的值為 .On
```

---

## <a name='protocols_as_types'></a> 協定型別 (Protocols as Types)

協定本身不實作任何功能，但你可以將它當做型別來使用。
<br>
使用場景：
* 作為函式，方法或建構器中的參數型別，回傳值型別
* 作為常數，變數，屬性的型別
* 作為陣列，字典或其他容器中的元素型別

>注意：<br>
注意： 協定型別應與其他型別(Int，Double，String)的寫法相同，使用駝峰式

```swift
protocol RandomNumberGenerator {
    func random() -> Double
}

class LinearCongruentialGenerator: RandomNumberGenerator {
    var lastRandom = 42.0
    let m = 139968.0
    let a = 3877.0
    let c = 29573.0
    func random() -> Double {
        lastRandom = ((lastRandom * a + c) % m)
        return lastRandom / m
    }
}

class Dice {
    let sides: Int
    let generator: RandomNumberGenerator
    init(sides: Int, generator: RandomNumberGenerator) {
        self.sides = sides
        self.generator = generator
    }
    func roll() -> Int {
        return Int(generator.random() * Double(sides)) + 1
    }
}

var d6 = Dice(sides: 6,generator: LinearCongruentialGenerator())
for _ in 1...5 {
    print("Random dice roll is \(d6.roll())")
}
//輸出結果
//Random dice roll is 3
//Random dice roll is 5
//Random dice roll is 4
//Random dice roll is 5
//Random dice roll is 4
```

---

## <a name='delegation'></a> 委托(代理)模式 (Delegation)

委托是一種設計模式，它允許類別或結構將一些需要它們負責的功能交由(委托)給其他的型別。
<br><br>
委托模式的實作很簡單：定義協定來封裝那些需要被委托的函式和方法， 使其遵循者擁有這些被委托的函式和方法。

```swift
protocol DiceGame {
    var dice: Dice { get }
    func play()
}

protocol DiceGameDelegate {
    func gameDidStart(game: DiceGame)
    func game(game: DiceGame, didStartNewTurnWithDiceRoll diceRoll:Int)
    func gameDidEnd(game: DiceGame)
}
```
DiceGame協定可以在任意含有骰子的遊戲中實作，DiceGameDelegate協定可以用來追蹤DiceGame的遊戲過程。

```swift
class SnakesAndLadders: DiceGame {
    let finalSquare = 25
    let dice = Dice(sides: 6, generator: LinearCongruentialGenerator())
    var square = 0
    var board: [Int]
    init() {
        board = [Int](count: finalSquare + 1, repeatedValue: 0)
        board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
        board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
    }
    var delegate: DiceGameDelegate?
    func play() {
        square = 0
        delegate?.gameDidStart(self)
        gameLoop: while square != finalSquare {
            let diceRoll = dice.roll()
            delegate?.game(self,didStartNewTurnWithDiceRoll: diceRoll)
            switch square + diceRoll {
            case finalSquare:
                break gameLoop
            case let newSquare where newSquare > finalSquare:
                continue gameLoop
            default:
                square += diceRoll
                square += board[square]
            }
        }
        delegate?.gameDidEnd(self)
    }
}
```
因為`delegate`是一個遵循`DiceGameDelegate`的可選屬性，因此在`play()`方法中使用了可選鏈來呼叫委托方法。 若`delegate`屬性為`nil`，則委托呼叫優雅地失效。若`delegate`不為`nil`，則委托方法被呼叫
<br><br>
新增`DiceGameTracker`遵循了`DiceGameDelegate`協定
```swift
class DiceGameTracker: DiceGameDelegate {
    var numberOfTurns = 0
    func gameDidStart(game: DiceGame) {
        numberOfTurns = 0
        if game is SnakesAndLadders {
            print("Started a new game of Snakes and Ladders")
        }
        print("The game is using a \(game.dice.sides)-sided dice")
    }
    func game(game: DiceGame, didStartNewTurnWithDiceRoll diceRoll: Int) {
        ++numberOfTurns
        print("Rolled a \(diceRoll)")
    }
    func gameDidEnd(game: DiceGame) {
        print("The game lasted for \(numberOfTurns) turns")
    }
}
```
`DiceGameTracker`的執行情況
```swift
let tracker = DiceGameTracker()
let game = SnakesAndLadders()
game.delegate = tracker
game.play()
// Started a new game of Snakes and Ladders
// The game is using a 6-sided dice
// Rolled a 3
// Rolled a 5
// Rolled a 4
// Rolled a 5
// The game lasted for 4 turns
```

---

## <a name='adding_protocol_conformance_with_an_extension'></a> 在擴展中添加協定成員（Adding Protocol Conformance with an Extension）

即便無法修改源程式碼，依然可以通過擴展(Extension)來擴充已存在型別。

>注意:<br>
通過擴展為已存在的型別遵循協定時，該型別的所有實例也會隨之添加協定中的方法

```swift
protocol TextRepresentable {
    func asText() -> String
}
```
通過擴展為上一節中提到的`Dice`類別遵循`TextRepresentable`協定
```swift
extension Dice: TextRepresentable {
    func asText() -> String {
        return "A \(sides)-sided dice"
    }
}
```
從現在起，`Dice`型別的實例可被當作`TextRepresentable`型別
```swift
let d12 = Dice(sides: 12,generator: LinearCongruentialGenerator())
print(d12.asText())
// 輸出: A 12-sided dice
```
`SnakesAndLadders`類別也可以通過擴展的方式來遵循協定
```swift
extension SnakesAndLadders: TextRepresentable {
    func asText() -> String {
        return "A game of Snakes and Ladders with \(finalSquare) squares"
    }
}
print(game.asText())
// 輸出: A game of Snakes and Ladders with 25 squares
```

---

## <a name='declaring_protocol_adoption_with_an_extension'></a> 通過擴展補充協定宣告 (Declaring Protocol Adoption with an Extension)

當一個型別已經實作了協定中的所有要求，卻沒有宣告時，可以通過擴展來補充協定宣告
```swift
protocol TextRepresentable {
    func asText() -> String
}

struct Hamster {
    var name: String
    func asText() -> String {
        return "A hamster named \(name)"
    }
}
extension Hamster: TextRepresentable {}

let simonTheHamster = Hamster(name: "Simon")
let somethingTextRepresentable: TextRepresentable = simonTheHamster
print(somethingTextRepresentable.asText())
// 輸出: A hamster named Simon
```
> 注意：即時滿足了協定的所有要求，型別也不會自動轉變，因此你必須為它做出明顯的協定宣告

---

## <a name='collections_of_protocol_types'></a> 集合中的協定型別 (Collections of Protocol Types)

協定型別可以被集合使用，表示集合中的元素均為協定型別
```swift
let things: [TextRepresentable] = [game, d12, simonTheHamster]
```
`things`陣列可以被直接遍歷，並呼叫其中元素的asText()函式(`thing`被當做是`TextRepresentable`型別)
```swift
for thing in things {
    print(thing.asText())
}
// A game of Snakes and Ladders with 25 squares
// A 12-sided dice
// A hamster named Simon
```

---

## <a name='protocol_inheritance'></a> 協定的繼承 (Protocol Inheritance)

協定能夠繼承一到多個其他協定。語法與類別的繼承相似，多個協定間用逗號`,`分隔
```swift
protocol InheritingProtocol: SomeProtocol, AnotherProtocol {
    // 協定定義
}
```
`PrettyTextRepresentable`協定繼承了`TextRepresentable`協定
```swift
protocol PrettyTextRepresentable: TextRepresentable {
    func asPrettyText() -> String
}
```
遵循`PrettyTextRepresentable`協定的同時，也需要遵循`TextRepresentable`協定。
```swift
extension SnakesAndLadders: PrettyTextRepresentable {
    func asPrettyText() -> String {
        var output = asText() + ":\n"
        for index in 1...finalSquare {
            switch board[index] {
            case let ladder where ladder > 0:
                output += "▲ "
            case let snake where snake < 0:
                output += "▼ "
            default:
                output += "○ "
            }
        }
        return output
    }
}
```
* 當從陣列中迭代出的元素的值大於0時，用▲表示
* 當從陣列中迭代出的元素的值小於0時，用▼表示
* 當從陣列中迭代出的元素的值等於0時，用○表示

```swift
print(game.asPrettyText())
// A game of Snakes and Ladders with 25 squares:
// ○ ○ ▲ ○ ○ ▲ ○ ○ ▲ ▲ ○ ○ ○ ▼ ○ ○ ○ ○ ▼ ○ ○ ▼ ○ ▼ ○
```

---

## <a name='protocol_composition'></a> 協定合成 (Protocol Composition)

一個協定可由多個協定采用`protocol<SomeProtocol, AnotherProtocol>`這樣的格式進行組合，稱為協定合成(protocol composition)。

```swift
protocol Named {
    var name: String { get }
}
protocol Aged {
    var age: Int { get }
}
struct Person: Named, Aged {
    var name: String
    var age: Int
}
func wishHappyBirthday(celebrator: protocol<Named, Aged>) {
    print("Happy birthday \(celebrator.name) - you're \(celebrator.age)!")
}
let birthdayPerson = Person(name: "Malcolm", age: 21)
wishHappyBirthday(birthdayPerson)
// 輸出: Happy birthday Malcolm - you're 21!
```

---

## <a name='checking_for_protocol_conformance'></a> 檢驗協定的一致性 (Checking for Protocol Conformance)

使用`is`檢驗協定一致性，使用`as`將協定型別向下轉換(downcast)為的其他協定型別。
* `is`運算子用來檢查實例是否遵循了某個協定。
* `as?`回傳一個可選值，當實例遵循協定時，回傳該協定型別；否則回傳nil
* `as`用以強制向下轉換型。

```swift
protocol HasArea {
    var area: Double { get }
}
```
```swift
class Circle: HasArea {
    let pi = 3.1415927
    var radius: Double
    var area: Double { return pi * radius * radius }
    init(radius: Double) { self.radius = radius }
}
class Country: HasArea {
    var area: Double
    init(area: Double) { self.area = area }
}
```
`Circle`和`Country`都遵循了`HasArea`協定，前者把`area`寫為計算型屬性，後者則把`area`寫為儲存型屬性。
<br><br>
建立`Animal`類別沒有實作任何協定，並采用`AnyObject`型別的陣列來裝載在它們的實例
```swift
class Animal {
    var legs: Int
    init(legs: Int) { self.legs = legs }
}

let objects: [AnyObject] = [
    Circle(radius: 2.0),
    Country(area: 243_610),
    Animal(legs: 4)
]
```
```swift
for object in objects {
    if let objectWithArea = object as? HasArea {
        print("Area is \(objectWithArea.area)")
    } else {
        print("Something that doesn't have an area")
    }
}
// Area is 12.5663708
// Area is 243610.0
// Something that doesn't have an area
```

---

## <a name='optional_protocol_requirements'></a> 可選協定要求 (Optional Protocol Requirements)

可選協定含有可選成員，其遵循者可以選擇是否實作這些成員。在協定中使用`@optional`關鍵字作為前綴來定義可選成員。

>注意： 可選協定只能在含有`@objc`前綴的協定中生效。且`@objc`的協定只能被類別遵循。

`Counter`類別使用`CounterDataSource`型別，`CounterDataSource`含有`incrementForCount`的可選方法和`fiexdIncrement`的可選屬性。
```swift
@objc protocol CounterDataSource {
    optional func incrementForCount(count: Int) -> Int
    optional var fixedIncrement: Int { get }
}
```
>注意： CounterDataSource中的屬性和方法都是可選的，因此可以在類別中宣告但不實作這些成員，儘管技術上允許這樣做，不過最好不要這樣寫。

```swift
class Counter {
    var count = 0
    var dataSource: CounterDataSource?
    func increment() {
        if let amount = dataSource?.incrementForCount?(count) {
            count += amount
        } else if let amount = dataSource?.fixedIncrement {
            count += amount
        }
    }
}
```
count屬性用於儲存當前的值，increment方法用來為count賦值。
<br><br>
increment方法通過可選鏈，嘗試從兩種可選成員中獲取count:
1. 由於`dataSource`可能為`nil`，因此在`dataSource`後邊加上了`?`標記來表明只在`dataSource`非空時才去呼叫`incrementForCount`方法。
2. 即使`dataSource`存在，但是也無法保證其是否實作了`incrementForCount`方法，因此在`incrementForCount`方法後邊也加有`?`標記。

當`incrementForCount`不能被呼叫時，嘗試使用可選屬性`fixedIncrement`來代替。
<br><br>
ThreeSource實作了CounterDataSource協定
```swift
class ThreeSource: NSObject, CounterDataSource {
    let fixedIncrement = 3
}

var counter = Counter()
counter.dataSource = ThreeSource()
for _ in 1...4 {
    counter.increment()
    print(counter.count)
}
// 3
// 6
// 9
// 12
```
`TowardsZeroSource`實作了`CounterDataSource`協定中的`incrementForCount`方法
```swift
@objc class TowardsZeroSource: NSObject, CounterDataSource {
    func incrementForCount(count: Int) -> Int {
        if count == 0 {
            return 0
        } else if count < 0 {
            return 1
        } else {
            return -1
        }
    }
}

counter.count = -4
counter.dataSource = TowardsZeroSource()
for _ in 1...5 {
    counter.increment()
    print(counter.count)
}
// -3
// -2
// -1
// 0
// 0
```

---