# Inheritance 繼承
---
內容：
* [定義一個基礎類別 (Base class)](#defining_a_base_class)
* [子類別生成 (Subclassing)](#subclassing)
* [重寫 (Overriding)](#overriding)
* [防止重寫 (Preventing Overrides)](#preventing_overrides)

---

一個類別可以繼承(inherit)另一個類別的方法(methods)，屬性(property)和其它特性。當一個類別繼承其它類別時，繼承類別叫子類別(subclass)，被繼承類別叫超類別(父類別，superclass)

## <a name='defining_a_base_class'></a> 定義一個基礎類別 (Base class)

不繼承於其他類別，稱為基礎類別
```swift
class Vehicle {
    var numberOfWheels: Int
    var maxPassengers: Int
    func description() -> String {
        return "\(numberOfWheels) wheels; up to \(maxPassengers) passengers"
    }
    init() {
        numberOfWheels = 0
        maxPassengers = 1
    }
}
```

---

## <a name='subclassing'></a> 子類別生成 (Subclassing)

在一個已有類別的基礎上創建一個新的類別。子類別繼承超類別的特性，並且可以優化或改變它。你還可以為子類別添加新的特性
```swift
class SomeClass: SomeSuperclass {
    // 類別的定義
}
```
```swift
class Bicycle: Vehicle {
    override init() {
        super.init()
        numberOfWheels = 2
    }
}

let bicycle = Bicycle()
print("Bicycle: \(bicycle.description())")
// Bicycle: 2 wheels; up to 1 passengers
```

## <a name='overriding'></a> 重寫 (Overriding)

子類別可以為繼承來的實例方法(instance method)，類別方法(class method)，實例屬性(instance property)，或下標腳本(subscript)提供自己定制的實作(implementation)

如果要重寫某個特性，你需要在重寫定義的前面加上`override`關鍵字

### 重寫方法
```swift
class Car: Vehicle {
    var speed: Double = 0.0
    override init() {
        super.init()
        maxPassengers = 5
        numberOfWheels = 4
    }
    override func description() -> String {
        return super.description() + "; "
            + "traveling at \(speed) mph"
    }
}

let car = Car()
print("Car: \(car.description())")
// Car: 4 wheels; up to 5 passengers; traveling at 0.0 mph
```

### 重寫屬性
你可以重寫繼承來的實例屬性或類別屬性，提供自己定制的getter和setter，或添加屬性觀察器使重寫的屬性觀察屬性值什麼時候發生改變

#### 重寫屬性觀察器

```swift
class SpeedLimitedCar: Car {
    override var speed: Double  {
        get {
            return super.speed
        }
        set {
            super.speed = min(newValue, 40.0)
        }
    }
}
```

>注意：<br>
如果你在重寫屬性中提供了 setter，那麼你也一定要提供 getter。如果你不想在重寫版本中的 getter 裡修改繼承來的屬性值，你可以直接回傳super.someProperty來回傳繼承來的值。

#### 重寫屬性觀察器
```swift
class AutomaticCar: Car {
    var gear = 1
    override var speed: Double {
        didSet {
            gear = Int(speed / 10.0) + 1
        }
    }
    override func description() -> String {
        return super.description() + " in gear \(gear)"
    }
}

let automatic = AutomaticCar()
automatic.speed = 35.0
print("AutomaticCar: \(automatic.description())")
// AutomaticCar: 4 wheels; up to 5 passengers; traveling at 35.0 mph in gear 4
```

>注意：<br>
你不可以為繼承來的常數儲存型屬性或繼承來的唯讀計算型屬性添加屬性觀察器。此外還要注意，你不可以同時提供重寫的 setter 和重寫的屬性觀察器。

---

## <a name='preventing_overrides'></a> 防止重寫 (Preventing Overrides)

如果不想讓別人覆寫你的屬性、方法、類別或下標，加上關鍵字`final`，例如：
* final var
* final func
* final class func
* final subscript

---