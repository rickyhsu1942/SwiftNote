# Nested Types 巢狀型別(嵌套型別)
---
內容：
* [巢狀型別實作](#nested_types_in_action)
* [巢狀型別的參考](#referring_to_nested_types)

---
Swift允許你定義巢狀型別，可以在列舉型別、類別和結構中定義支援嵌套的型別。


## <a name='nested_types_in_action'></a> 巢狀型別實作
```swift
struct BlackjackCard {
    // 嵌套定義列舉型Suit
    enum Suit: Character {
        case Spades = "♠", Hearts = "♡", Diamonds = "♢", Clubs = "♣"
    }
    
    // 嵌套定義列舉型Rank
    enum Rank: Int {
        case Two = 2, Three, Four, Five, Six, Seven, Eight, Nine, Ten
        case Jack, Queen, King, Ace
        struct Values {
            let first: Int, second: Int?
        }
        var values: Values {
            switch self {
            case .Ace:
                return Values(first: 1, second: 11)
            case .Jack, .Queen, .King:
                return Values(first: 10, second: nil)
            default:
                return Values(first: self.rawValue, second: nil)
            }
        }
    }
    
    // BlackjackCard 的屬性和方法
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

let theAceOfSpades = BlackjackCard(rank: .Ace, suit: .Spades)
print("theAceOfSpades: \(theAceOfSpades.description)")
// 輸出: theAceOfSpades: suit is ♠, value is 1 or 11
```
儘管`Rank`和`Suit`嵌套在`BlackjackCard`中，但仍可被參考，所以在初始化實例時能夠通過列舉型別中的成員名稱單獨參考。在上面的範例中`description`屬性能正確得輸出對Ace牌有1和11兩個值。

---

## <a name='referring_to_nested_types'></a> 巢狀型別的參考

在外部對嵌套型別的參考，以被嵌套型別的名字為前綴，加上所要參考的屬性名
```swift
let heartsSymbol = BlackjackCard.Suit.Hearts.rawValue
// 紅心的符號 為 "♡"
```

---