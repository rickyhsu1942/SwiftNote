# Generics 泛型
---
內容：
* [泛型所解決的問題](the_problem_that_generics_solve)
* [泛型函式](#generic_functions)
* [型別參數](#type_parameters)
* [命名型別參數](#naming_type_parameters)
* [泛型型別](#generic_types)
* [型別約束](#type_constraints)
* [關聯型別](#associated_types)
* [Where語句](#where_clauses)

---

泛型程式碼可以讓你寫出根據自我需求定義、適用於任何型別的，靈活且可重用的函式和型別。它的可以讓你避免重複的程式碼，用一種清晰和抽像的方式來表達程式碼的意圖。

## <a name='the_problem_that_generics_solve'></a> 泛型所解決的問題

泛型函式`swapTwoInts`,用來交換兩個`Int`值，這個函式使用寫入讀出`in-out`參數來交換`a`和`b`的值
```swift
func swapTwoInts(inout a: Int, inout _ b: Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```
`swapTwoInts`函式是非常有用的，但是它只能交換`Int`，如果你想要交換兩個`String`或者`Double`，就不得不寫更多的函式，如`swapTwoStrings`和`swapTwoDoublesfunctions`
```swift
func swapTwoStrings(inout a: String, inout _ b: String) {
    let temporaryA = a
    a = b
    b = temporaryA
}
 
func swapTwoDoubles(inout a: Double, inout _ b: Double) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```
實際應用中通常需要一個用處更強大並且盡可能的考慮到更多的靈活性單個函式，可以用來交換兩個任何型別值，很幸運的是，泛型程式碼幫你解決了這種問題。

---

## <a name='generic_functions'></a> 泛型函式

泛型函式可以工作於任何型別，這裡是一個上面`swapTwoInts`函式的泛型版本，用於交換兩個值
```swift
func swapTwoValues<T>(inout a: T, inout _ b: T) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```
這個函式的泛型版本使用了占位型別名字(通常此情況下用字母T來表示)來代替實際型別名(如`Int`、`String`或`Double`)。<br><br>
占位型別名沒有提示T必須是什麼型別，但是它提示了`a`和`b`必須是同一型別T，而不管T表示什麼型別。只有`swapTwoValues`函式在每次呼叫時所傳入的實際型別才能決定T所代表的型別。
<br><br>
另外一個不同之處在於這個泛型函式名後面跟著的展位型別名字`<T>`是用角括號括起來的，這個角括號告訴Swift那個T是`swapTwoValues`函式所定義的一個型別。因為T是一個占位命名型別，Swift不會去查找命名為T的實際型別。

```swift
var someInt = 3
var anotherInt = 107
swapTwoValues(&someInt, &anotherInt)
// someInt is now 107, and anotherInt is now 3
```
```swift
var someString = "hello"
var anotherString = "world"
swapTwoValues(&someString, &anotherString)
// someString is now "world", and anotherString is now "hello"
```

---

## <a name='type_parameters'></a> 型別參數

在上面的swapTwoValues範例中，占位型別T是一種型別參數的示例。型別參數指定並命名為一個占位型別，並且緊隨在函式名後面，使用一對角括號括起來<>
<br><br>
你可支援多個型別參數，命名在角括號中用逗號分開。

---

## <a name='naming_type_parameters'></a> 命名型別參數

如果你使用多個參數定義更複雜的泛型函式或泛型型別，那麼使用更多的描述型別參數是非常有用的。例如，Swift字典(`Dictionary`)型別有兩個型別參數，一個是鍵，另外一個是值。如果你自己寫字典，你或許會定義這兩個型別參數為`KeyType`和`ValueType`，用來記住它們在你的泛型程式碼中的作用。
>注意：<br>
請始終使用大寫字母開頭的駝峰式命名法（例如T和KeyType）來給型別參數命名，以表明它們是型別的占位符，而非型別值。

---

## <a name='generic_types'></a> 泛型型別

這部分教你如何寫一個泛型集合型別稱為佇列(Queue)，佇列是一組有順序的集合，類似陣列一樣，不同的地方在於陣列可以在指定的位置新增與移除，但佇列只能將新元素加在最後面，移除在最前面開始移除(FIFO)。
![stackPushPop_2x](Picture/stackPushPop_2x.png)

1. 現在有三個值在佇列中
2. 第四個值「pushed」到佇列頂部
3. 現在有四個值在佇列中
4. 佇列最頂部被移除，稱為「popped」
5. 移除後，現在佇列又重新只有三個值

這裡展示了如何寫一個非泛型版本的佇列(Int型別)
```swift
struct IntStack {
    var items = [Int]()
    mutating func push(item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
}
```
這裡是一個相同程式碼的泛型版本
```swift
struct Stack<Element> {
    var items = [Element]()
    mutating func push(item: Element) {
        items.append(item)
    }
    mutating func pop() -> Element {
        return items.removeLast()
    }
}
```
當創建一個新單例並初始化時， 通過用一對緊隨在型別名後的角括號裡寫出實際指定棧用到型別，創建一個`Stack`實例，同創建`Array`和`Dictionary`一樣
```swift
var stackOfStrings = Stack<String>()
stackOfStrings.push("uno")
stackOfStrings.push("dos")
stackOfStrings.push("tres")
stackOfStrings.push("cuatro")
// the stack now contains 4 strings
```
![stackPushedFourStrings_2x](Picture/stackPushedFourStrings_2x.png)
從棧中`pop`並移除值`cuatro`
```swift
let fromTheTop = stackOfStrings.pop()
// fromTheTop is equal to "cuatro", and the stack now contains 3 strings
```
![stackPoppedOneString_2x](Picture/stackPoppedOneString_2x.png)

---

## <a name='type_constraints'></a> 型別約束

型別約束指定了一個必須繼承自指定類別的型別參數，或者遵循一個特定的協定或協定構成。
<br><br>
例如，Swift 的`Dictionary`型別對作用於其鍵的型別做了些限制。在字典的描述中，字典的鍵型別必須是可雜湊，也就是說，必須有一種方法可以使其是唯一的表示。

### 型別約束語法

你可以寫一個在一個型別參數名後面的型別約束，通過冒號分割，來作為型別參數鏈的一部分。這種作用於泛型函式的型別約束的基礎語法如下所示（和泛型型別的語法相同）
```swift
func someFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U) {
    // function body goes here
}
```
上面這個假定函式有兩個型別參數。第一個型別參數T，有一個需要T必須是`SomeClass`子類別的型別約束；第二個型別參數U，有一個需要U必須遵循`SomeProtocol`協定的型別約束。

### 型別約束行為

這裡有個名為`findStringIndex`的非泛型函式，該函式功能是去查找包含一給定`String`值的陣列。若查找到匹配的字串，`findStringIndex`函式回傳該字串在陣列中的索引值(`Int`)，反之則回傳`nil`
```swift
func findStringIndex(array: [String], _ valueToFind: String) -> Int? {
    for (index, value) in array.enumerate() {
        if value == valueToFind {
            return index
        }
    }
    return nil
}

let strings = ["cat", "dog", "llama", "parakeet", "terrapin"]
if let foundIndex = findStringIndex(strings, "llama") {
    print("The index of llama is \(foundIndex)")
}
// 輸出 "The index of llama is 2"
```
如下，寫一個`findStringIndex`的泛型版本`findIndex`。注意這個函式仍然回傳`Int`
```swift
func findIndex<T>(array: [T], _ valueToFind: T) -> Int? {
    for (index, value) in array.enumerate() {
        if value == valueToFind {
            return index
        }
    }
    return nil
}
// 編譯不會成功
```
上面所寫的函式不會編譯成功。這個問題的位置在等式的檢查上，`if value == valueToFind`。不是所有的Swift中的型別都可以用等式符`==`進行比較。正因如此，這部分程式碼不能可能保證工作於每個可能的型別`T`。
<br><br>
Swift 標準函式庫中定義了一個`Equatable`協定，該協定要求任何遵循的型別實作等式符`==`和不等符`!=`對任何兩個該型別進行比較。
```swift
func findIndex<T: Equatable>(array: [T], _ valueToFind: T) -> Int? {
    for (index, value) in array.enumerate() {
        if value == valueToFind {
            return index
        }
    }
    return nil
}

let doubleIndex = findIndex([3.14159, 0.1, 0.25], 9.3)
// doubleIndex is an optional Int with no value, because 9.3 is not in the array
let stringIndex = findIndex(["Mike", "Malcolm", "Andrea"], "Andrea")
// stringIndex is an optional Int containing a value of 2
```

---

## <a name='associated_types'></a> 關聯型別

當定義一個協定時，有的時候宣告一個或多個關聯型別作為協定定義的一部分是非常有用的。

### 關聯型別行為

這裡是一個`Container`協定的範例，定義了一個`ItemType`關聯型別
```swift
protocol Container {
    typealias ItemType
    mutating func append(item: ItemType)
    var count: Int { get }
    subscript(i: Int) -> ItemType { get }
}
```
`Container`協定定義了三個任何容器必須支援的相容要求
* 必須可能通過`append`方法添加一個新item到容器裡；
* 必須可能通過使用`count`屬性獲取容器裡items的數量，並回傳一個`Int`值；
* 必須可能通過容器的`Int`索引值下標可以檢索到每一個`item`。

`IntStack`型別的非泛型版本，遵循`Container`協定
```swift
struct IntStack: Container {
    // original IntStack implementation
    var items = [Int]()
    mutating func push(item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
    // conformance to the Container protocol
    typealias ItemType = Int
    mutating func append(item: Int) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Int {
        return items[i]
    }
}
```
`IntStack`型別實作了`Container`協定的所有三個要求，此外，`IntStack`指定了`Container`的實作，適用的`ItemType`被用作`Int`型別。
<br><br>
感謝Swift型別參考，你不用在`IntStack`定義部分宣告一個具體的`Int`的`ItemType`。只要通過簡單的查找`append`方法的`item`參數型別和下標回傳的型別，Swift就可以推斷出合適的`ItemType`來使用。
<br><br>
你也可以生成遵循`Container`協定的泛型`Stack`型別
```swift
struct Stack<Element>: Container {
    // original Stack<Element> implementation
    var items = [Element]()
    mutating func push(item: Element) {
        items.append(item)
    }
    mutating func pop() -> Element {
        return items.removeLast()
    }
    // conformance to the Container protocol
    mutating func append(item: Element) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Element {
        return items[i]
    }
}
```

### 擴展一個存在的型別為一指定關聯型別

在使用擴展來添加協定相容性中有描述擴展一個存在的型別添加遵循一個協定。這個型別包含一個關聯型別的協定。
<br><br>
Swift的`Array`已經提供`append`方法，一個`count`屬性和通過下標來查找一個自己的元素。這三個功能都達到`Container`協定的要求。也就意味著你可以擴展`Array`去遵循`Container`協定
```swift
extension Array: Container {}
```

---

## <a name='where_clauses'></a> Where語句

一個`where`語句使你能夠要求一個關聯型別遵循一個特定的協定，以及那個特定的型別參數和關聯型別可以是相同的。
<br><br>
下面的列子定義了一個名為`allItemsMatch`的泛型函式，用來檢查是否兩個`Container`單例包含具有相同順序的相同元素。如果匹配到所有的元素，那麼回傳一個為`true`，反之，則相反。這個需求通過一個型別約束和where語句結合
```swift
func allItemsMatch<
    C1: Container, C2: Container
    where C1.ItemType == C2.ItemType, C1.ItemType: Equatable>
    (someContainer: C1, _ anotherContainer: C2) -> Bool {
        
        // check that both containers contain the same number of items
        if someContainer.count != anotherContainer.count {
            return false
        }
        
        // check each pair of items to see if they are equivalent
        for i in 0..<someContainer.count {
            if someContainer[i] != anotherContainer[i] {
                return false
            }
        }
        
        // all items match, so return true
        return true
}
```
* C1必須遵循Container協定 (寫作 C1: Container)。
* C2必須遵循Container協定 (寫作 C2: Container)。
* C1的ItemType同樣是C2的ItemType (寫作 C1.ItemType == C2.ItemType)。
* C1的ItemType必須遵循Equatable協定 (寫作 C1.ItemType: Equatable)。

```swift
var stackOfStrings = Stack<String>()
stackOfStrings.push("uno")
stackOfStrings.push("dos")
stackOfStrings.push("tres")
 
var arrayOfStrings = ["uno", "dos", "tres"]
 
if allItemsMatch(stackOfStrings, arrayOfStrings) {
    print("All items match.")
} else {
    print("Not all items match.")
}
// 輸出 All items match.
```

---