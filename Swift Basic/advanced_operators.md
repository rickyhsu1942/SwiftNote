# Advanced Operators 進階運算子
---
內容:
* [位元運算子 (Bitwise Operators)](#bitwise_operators)
* [溢位運算子 (Overflow Operators)](#overflow_operators)
* [優先級和結合性 (Precedence and Associativity)](#precedence_and_associativity)
* [運算子函式 (Operator Functions)](#operator_functions)

---

## <a name='bitwise_operators'></a> 位元運算子 (Bitwise Operators)

位元運算子通常在諸如圖像處理和創建設備驅動等底層開發中使用，使用它可以單獨運算元據結構中原始資料的位元。在使用一個自定義的協定進行通信的時候，運用位元運算子來對原始資料進行編碼和解碼也是非常有效的。

### 位元補數運算子 (Bitwise NOT Operator)
位元補數運算子`~`對一個運算元的每一位都取補數。
![bitwiseNOT_2x](Picture/bitwiseNOT_2x.png)
```swift
let initialBits: UInt8 = 0b00001111
let invertedBits = ~initialBits  // 11110000
```

### 位元 AND 運算子 (Bitwise AND Operator)

位元 AND 運算子對兩個數進行操作，然後回傳一個新的數，這個數的每個位元都需要兩個輸入數的同一位元都為 1 時才為 1。
![bitwiseAND_2x](Picture/bitwiseAND_2x.png)
```swift
let firstSixBits: UInt8 = 0b11111100
let lastSixBits: UInt8  = 0b00111111
let middleFourBits = firstSixBits & lastSixBits  // 00111100
```

### 位元 OR 運算 (Bitwise OR Operator)

位元 OR 運算子`|`比較兩個數，然後回傳一個新的數，這個數的每一位元設置1的條件是兩個輸入數的同一位元都不為0。
![bitwiseOR_2x](Picture/bitwiseOR_2x.png)
```swift
let someBits: UInt8 = 0b10110010
let moreBits: UInt8 = 0b01011110
let combinedbits = someBits | moreBits  // 11111110
```

### 位元 XOR 運算子 (Bitwise XOR Operator)

位元 XOR 運算子`^`比較兩個數，然後回傳一個數，這個數的每個位元設為1的條件是兩個輸入數的同一位元不同，如果相同就設為0。
![bitwiseXOR_2x](Picture/bitwiseXOR_2x.png)
```swift
let firstBits: UInt8 = 0b00010100
let otherBits: UInt8 = 0b00000101
let outputBits = firstBits ^ otherBits  // 00010001
```

### 移位運算子 (Shift Operator)
左移運算子`<<`和右移運算子`>>`會把一個數的所有位元按以下定義的規則向左或向右移動指定位數。
<br><br>
按位左移和按位右移的效果相當把一個整數乘於或除於一個因數為2的整數。向左移動一個整數的位元相當於把這個數乘於2，向右移一位就是除於2。

* <h4>無號整數的移位運算</h4>
如圖展示了`11111111 << 1`（11111111向左移 1 位），和 `11111111 >> 1`（11111111向右移1位）。藍色的是被移位的，灰色是被拋棄的，橙色的0是被填充進來的。
![bitshiftUnsigned_2x](Picture/bitshiftUnsigned_2x.png)
```swift
let shiftBits: UInt8 = 4   // 00000100 in binary
shiftBits << 1             // 00001000
shiftBits << 2             // 00010000
shiftBits << 5             // 10000000
shiftBits << 6             // 00000000
shiftBits >> 2             // 00000001
```

* <h4>有號整數的移位運算</h4>
有號整數的移位運算相對複雜得多，因為正負號也是用二進制位表示的。(這裡舉的範例雖然都是 8 位的)
<br><br>
有號整數通過第 1 個位元 (稱為符號位，sign bit) 來表達這個整數是正數還是負數。0代表正數，1代表負數。
![bitshiftSignedFour_2x](Picture/bitshiftSignedFour_2x.png)
符號位為0，代表正數，另外7個位元的二進制表示的實際值就剛好是4。
<br><br>
負數跟正數不同。負數儲存的是 2 的 n 次方減去它的絕對值，n 為數值位的位數。一個 8 位元的數有 7 個數值位，所以是 2 的 7 次方，即128。
![bitshiftSignedMinusFour_2x](Picture/bitshiftSignedMinusFour_2x.png)
負數的編碼方式稱為二進制補數表示。這種表示方式看起來很奇怪，但它有幾個優點。<br>
首先，只需要對全部8個位元(包括符號)做標準的二進制加法就可以完成 -1 + -4 的運算，忽略加法過程產生的超過8個位元表達的任何資訊。
![bitshiftSignedAddition_2x](Picture/bitshiftSignedAddition_2x.png)

---

## <a name='overflow_operators'></a> 溢位運算子 (Overflow Operators)

預設情況下，一個整數常數或變數被賦予一個它不能容納的大數時，Swift 會回報錯誤。這在操作過大或過小的數的時候很安全。
```swift
var potentialOverflow = Int16.max
// potentialOverflow 等於 32767, 這是 Int16 能容納的最大整數
potentialOverflow += 1
// 出錯
```
當然，你有意在溢位時對有效位進行截斷時，可以使用溢位運算，而非錯誤處理。Swfit 為整數計算提供了 3 個`&`符號開頭的溢位運算子。
* 溢位加法 `&+`
* 溢位減法 `&-`
* 溢位乘法 `&*`

### 值的上溢位

![overflowAddition_2x](Picture/overflowAddition_2x.png)

```swift
var willOverflow = UInt8.max
// willOverflow 等於 UInt8 的最大整數 255
willOverflow = willOverflow &+ 1
// 此時 willOverflow 等於 0
```

### 值的下溢位

![overflowUnsignedSubtraction_2x](Picture/overflowUnsignedSubtraction_2x.png)
```swift
var willUnderflow = UInt8.min
// willUnderflow 等於 UInt8 的最小值 0
willUnderflow = willUnderflow &- 1
// 此時 willUnderflow 等於 255
```
有號整數的下溢位<br>
![overflowSignedSubtraction_2x](Picture/overflowSignedSubtraction_2x.png)
```swift
var signedUnderflow = Int8.min
// signedUnderflow 等於最小的有號整數 -128
signedUnderflow = signedUnderflow &- 1
// 此時 signedUnderflow 等於 127
```

---

## <a name='precedence_and_associativity'></a> 優先級和結合性 (Precedence and Associativity)

運算子的優先級使得一些運算子優先於其他運算子，高優先級的運算子會先被計算。
<br><br>
在混合表達式中，運算子的優先級和結合性是非常重要的。舉個範例
```swift
2 + 3 % 4 * 5
// this equals 17
```
乘法和取餘擁有相同的優先級，在運算過程中，我們還需要結合性，乘法和取餘運算都是左結合的。這相當於在表達式中有隱藏的括號讓運算從左開始。
```swift
2 + ((3 % 4) * 5)
```
計算結果為17

---

## <a name='operator_functions'></a> 運算子函式 (Operator Functions)

讓已有的運算子也可以對自定義的類別和結構進行運算，這稱為運算子重載。

範例中定義了一個名為Vector2D的二維坐標向量(x,y)的結構，然後定義了讓兩個Vector2D的物件相加的運算子函式。
```swift
struct Vector2D {
    var x = 0.0, y = 0.0
}
func + (left: Vector2D, right: Vector2D) -> Vector2D {
    return Vector2D(x: left.x + right.x, y: left.y + right.y)
}
```
這個函式是全域的，而不是Vector2D結構的成員方法，所以任意兩個Vector2D物件都可以使用這個中綴運算子。
```swift
let vector = Vector2D(x: 3.0, y: 1.0)
let anotherVector = Vector2D(x: 2.0, y: 4.0)
let combinedVector = vector + anotherVector
// combinedVector 是一個新的 `Vector2D`，值為 (5.0, 5.0)
```
![vectorAddition_2x](Picture/vectorAddition_2x.png)

### 前綴和後綴運算子

一元 上個範例演示了一個二元中綴運算子的自定義實作，同樣我們也可以實作標準一元運算子。一元運算子只有一個運算元，在運算元之前就是前綴的，如`-a`; 在運算元之後就是後綴的，如`i++`。
<br><br>
實作一個前綴或後綴運算子時，在定義該運算子的時候於關鍵字func之前標注`prefix`或`postfix`屬性。
```swift
prefix func - (vector: Vector2D) -> Vector2D {
    return Vector2D(x: -vector.x, y: -vector.y)
}
```
這段程式碼為Vector2D型別提供了一元減法運算`-a`，`prefix`屬性表明這是個前綴運算子。
```swift
let positive = Vector2D(x: 3.0, y: 4.0)
let negative = -positive
// negative 為 (-3.0, -4.0)
let alsoPositive = -negative
// alsoPositive 為 (3.0, 4.0)
```

### 組合賦值運算子

組合賦值是其他運算子和賦值運算子一起執行的運算。例如+=把加運算和賦值運算組合成一個運算。需要把運算子的左參數設置成`inout`，因為這個參數會在運算子函式內直接修改它的值。
```swift
func += (inout left: Vector2D, right: Vector2D) {
    left = left + right
}
```
因為加法運算在之前定義過了，這裡無需重新定義。所以，加賦運算子函式使用已經存在的加法運算子函式來執行左值加右值的運算。
```swift
var original = Vector2D(x: 1.0, y: 2.0)
let vectorToAdd = Vector2D(x: 3.0, y: 4.0)
original += vectorToAdd
// original 現在為 (4.0, 6.0)
```
你可以將加上`prefix`或`postfix`屬性，實作一個Vector2D的前綴運算子。
```swift
prefix func ++ (inout vector: Vector2D) -> Vector2D {
    vector += Vector2D(x: 1.0, y: 1.0)
    return vector
}

var toIncrement = Vector2D(x: 3.0, y: 4.0)
let afterIncrement = ++toIncrement
// toIncrement 現在是 (4.0, 5.0)
// afterIncrement 現在也是 (4.0, 5.0)

```
>注意：<br>
預設的賦值運算子是不可重載的。只有組合賦值運算子可以重載。三元條件運算子a？b：c也是不可重載

### 相等運算子 (Equivalence Operators)

自定義的類別或結構沒有預設的「相等（==）」或「不相等（!=）」這樣的相等運算子，所以自定義的類別和結構要使用相等運算==或!=就需要重載。
```swift
func == (left: Vector2D, right: Vector2D) -> Bool {
    return (left.x == right.x) && (left.y == right.y)
}
func != (left: Vector2D, right: Vector2D) -> Bool {
    return !(left == right)
}

let twoThree = Vector2D(x: 2.0, y: 3.0)
let anotherTwoThree = Vector2D(x: 2.0, y: 3.0)
if twoThree == anotherTwoThree {
    print("These two vectors are equivalent.")
}
// 輸出 These two vectors are equivalent.
```

### 自定義運算子

你可以宣告一些自定義的運算子，新的運算子宣告需在全域使用operator關鍵字宣告，可以宣告為前綴，中綴或後綴的。
```swift
prefix operator +++ {}
```
這段程式碼定義了一個新的前綴運算子叫+++，在這之前 Swift 並不存在這個運算子。為了示範，我們讓+++對Vector2D物件的運算定義為「加倍」 (doubling incrementer) 這樣一個獨有的操作，這個操作使用了之前定義的加法賦值運算來實作自已加上自己然後回傳的運算。
```swift
prefix func +++ (inout vector: Vector2D) -> Vector2D {
    vector += vector
    return vector
}

var toBeDoubled = Vector2D(x: 1.0, y: 4.0)
let afterDoubling = +++toBeDoubled
// toBeDoubled 現在是 (2.0, 8.0)
// afterDoubling 現在也是 (2.0, 8.0)
```

### 自定義中綴運算子的優先級和結合性

可以為自定義的中綴運算子指定優先級和結合性。(參考<a name'#precedence_and_associativity'>優先級和結合性</a>)
<br><br>
結合性（associativity）的定義可以是left、right或none。左結合運算子跟其他優先級相同的左結合運算子寫在一起時，會跟左邊的運算元結合。同理，右結合運算子會跟右邊的運算元結合。而非結合運算子不能跟其他相同優先級的運算子寫在一起。
<br><br>
結合性預設為none，優先級（precedence）預設為100，以下範例定義了一個新的中綴運算子+-，結合性為左結合，優先級為140。
```swift
infix operator +- { associativity left precedence 140 }
func +- (left: Vector2D, right: Vector2D) -> Vector2D {
    return Vector2D(x: left.x + right.x, y: left.y - right.y)
}
let firstVector = Vector2D(x: 1.0, y: 2.0)
let secondVector = Vector2D(x: 3.0, y: 4.0)
let plusMinusVector = firstVector +- secondVector
// plusMinusVector is a Vector2D instance with values of (4.0, -2.0)
```

---