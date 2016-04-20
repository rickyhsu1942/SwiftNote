# Collection Types 集合
---
內容：
* [陣列(Array)](#array)
* [集合(Set)](#set)
* [字典(Dictionary)](#dictionary)

---
## <a name='array'></a>陣列(Array)
* 儲存同一種型態的數值
* 元素的位置有順序排列
* 相同數值可以多次存入

在swift中，陣列的宣告
```swift
var names:Array<String> = ["Andy", "Bob"]
var names: [String] = ["Andy", "Bob"]
var names = ["Andy", "Bob"]

print("\(names[0])") // 輸出：Andy
```
可以使用關鍵字`isEmpty`來檢查陣列是否為空的
```swift
if names.isEmpty {
    // 陣列無東西
} else {
    // 陣列有東西
}
```
可以使用關鍵字`append`或`+=`來增加元素
```swift
names.append("Cindy")
names += ["David", "Ellen"]

print("\(names)") // 輸出：["Andy", "Bob", "Cindy", "David", "Ellen"]
```
陣列可以配合區間運算值更改值
```swift
names[2...3] = ["Cabe", "Decker"]

print("\(names)") // 輸出：["Andy", "Bob", "Cabe", "Decker", "Ellen"]
```
插入與新增可以使用以下關鍵字
```swift
names.insert("Fox", atIndex: 5) // 插入
names.removeAtIndex(5) // 移除，此方法會回傳移除的元素
let lastName = names.removeLast() // 移除最後一個，此方法也會回傳移除的元素
```
關鍵字`enumerate`會回傳一個元組
```swift
for (index, value) in names.enumerate() {
    print("[\(index)]:\(value)")
    /*輸出：
    [0]:Andy
    [1]:Bob
    [2]:Clever
    [3]:Decker
    [4]:Ellen
    */
}
```
---
## <a name='set'></a>集合(Set)
* 儲存同一種型態的數值
* 元素的位置沒有順序排列
* 相同的數值只存入一次
* 首先只有能算出hashValue的類型才能放入Set中

集合的宣告
```swift
var names: Set<String> = Set<String>()
var names: Set<String> = []
var names: Set = ["Andy", "Bob"]
```
插入與移除
```swift
var names: Set = ["Andy", "Bob"]
names.insert("Clever") // 新增"Clever"
names.remove("Andy") // 移除"Andy"，此方法會回傳移除的元素，如果沒有對應元素被移除則回傳nil

print(names) // 輸出：["Bob", "Clever"]
```
使用關鍵字`contains`可以檢查集合中是否包含某元素
```swift
if names.contains("Bob") {
    print("找到Bob")
} else {
    print("找不到Bob")
}

// 輸出：找到Bob
```
交集、互斥或、連集和subtract，其中關鍵字`sort`是排序的意思
```swift
let aSet: Set = [1, 2, 3, 4, 5]
let bSet: Set = [4, 5, 6, 7, 8]

print("\(aSet.intersect(bSet).sort())") // 輸出：[4, 5]
print("\(aSet.exclusiveOr(bSet).sort())") // 輸出：[1, 2, 3, 6, 7, 8]
print("\(aSet.union(bSet).sort())") // 輸出：[1, 2, 3, 4, 5, 6, 7, 8]
print("\(aSet.subtract(bSet).sort())") // 輸出：[1, 2, 3]
```
判斷集合的關係
```swift
let aSet: Set = ["A", "B"]
let bSet: Set = ["A", "B", "C", "D"]
let cSet: Set = ["E", "F"]

print(aSet == bSet) // 判斷兩個是否相同，false

print(aSet.isSubsetOf(bSet)) // 判斷a是否為b的子集合，true
print(bSet.isSubsetOf(bSet)) // 判斷自己是否為自己的子集合，true

print(aSet.isStrictSubsetOf(bSet)) // 判斷a是否為b的子集合，true
print(bSet.isStrictSubsetOf(bSet)) // 判斷自己是否為自己的子集合，false

print(bSet.isSupersetOf(aSet)) // 判斷b是否為a的父集合，true
print(bSet.isSupersetOf(bSet)) // 判斷自己是否為自己的父集合，true

print(bSet.isStrictSupersetOf(aSet)) // 判斷b是否為a的父集合，true
print(bSet.isStrictSupersetOf(bSet)) // 判斷自己是否為自己的父集合，false

print(bSet.isDisjointWith(aSet)) // 判斷b是否與a分離，false
print(bSet.isDisjointWith(cSet)) // 判斷b是否與c分離，true

```
---
## <a name='dictionary'></a>字典(Dictionary)

* [key, value]的模式
* 元素沒有順序排序
* 可存入相同的值(value)

字典的宣告
```swift
var nameDic: Dictionary<Int, String> = [1: "Andy"]
var nameDic: [Int: String] = [1: "Andy"]

nameDic = [:] // 清空字典
```
插入、刪除與更新
```swift
nameDic[2] = "Bob" // 這樣就可以直接新增

nameDic[1] = nil // 給定nil，即可刪除
nameDic.removeValueForKey(1) // 使用此方法也可以刪除，且會回傳被刪除的值

nameDic[1] = "Amy" // 直接更新key為1的值
nameDic.updateValue("Amy", forKey: 1) // 使用此方法也可以更新，且會回傳被更新的舊值
```
---

>注意：<br>
在我們不需要改變陣列大小的時候創建不可變陣列是很好的習慣。如此 Swift 編譯器可以優化我們創建的集合。








