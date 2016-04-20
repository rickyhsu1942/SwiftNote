# Basic Operators 基本運算子
---
內容：
* [區間運算值](#range_operators)
* [Nil Coalescing Operator](#nil_coalescing_operators)

---
## <a name='range_operators'></a> 區間運算值
以下面例子做舉例，在原先的迴圈可能如下
```swift
for var index = 1; index <= 5 ; index ++ {
    // 執行5次迴圈
}
```
使用區間運算值
```swift
for index in 1...5 {
    // 執行5次迴圈
}
```
```swift
for index in 1..<5 {
    // 執行4次回圈
}
```
注意，在寫區間運算值時，中間不可空白

---
## <a name='nil_coalescing_operators'></a> Nil Coalescing Operator
這個判斷式是利用兩個`??`問號來表示，功能像是下面例子
```swift
a != nil ? a! : b // 如果a不是nil則為a，否則為b
```
實際套用`Nil Coalescing Operator`例子如下
```swift
let defaultColor = "red"
var userDefinedColor: String? // 預設為nil

var colorToUse = userDefinedColor ?? defaultColor
print(colorToUse) // 輸出：red
```
如果userDefinedColor為nil，colorToUse = defaultColor，<br>
反之userDefinedColor不為nil，colorToUse = userDefinedColor
```swift
userDefinedColor = "green"
var colorToUse = userDefinedColor ?? defaultColor
print(colorToUse) // 輸出：green
```
---