# Subscripts 下標腳本
---
內容：
* [下標腳本語法 (Subscript Syntax)](#subscript_syntax)
* [下標腳本選項 (Subscript Options)](#subscript_options)

---
當使用陣列或字典，當使用到`someArray[Index]`或`someDictionary[key]`，後面的`[Index]`和`[key]`就是下標語法

## <a name='subscript_syntax'></a> 下標腳本語法 (Subscript Syntax)

下標腳本允許你通過在實例後面的方括號中傳入一個或者多個的索引值來對實例進行存取和賦值。定義下標腳本使用subscript關鍵字
```swift
subscript(index: Int) -> Int {
    get {
      // 回傳與入參匹配的Int型別的值
    }

    set(newValue) { // newValue的型別必須和下標腳本定義的回傳型別相同。
      // 執行賦值操作
    }
}
```
```swift
struct TimesTable {
    let multiplier: Int
    subscript(index: Int) -> Int {
      return multiplier * index
    }
}
let threeTimesTable = TimesTable(multiplier: 3)
println("3的6倍是\(threeTimesTable[6])")
// 輸出 "3的6倍是18"
```

## <a name='subscript_options'></a> 下標腳本選項 (Subscript Options)

一個類別或結構可以根據自身需要提供多個下標腳本實作，透過傳來的參數來匹配合適的下標腳本實作執行(下標腳本的重載)。
```swift
class Test {
    subscript(index: String) -> String {
        print("傳入的參數是字串：\(index)")
        return index
    }
    
    subscript(index: Int...) -> Int {
        var total = 0
        for val in index {
            print("傳入的參數是：\(val)")
            total += val
        }
        return total
    }
}

let myTest = Test()
myTest["Andy"]
myTest[11, 22, 33]
/*
傳入的參數是字串：Andy
傳入的參數是：11
傳入的參數是：22
傳入的參數是：33
*/
```

---