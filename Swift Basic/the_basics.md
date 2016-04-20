# The Basics 基礎篇
---
內容：
* [常數和變數](#constants_and_variables)
* [布林值](#booleans)
* [標註型態](#types)
* [註解](#comments)
* [類型判斷](#type_inference)
* [類型轉換](#type_conversion)
* [型態別名](#type_aliases)
* [元組(Tuples)](#tuples)
* [Optionals](#optionals)
* [錯誤處理](#error_handling)
* [斷言Assertions](#assertions)

---
## <a name='constants_and_variables'></a> 常數和變數

使用`let`宣告常數<br/>
使用`var`宣告變數

```swift
let aConstant = 100
var aVariable = 30

aConstant = 80 // 這邊更改常數會出現錯誤
aVariable = 500 // 變數可以改值
```
如果想要宣告多個，可用`,`逗點隔開
```swift
var a=1, b=2, c=3 // 使用逗點隔開
```
---
## <a name='booleans'></a> 布林值

在swift的布林值中，只能為`true`或`false`
```swift
let a = true
if a {
    print("^_^")
}
// 輸出^_^
```
但swift並不會把1當成`true`
```swift
let one = 1
if one {
    print(">_<")
}
// 會出現錯誤
```

---
## <a name='types'></a> 標註型態

宣告aBoy變數為String
```swift
var aBoy: String
```
在此宣告後面也可以直接指初始值
```swift
var aBoy: String = "Andy"
```
---
## <a name='comments'></a> 註解

在Swift中，註解可以塞入註解
```swift
/* 
第一層的註解開始
@$@!$@%
/* 註解裡面的註解 */
@#$!@$!
註解結束
*/
```
---
## <a name='type_inference'></a> 類型判斷

當沒有宣告型態時，它會依照給定得初始值去判斷型別
```swift
let money = 500
// money會依照你給訂的初始值，被判斷為Int型別
```
---
## <a name='type_conversion'></a> 類型轉換

以下舉例如何型態轉換
```swift
let a:Float = 30.2
let b:Int = 5
let c: Float = a + Float(b) // 將b轉換為Float型別，否則將會出錯
```
---
## <a name='type_aliases'></a> 型態別名

將現有的型態定義成另一個名字．使用關鍵字`typealias`
```swift
typealias 整數 = Int // swift可以使用中文當作變數名稱

let a: 整數 = 123
print("\(a)") // 輸出結果為123，且型態為'整數'
```
---
## <a name='tuples'></a> 元組(Tuples)

元組的主要用途是把多個值組合在一起，並可以使用Index來操作元組元素
```swift
let manInfo: (String, Int) = ("Andy", 1955)

print("\(manInfo.0)") // 輸出：Andy
print("\(manInfo.1)") // 輸出：1955
```
可以將元組內容放入常數或變數
```swift
let (name, birth) = manInfo

print("\(name)") // 輸出：Andy
```
如果想要忽略可使用`_`底線忽略

```swift
let (name, _) = manInfo

print("\(name)") // 輸出Andy
```
可以直接幫元組定義元素的命名
```swift
let manInfo = (Name: "Andy", birthYear: 1955)

print("\(manInfo.Name)") // 輸出：Andy
print("\(manInfo.birthYear)") // 輸出：1955
```
---
## <a name='optionals'></a> Optionals

這是Swift新的類型，在型態後面加上`?`則為Optionals。<br>
```swift
var num:Int?
print("\(num)") // 輸出：nil
```
簡單的來描述Optional就是以下兩種狀態
* 有值
* 沒有值(nil)

在正常狀況，如果此常數或變數不為Optionals類型，其常數或變數不可為nil
```swift
var name: String = "Andy"

name = nil // 出錯
```
反之
```swift
var name: String? = "Andy"

name = nil // 可以指定為nil
```
如果Optionals沒指派出始值，則會預設為nil
```swift
var name: String? // 預設為nil
```
在`Int`(整數型態)與`Int?`(可選型整數型態)兩個並非一樣
```swift
var a: Int = 123
var b: Int? = 123

print("\(a)") // 輸出：123
print("\(b)") // 輸出：Optional(123)
```
當確定某個Optionals有值，可用在變數後面增加`!`驚嘆號來強制解析，代表裡面一定有值(如果沒有值將會崩潰)

```swift
var b: Int? = 123

print("\(b)") // 輸出：Optional(123)
print("\(b!)") // 輸出：123
```
我們可以使用`if`來判斷Optionals是否有值，並用`!`將值取出
```swift
var aBoy: String? = "Andy"

if aBoy == nil {
    print("沒男孩")
} else {
    print("有男孩，名字為\(aBoy!)")
}

// 輸出：有男孩，名字為Andy
```
可以在`if`中使用一個臨時的常數或變數改寫上面例子
```swift
var aBoy: String? = "Andy"

if let tempBoy = aBoy {
    print("沒男孩")
} else {
    print("有男孩，名字為\(tempBoy)")
}

// 輸出：有男孩，名字為Andy
```
此可選綁定能多個
```swift
if let temp1 = someOpt, temp2 = someOpt2, ... {
    // 只有其中一個可選為nil，則不會進來此判斷式
}
```
---
## <a name='error_handling'></a> 錯誤處理

要使用錯誤處理，可在函式的後面加上關鍵字`throws`<br>
(如果對函式不清楚的，可到XX章節先看函式`func`的介紹)
```swift
func getFullName(firstname:String, withLastName lastname:String) throws -> String {
    let fullName = firstname + "" + lastname
    return fullName
}
```
但如果單單只有加入`throws`就呼叫此函式，程式將會出錯，因為它必須配合`try`
```swift
let result = try getFullName("Hsu", withLastName: "Ricky")
```
可以使用`do`和`catch`來抓出錯誤
```swift
do {
    let result = try getFullName("Hsu", withLastName: "Ricky")
    print("\(result)")
} catch {
    print("錯誤發生")
}
```
但因為目前都還沒有定義錯誤，所以我們宣告`enum`來建立錯誤
```swift
enum NameInputError: ErrorType {
    case Empty
    case ISNumber
}
```
並在原本`getFullName`函式中配合`guard`關鍵字，判斷錯誤情況並丟出錯誤
```swift
func getFullName(firstname:String, withLastName lastname:String) throws -> String {
    guard firstname != "" && lastname != "" else { throw NameInputError.Empty }
    guard Int(firstname) == nil && Int(lastname) == nil else { throw NameInputeError.ISNumber }
    
    let fullName = firstname + "" + lastname
    return fullName
}
```
更改後，就可以新增`catch`條件來抓取對應的錯誤
```swift
do {
    let result = try getFullName("Hsu", withLastName: "Ricky")
    print("\(result)")
} catch NameInputError.Empty{
    print("偵測到姓名未輸入")
} catch NameInputError.ISNumber{
    print("偵測到姓名有數字")
} catch {
    print("其他錯誤發生")
}
```
---
## <a name='assertions'></a> 斷言Assertions

當要除錯(Debugging)時可以使用斷言來幫忙，斷言的兩種狀況
* 條件為true，程式繼續運行
* 條件為false，程式停止運行

```swift
let age = 15
assert(age >= 18, "年紀未成年，程式停止") 
// 此條件為false，將停止運行程式

// 輸出：assertion failed: 年紀未成年，程式停止: file 路徑, line 2(第幾行)
```
此語法的錯誤描述可省略不寫

---






