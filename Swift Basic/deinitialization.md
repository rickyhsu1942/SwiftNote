# Deinitialization 解構過程
---
內容：
* [解構過程原理](#how_deinitialization_works)
* [析構函式操作](#deinitializers_in_action)

---
在一個類別的實例被釋放之前，解構函式被立即呼叫。用關鍵字`deinit`來標示解構函式。解構函式只適用於類型別。

## <a name='how_deinitialization_works'></a> 解構過程原理

一般Swift會自動幫我們管理記憶體的使用(透過自動參考計數ARC)。但在某些時候，你可能需要進行一些額外的清理。例如，如果創建了一個自定義的類別來打開一個文件，並寫入一些資料，你可能需要在類別實例被釋放之前關閉該文件。
<br>
<br>
一個類別最多只能有一個解構器，而且不能穿入參數
```swift
deinit {
    // 執行解構過程
}
```
解構函式是在實例釋放發生前一步被自動呼叫。不允許主動呼叫自己的解構函式。子類別繼承了父類別的解構函式，並且在子類別解構函式實作的最後，父類別的解構函式被自動呼叫。即使子類別沒有提供自己的解構函式，父類別的解構函式也總是被呼叫。

---

## <a name='deinitializers_in_action'></a> 析構函式操作

以簡單範例觀察解構器的呼叫順序
```swift
class aClass {
    var a = 10
    deinit {
        print("A Class 解構器，a:\(a)")
    }
}

class bClass: aClass {
    var b = 20
    deinit {
        print("B Class 解構器，a:\(a) b:\(b)")
    }
}

class cClass: bClass {
    var c = 30
    deinit {
        print("C Class 解構器，a:\(a) b:\(b) c:\(c)")
    }
}

var myClass: cClass? = cClass()
myClass = nil

/*
C Class 解構器，a:10 b:20 c:30
B Class 解構器，a:10 b:20
A Class 解構器，a:10
*/
```