# Properties 屬性
---
內容：
* [儲存屬性(Stored Properties)](#stored_properties)
* [計算屬性(Computed Properties)](#computed_properties)
* [屬性監視器(Property Observers)](#property_observers)
* [全域變數和局部變數(Global and Local Variables)](#global_and_local_variables)
* [型別屬性語法 (Type Properties)](#type_properties)

---

## <a name='stored_properties'></a> 儲存屬性 (Stored Properties)

儲存屬性就是實體裡面的一個變數或常數，分為
* 變數儲存屬性(`var`定義)
* 常數儲存屬性(`let`定義)

```swift
struct FixedLengthRange {
    var firstValue: Int
    let length: Int
}

var rangeOgThreeItems = FixedLengthRange(firstValue: 0, length: 3)
```
###常數和儲存屬性

如果創建一個結構實例並賦值給一個常數，則無法修改實例的任何性
```swift
let rangeOgThreeItems = FixedLengthRange(firstValue: 0, length: 4)

rangeOgThreeItems.firstValue = 5
// 儘管 firstValue 是個變數屬性，這裡還是會報錯
```

### 延遲儲存屬性

延遲儲存屬性是指當第一次被呼叫的時候才會計算其初始值的屬性。使用關鍵字`@lazy`來表示。(必須將延遲儲存屬性宣告為變數`var`)<br>
##### 延遲屬性適合用在:
* 當屬性的值依賴於在實例的建構過程結束前無法知道具體值的外部因素時
* 當屬性的值需要複雜或大量計算時

```swift
class DataImporter {
    /*
    DataImporter 是一個將外部文件中的資料導入的類別。
    這個類別的初始化會消耗不少時間。
    */
    var fileName = "data.txt"
    // 這是提供資料導入功能
}

class DataManager {
    @lazy var importer = DataImporter()
    var data = String[]()
    // 這是提供資料管理功能
}

let manager = DataManager()
manager.data += "Some data"
manager.data += "Some more data"
// DataImporter 實例的 importer 屬性還沒有被創建


println(manager.importer.fileName)
// DataImporter 實例的 importer 屬性現在被創建了
// 輸出 "data.txt」
```

---


## <a name='computed_properties'></a> 計算屬性 (Computed Properties)

計算屬性不直接儲存值，而是提供一個 getter 來獲取值，一個可選的 setter 來間接設置其他屬性或變數的值
```swift
struct Point {
    var x = 0.0, y = 0.0
}
struct Size {
    var width = 0.0, height = 0.0
}

struct Rect {
    var origin = Point()
    var size = Size()
    var center: Point {
        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            return Point(x: centerX, y: centerY)
        }
        set(newCenter) {
            origin.x = newCenter.x - (size.width / 2)
            origin.y = newCenter.y - (size.height / 2)
        }
    }
}

var square = Rect(origin: Point(x: 0.0, y: 0.0),
    size: Size(width: 10.0, height: 10.0))
let initialSquareCenter = square.center
square.center = Point(x: 15.0, y: 15.0)
print("square.origin is now at (\(square.origin.x), \(square.origin.y))")
// 輸出：square.origin is now at (10.0, 10.0)
```

### 便捷setter宣告

如果計算屬性的`setter`沒有定義表示新值的參數名，則可以使用預設名稱`newValue`

```swift
struct AlternativeRect {
    var origin = Point()
    var size = Size()
    var center: Point {
        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            return Point(x: centerX, y: centerY)
        }
        set {
            origin.x = newValue.x - (size.width / 2)
            origin.y = newValue.y - (size.height / 2)
        }
    }
}
```

### 唯讀計算屬性

只有 getter 沒有 setter 的計算屬性就是唯讀計算屬性

>注意：<br>
不管是「計算屬性」或是「唯讀計算屬性」都只能用`var`變數來宣告

唯讀計算屬性的宣告可以去掉get關鍵字和花括號
```swift
struct Cuboid {
    var width = 0.0, height = 0.0, depth = 0.0
    var volume: Double {
    return width * height * depth
    }
}
let fourByFiveByTwo = Cuboid(width: 4.0, height: 5.0, depth: 2.0)
print("the volume of fourByFiveByTwo is \(fourByFiveByTwo.volume)")
// 輸出：the volume of fourByFiveByTwo is 40.0
```

---

## <a name='property_observers'></a> 屬性監視器

每次屬性被設置值的時候都會呼叫屬性監視器，可以為除了延遲儲存屬性之外的其他儲存屬性添加屬性監視器

>注意：<br>
不需要為無法overriding的計算屬性添加屬性監視器，因為可以通過 setter 直接監控和響應值的變化。

* `willSet`在設置新的值之前呼叫
* `didSet`在新的值被設置之後立即呼叫

>注意：<br>
willSet和didSet監視器在屬性初始化過程中不會被呼叫，它們只會當屬性的值在初始化之外的地方被設置時被呼叫。

```swift
class StepCounter {
    var totalSteps: Int = 0 {
        willSet(newTotalSteps) {
            print("About to set totalSteps to \(newTotalSteps)")
        }
        didSet {
            if totalSteps > oldValue  {
                print("Added \(totalSteps - oldValue) steps")
            }
        }
    }
}
let stepCounter = StepCounter()
stepCounter.totalSteps = 200
// About to set totalSteps to 200
// Added 200 steps
stepCounter.totalSteps = 360
// About to set totalSteps to 360
// Added 160 steps
stepCounter.totalSteps = 896
// About to set totalSteps to 896
// Added 536 steps
```
>注意：<br>
如果在didSet監視器裡為屬性賦值，這個值會替換監視器之前設置的值。

---

## <a name='global_and_local_variables'></a> 全域變數和局部變數 (Global and Local Variables)

全域變數是在函式、方法、閉包或任何型別之外定義的變數<br>
局部變數是在函式、方法或閉包內部定義的變數

>注意：<br>
全域的常數或變數都是延遲計算的，跟延遲儲存屬性相似，不同的地方在於，全域的常數或變數不需要標記`@lazy`特性。<br>
局部範圍的常數或變數不會延遲計算。

---

## <a name='type_properties'></a> 型別屬性語法 (Type Properties)
它的好處是可以讓不同的實體彼此之間共享同一份屬性資料。使用`static`來定義型別屬性
``` swift
struct SomeStructure {
    static var storedTypeProperty = "Some value."
    static var computedTypeProperty: Int {
    // 這裡回傳一個 Int 值
    }
}
enum SomeEnumeration {
    static var storedTypeProperty = "Some value."
    static var computedTypeProperty: Int {
    // 這裡回傳一個 Int 值
    }
}
class SomeClass {
    static var computedTypeProperty: Int {
    // 這裡回傳一個 Int 值
    }
}
```

>注意：<br>
範例中的計算型型別屬性是唯讀的，但也可以定義可讀可寫的計算型型別屬性，只要加上set即可。

### 獲取和設置型別屬性的值

跟實例的屬性一樣，型別屬性的存取也是通過點運算子來進行，但是，型別屬性是通過型別本身來獲取和設置，而不是通過實例
```swift
struct Car {
    static var wheel = 4
    var mileage = 3000
}

let myCar = Car() // 建立結構實例

print(myCar.mileage) // 使用「實體名稱」來取得「儲存屬性」
print(Car.wheel)     // 使用「型別名稱」來取得「型別屬性」

/*
輸出：
3000
4
*/
```

---