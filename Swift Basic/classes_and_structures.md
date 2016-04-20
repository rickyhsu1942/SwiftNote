# Classes and Structures 類別與結構
---
內容：
* [類別與結構對比](#comparing_classes_and_structures)
* [類別和結構的選擇](#choosing_between_classes_and_structures)

---

## <a name='comparing_classes_and_structures'></a> 類別與結構對比
Swift中`Class`(類別)與`Structures`(結構)的共同點
* 定義屬性(Properties)來儲存值
* 定義方法(Methods)來提供功能
* 定義腳本(Subscripts)來存取值
* 定義建構器(Initialization)來生成初始化
* 使用擴展(Extensions)增加預設實作功能
* 符合協定(Protocols)以對某類別提供標準功能

類別比結構還有多以下功能：
* 繼承允許一個類別繼承另一個類別
* 型別轉換允許在執行時檢查和解釋一個類別實例的型別
* 解構器允許一個類別實例釋放任何其所被分配的資源
* 參考計數允許對一個類別的多次參考

>注意：<br>
結構總是通過被複製的方式在程式碼中傳遞，因此請不要使用參考計數。

```swift
class SomeClass {
    // 類別定義
}

struct SomeStructure {
    // 結構定義
}
```
>注意：<br>
在你每次定義一個新類別或者結構的時候，實際上你是有效地定義了一個新的 Swift 型別。因此請使用 UpperCamelCase 方式來命名（如 SomeClass 和SomeStructure等），以便符合標準Swift 型別的大寫命名風格（如String，Int和Bool）。相反的，請使用lowerCamelCase這種方式為屬性和方法命名（如framerate和incrementCount），以便和類別區分。

```swift
// 定義Resolution結構
struct Resolution {
    var width = 0
    var height = 0
}
// 定義VideoMode類別
class VideoMode {
    var resolution = Resolution()
    var interlaced = false
    var frameRate = 0.0
    var name: String?
}

// 生成結構與類別
let someResolution = Resolution()
let someVideoMode = VideoMode()

// 可以使用點語法為屬性變數賦值
someVideoMode.resoluction.width = 12880
```
結構都有一個自動生成的成員逐一建構器，用於初始化新結構實例中成員的屬性，與類別不同(類別實例沒有預設的成員逐一建構器)
```swift
let vga = resolution(width:640, heigth: 480)
```
結構與列舉是值型別，類別是參考型別(以下面程式碼為例子)

```swift
let tenEighty = VideoMode()
tenEighty.resolution = hd
tenEighty.interlaced = true
tenEighty.name = "1080i"
tenEighty.frameRate = 25.0

let alsoTenEighty = tenEighty
alsoTenEighty.frameRate = 30.0

print("The frameRate property of tenEighty is now \(tenEighty.frameRate)")
// 輸出 "The frameRate property of theEighty is now 30.0"
```

因為類別是參考型別，Swift內建了兩個恆等運算子來判斷兩個常數或變數是否參考同一個類別實例

* 等價於 (===)
* 不等價於 (!==)

```swift
if tenEighty === alsoTenTighty {
    println("tenTighty and alsoTenEighty refer to the same Resolution instance.")
}
//輸出 "tenEighty and alsoTenEighty refer to the same Resolution instance."
```

---

## <a name='choosing_between_classes_and_structures'></a> 類別和結構的選擇

當符合一條或多條以下條件時，請考慮構建結構：

* 結構的主要目的是用來封裝少量相關簡單資料值
* 操作實體時是被拷貝而不是參考
* 結構不需要去繼承另一個已存在型別的屬性或者行為。

適合使用結構的例子

* 幾何形狀的大小，封裝一個width屬性和height屬性，都為Double型別。
* 一定範圍內的路徑，封裝一個start屬性和length屬性，兩者均為Int型別。
* 三維坐標系內一點，封裝x，y和z屬性，三者均為Double型別。

在所有其它案例中，定義一個類別，生成一個它的實例，並通過參考來管理和傳遞。實際中，這意味著絕大部分的自定義資料建構都應該是類別，而非結構。

---






