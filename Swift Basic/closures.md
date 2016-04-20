# Closures 閉包
---
內容：
* [閉包表達式(Closure Expressions)](#closure_expressions)
* [捕獲值(Capturing Values)](#capturing_values)

---

## <a name='closure_expressions'></a> 閉包表達式(Closure Expressions)

閉包的語法
```swift
{ (parameters) -> returnType in
    statements
}

{ (參數) -> 回傳型態 in
    程式碼
}
```
```swift
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]

var reversed = names.sort({ (s1: String, s2: String) -> Bool in
    return s1 > s2
})
```
可以根據上下文來推斷型別，Swift可以推斷其參數和回傳值的型別來縮減閉包
```swift
var reversed = names.sort({ s1, s2 in
    return s1 > s2
})
```
如果只是單行表示閉包可以隱藏`return`
```swift
var reversed = names.sort({ s1, s2 in s1 > s2 })
```
swift提供了參數名稱縮寫功能，直接透過`$0`、`$1`、`$2`來順序呼叫閉包參數
```swift
var reversed = names.sort({ $0 > $1 })
```
實際上在這個例子，還有更簡短的寫法，就是只留運算子
```swift
var reversed = names.sort(>)
```
<br>
如果你需要將一個很長的閉包作為最後一個參數傳遞給函式，可以使用尾隨閉包來增加可讀性
```swift
func someFunctionTahtTakesAClosure(closure: () -> ()) {
    // 函式程式碼
}

// 不使用尾隨閉包
someFunctionTahtTakesAClosure({
    // 閉包程式碼
})

// 使用尾隨閉包
someFunctionTahtTakesAClosure() {
    // 閉包程式碼
}
// 如果函式只有閉包一個參數，使用尾隨閉包時，可以將()省略掉
```
```swift
var reversed = names.sort(){ $0 > $1 }
//省略()
var reversed = names.sort{ $0 > $1 }
```
---

## <a name='capturing_values'></a> 捕獲值(Capturing Values)

Swift最簡單的閉包形式是[嵌套函式](chapter4.md#Nested_Functions)。嵌套函式可以捕獲其外部函式所有的參數以及定義的變數
```swift
func makeIncrementor(forIncrement amount: Int) -> () -> Int {
    var runningTotal = 0
    func addAmount() -> Int {
        runningTotal += amount
        return runningTotal
    } // 在這個嵌套函式捕獲了參數amount與變數runningTotal
    return addAmount
}

let addTen = makeIncrementor(forIncrement: 10)

addTen()
// 回傳的值為10
addTen()
// 回傳的值為20
addTen()
// 回傳的值為30
```
如果創建了另一個`addAmount`，其會有一個屬於自己獨立的`runningTotal`變數的參考
```swift
let addSeven = makeIncrementor(forIncrement: 7)
addSeven()
// 回傳的值為7
addTen()
// 回傳的值為40
```

>注意：<br>
如果你將閉包賦值給一個類別實例的屬性，並且該閉包通過指向該實例或其成員來捕獲了該實例，你將創建一個在閉包和實例間的強參考環。(會導致記憶體洩漏memory leak)。更多資訊，請參考[閉包引起的迴圈強參考]()。

函式與閉包都是參考型別，用下面例子作示範
```swift
let otherAddTen = addTen
otherAddTen()
// 回傳的值為50
```

---