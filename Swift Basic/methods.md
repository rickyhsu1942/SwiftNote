# Methods 方法
---
內容：
* [實例方法 (Instance Methods)](#instance_methods)
* [型別方法 (Type Methods)](#type_methods)

---
方法是定義在類別、結構或列舉之內的函式。結構和列舉能夠定義方法是`Swift`與 `C/Objective-C` 的主要區別之一。

## <a name='instance_methods'></a> 實例方法 (Instance Methods)
實例方法提供存取和修改實例屬性的方法或提供與實例目的相關的功能，並以此來支撐實例的功能。
```swift
class Counter {
  var count = 0
  func increment() { // 遞增1的實例方法
    count++
  }
  func incrementBy(amount: Int) { // 遞增整數參數的實例方法
    count += amount
  }
  func reset() { // 重置為0的實例方法
    count = 0
  }
}
```

### self屬性(The self Property)
使用這條規則的主要場景是實例方法的某個參數名稱與實例的某個屬性名稱相同的時候(參數名稱享有優先權)。這時你可以使用`self`屬性來區分參數名稱和屬性名稱。

```swift
struct Point {
  var x = 0.0, y = 0.0
  func isToTheRightOfX(x: Double) -> Bool {
    return self.x > x
  }
}
let somePoint = Point(x: 4.0, y: 5.0)
if somePoint.isToTheRightOfX(1.0) {
  print("This point is to the right of the line where x == 1.0")
}
// 輸出 "This point is to the right of the line where x == 1.0"（這個點在x等於1.0這條線的右邊）
```
### 在實例方法中修改值型別 (Modifying Value Types from Within Instance Methods)

一般情況下，值型別的屬性不能在實例方法中被修改
```swift
struct Test {
    var x = 0
    func updateValueBy(num:Int) {
        x = num // 錯誤
    }
}
```
使用關鍵字`mutating`，使用`變異`方法就可以修改值型別
```swift
struct Test {
    var x = 0
    mutating func updateValueBy(num:Int) {
        x = num
    }
}
```
### 在變異方法中給self賦值

變異方法能夠賦給隱含屬性self一個全新的實例。
```swift
struct Point {
  var x = 0.0, y = 0.0
  mutating func moveByX(deltaX: Double, y deltaY: Double) {
    self = Point(x: x + deltaX, y: y + deltaY)
  }
}
```

---

## <a name='type_methods'></a> 型別方法 (Type Methods)

顧名思義就是跟隨著型別
* 在結構或例舉定義型別方法，使用`static`在`func`前面
* 在類別定義型別方法，使用`class`在`func`前面

>注意:<br>
如果使用`class`，當子類別繼承此型別可以覆寫這個方法，但使用`static`則子類別無法覆寫。

```swift
class SomeClass {
  class func someTypeMethod() {
    // ...
  }
}

// 不用建立實體，直接用類別名字呼叫型別方法
SomeClass.someTypeMethod()
```

---