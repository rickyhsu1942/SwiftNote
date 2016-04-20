# Optional Chaining 可選鏈
---
內容：
* [使用可選鏈替代強制解析](#optional_chaining_as_an_alternative_to_forced_unwrapping)
* [為可選鏈定義模型類別](#defining_model_classes_for_optional_chaining)
* [使用可選鏈呼叫屬性](#calling_properties_through_optional_chaining)
* [使用可選鏈呼叫方法](#calling_methods_through_optional_chaining)
* [使用可選鏈呼叫下標](#calling_subscripts_through_optional_chaining)
* [多階層的可選鏈](#linking_multiple_levels_of_chaining)
* [可選鏈與回傳可選的方法](#chaining_on_methods_with_optional_return_values)

---

可選鏈（Optional Chaining）是一種可以請求和呼叫屬性、方法及子腳本的過程，它的可選性體現於請求或呼叫的目標當前可能為空（nil）。如果可選的目標有值，那麼呼叫就會成功；相反，如果選擇的目標為空（nil），則這種呼叫將回傳空（nil）。多次請求或呼叫可以被鏈接在一起形成一個鏈，如果任何一個節點為空（nil）將導致整個鏈失效。

## <a name='optional_chaining_as_an_alternative_to_forced_unwrapping'></a> 使用可選鏈替代強制解析

通過在想呼叫的屬性、方法、或子腳本的可選值（optional value）（非空）後面放一個問號，可以定義一個可選鏈。

```swift
class Person {
    var residence: Residence?
}

class Residence {
    var numberOfRooms = 1
}

let john = Person() 
// 它的residence屬性由於是被定義為可選型的，此屬性將預設初始化為空

let roomCount = john.residence!.numberOfRooms
//將導致執行時錯誤
```
可選鏈提供了一種另一種獲得numberOfRooms的方法。利用可選鏈，使用問號來代替原來!的位置
```swift
if let roomCount = john.residence?.numberOfRooms {
    print("John's residence has \(roomCount) room(s).")
} else {
    print("Unable to retrieve the number of rooms.")
}
// 輸出: Unable to retrieve the number of rooms.
```
假設現在定義一個`Residence`實例給`john.residence`
```swift
john.residence = Residence() // 此時已經不為空的

if let roomCount = john.residence?.numberOfRooms {
    print("John's residence has \(roomCount) room(s).")
} else {
    print("Unable to retrieve the number of rooms.")
}
// 輸出: John's residence has 1 room(s).
```

---

## <a name='defining_model_classes_for_optional_chaining'></a> 為可選鏈定義模型類別

你可以使用可選鏈來多層呼叫屬性，方法，和子腳本。這讓你可以利用它們之間的複雜模型來獲取更底層的屬性，並檢查是否可以成功獲取此類別底層屬性。
```swift
class Person {
    var residence: Residence?
}

class Residence {
    var rooms = Room[]()
    var numberOfRooms: Int {
    return rooms.count
    }
    subscript(i: Int) -> Room {
        return rooms[i]
    }
    func printNumberOfRooms() {
        print("The number of rooms is \(numberOfRooms)")
    }
    var address: Address?
}

class Room {
    let name: String
    init(name: String) { self.name = name }
}

class Address {
    var buildingName: String?
    var buildingNumber: String?
    var street: String?
    func buildingIdentifier() -> String? {
        if buildingName {
            return buildingName
        } else if buildingNumber {
            return buildingNumber
        } else {
            return nil
        }
    }
}
```
`Address`類別還提供了一個`buildingIdentifier`的方法，它的回傳值型別為`String?`。這個方法檢查`buildingName`和`buildingNumber`的屬性，如果`buildingName`有值則將其回傳，或者如果`buildingNumber`有值則將其回傳，再或如果沒有一個屬性有值，回傳`nil`。

---

## <a name='calling_properties_through_optional_chaining'></a> 使用可選鏈呼叫屬性

利用可選鏈存取屬性，並檢查可選鏈是否取得成功。
```swift
let john = Person()
if let roomCount = john.residence?.numberOfRooms {
    print("John's residence has \(roomCount) room(s).")
} else {
    print("Unable to retrieve the number of rooms.")
}
// 輸出: Unable to retrieve the number of rooms.
```

---

## <a name='calling_methods_through_optional_chaining'></a> 使用可選鏈呼叫方法

你可以使用可選鏈的來呼叫可選值的方法並檢查方法呼叫是否成功。即使這個方法沒有回傳值，你依然可以使用可選鏈來達成這一目的。

```swift
func printNumberOfRooms(){
    print("The number of rooms is \(numberOfRooms)")
}
```
```swift
if john.residence?.printNumberOfRooms() {
    print("It was possible to print the number of rooms.")
} else {
    print("It was not possible to print the number of rooms.")
}
// 輸出: It was not possible to print the number of rooms.
```

---

## <a name='calling_subscripts_through_optional_chaining'></a> 使用可選鏈呼叫下標

你可以使用可選鏈來嘗試從下標獲取值並檢查下標的呼叫是否成功，然而，你不能通過可選鏈來設置子程式碼。

>注意：<br>
當使用可選鏈呼叫下標時，要將問號放在下標的前面而非後面。`someVal?[Index]`，而非`someVal[Index]?`

```swift
if let firstRoomName = john.residence?[0].name {
    print("The first room name is \(firstRoomName).")
} else {
    print("Unable to retrieve the first room name.")
}
// 輸出: Unable to retrieve the first room name.
```

```swift
let johnsHouse = Residence()
johnsHouse.rooms += Room(name: "Living Room")
johnsHouse.rooms += Room(name: "Kitchen")
john.residence = johnsHouse

if let firstRoomName = john.residence?[0].name {
    print("The first room name is \(firstRoomName).")
} else {
    print("Unable to retrieve the first room name.")
}
// 輸出: The first room name is Living Room.
```

---

## <a name='linking_multiple_levels_of_chaining'></a> 多階層的可選鏈
``` swift
if let johnsStreet = john.residence?.address?.street {
    print("John's street name is \(johnsStreet).")
} else {
    print("Unable to retrieve the address.")
}
// 輸出: Unable to retrieve the address.
```
```swift
let johnsAddress = Address()
johnsAddress.buildingName = "The Larches"
johnsAddress.street = "Laurel Street"
john.residence!.address = johnsAddress

if let johnsStreet = john.residence?.address?.street {
    print("John's street name is \(johnsStreet).")
} else {
    print("Unable to retrieve the address.")
}
// 輸出: John's street name is Laurel Street.
```
值得注意的是，「!」符號在給`john.residence.address`分配`address`實例時的使用。`john.residence`屬性是一個可選型別，因此你需要在它獲取`address`屬性之前使用`!`解析以獲得它的實際值。

---

## <a name='chaining_on_methods_with_optional_return_values'></a> 可選鏈與回傳可選的方法

前面的範例解釋了如何通過可選鏈來獲得可選型別屬性值。你也可以通過可選鏈呼叫一個回傳可選型別值的方法並按需鏈接該方法的回傳值。

```swift
if let buildingIdentifier = john.residence?.address?.buildingIdentifier() {
    print("John's building identifier is \(buildingIdentifier).")
}
// 輸出: John's building identifier is The Larches.
```
如果你還想進一步對方法回傳值執行可選鏈，將可選鏈問號符放在方法括號的後面
```swift
if let upper = john.residence?.address?.buildingIdentifier()?.uppercaseString {
    print("John's uppercase building identifier is \(upper).")
}
// 輸出: John's uppercase building identifier is THE LARCHES.
```
>注意：<br>
在上面的範例中，你將可選鏈問號符放在括號後面是因為你想要鏈接的可選值是buildingIdentifier方法的回傳值，不是buildingIdentifier方法本身。

---