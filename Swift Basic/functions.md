# Functions 函式
---
內容：
* [函數型別(Function Types)](#function_types)
* [嵌套函式(Nested Functions)](#nested_functions)

---

## <a name='function_types'></a> 函數型別(Function Types)

每個函式都有特定的函式型別，決定於函式的參考型別與回傳型別
``` swift
func addTwoInts(a: Int, b:Int) -> Int {
    return a + b
} // 此行別為(Int, Int) -> Int

var mathFunction: (Int, Int) -> Int = addTwoInts
print("\(mathFunction(2,3))") // 輸出: 5
```
<br>
使用函式型別`(Int, Int) -> Int`作為另一個函式的參考型別
```swift
func printMathResult(mathFunction:(Int, Int) -> Int, a: Int, b: Int) {
    print("\(mathFunction(a,b))")
}

printMathResult(addTwoInts, 2, 5) // 輸出: 7
```
<br>
使用函式型別`(Int) -> Int`作為另一個函式的回傳型別
```swift
func stepForward(input: Int) -> Int {
    return input + 1
}
func stepBackward(input: Int) -> Int {
    return input - 1
}

func chooseStepFunction(chooseFunc: Bool) -> (Int) -> Int {
    return chooseFunc ? stepBackward : stepForward
}
```
所以可以使用`chooseStepFunction`來獲得一個函式
```swift
var currentInt = 3
let moveNearToZero = chooseStepFunction(currentInt > 0)

while currentInt != 0 {
    print("\(currentValue)... ")
    currentInt = moveNearToZero(currentInt)
}
/* 輸出：
3...
2...
1...
*/
```

---

## <a name='nested_functions'></a> 嵌套函式(Nested Functions)

你可以把函式定義在別的函式體中
```swift
func chooseStepFunction(chooseFunc: Bool) -> (Int) -> Int {
    func stepForward(input: Int) -> Int { return input + 1 }
    func stepBackward(input: Int) -> Int { return input - 1 }
    return chooseFunc ? stepBackward : stepForward
}
```
```swift
var currentInt = -3
let moveNearToZero = chooseStepFunction(currentInt > 0)
while currentInt != 0 {
    print("\(currentValue)... ")
    currentInt = moveNearToZero(currentInt)
}
/* 輸出：
-3...
-2...
-1...
*/
```

---






