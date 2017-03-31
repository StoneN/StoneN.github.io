---
title: "Swift enmu"
layout: post
date: 2017-03-22 23:10
tag: iOS
category: blog
author: stonen
---

> enum 的意义：把一组具有相关意义的值定义成一个独立的类型。

**\*Swift 对 enmu 类型做了诸多改进与增强。正如官方文档里写的 `Enumerations in Swift are first-class types in their own right.` ,它更像是一个class，可以有自己的属性、方法，还可以遵从protocol。\***

### 定义一个enum

```swift
// 1. 每个case对应一个值
enum Direction {
    case east
    case south
    case west
    case north
}
// 2. 一个case后跟多个值，用逗号分隔
enum Month {
    case january, februray, march, 
        april, may, june, july,
        august, september, october, 
        november, december
}
// 3. 使用
var directionToHead = Direction.west
// Once directionToHead is declared as a Direction, you can set it to a different Direction value using a shorter dot syntax:
directionToHead = .east
```

### Raw value

> Swift 的 enum 和 C／OC 不同，它默认不会为每个 case 绑定一个整数值。但是我们可以通过 `Row value`
>
> 来手工为 case 绑定一个 `default values`

```swift
// 为每个case绑定一个整数值，就像C语言那样：
enum Direction: Int {
    case east
    case south
    case west
    case north
}	
Direction.south.rawValue // 1

// 这样Swift会自动依次为每个case绑定上：0，1，2，3。当然我们也可以手动设置：

enum Direction: Int {
    case east  = 2
    case south = 4
    case west = 6 
    case north = 8
}
Direction.south.rawValue // 4

// 当只为第一个赋值时，Swift会自增的为后面的每个case绑定rawValue：

enum Month: Int {
    case January = 1, Februray, March, 
        April, May, June, July,
        August, September, October, 
        November, December
}
Month.June.rawValue // 6

// 当然RawValue的类型不局限于Int，你可以用任何类型，但每个case绑定的都得是同一类型，且每个值必须是唯一的
// 当使用String作为RawValue时，默认的值是case's name本身

enum ASCIIControlCharacter: Character {
    case tab = "\t"
    case lineFeed = "\n"
    case carriageReturn = "\r"
}

// 当定义一个具有RawValue的enum，它就自动接受一个`failable initializer`，它接受一个rawValue的参数，返回enum？，可以使用它来实例化enum：

let JUNE = Month(rawValue: 6) // 相当于 let JUNE = Month.June

// 上例返回 Month？当rawValue的值在Month中不存在，该构造器返回nil
```

### Associated Values

> 给 case 绑定各自的 `Associated Values` 数据类型，在使用时才赋值

```swift
enum Barcode {
    case upc(Int, Int, Int, Int)
    case qrCode(String)
}
var productBarcode = Barcode.upc(8, 85909, 51226, 3)
productBarcode = .qrCode("ABCDEFGHIJKLMNOP")

// 使用let／var来提取 AssociatedValues

switch productBarcode {
case .upc(let numberSystem, let manufacturer, let product, let check):
    print("UPC: \(numberSystem), \(manufacturer), \(product), \(check).")
case .qrCode(let productCode):
    print("QR code: \(productCode).")
}

// 当某个 case 的 AssociatedValue 中的所有值都被提取时，可把let／var提取出来：

switch productBarcode {
case let .upc(numberSystem, manufacturer, product, check):
    print("UPC : \(numberSystem), \(manufacturer), \(product), \(check).")
case let .qrCode(productCode):
    print("QR code: \(productCode).")
}
```

- 不是每一个`case`必须有associated value；
- 当我们想“提取”associated value的所有内容时，我们可以把`let`或`var`写在`case`后面；
- 当我们想分别“提取”associated value中的某些值时，我们可以把`let`或`var`写在associated value里面；

### Recursive Enumerations

> enum是一个值类型，每一个enum对象都只能有一个owner，因此你无法创建指向同一个enum对象的多个引用。但使用 `indirect` 关键字来指定引用类型的enum case后，当enum对象对应于一个 indirect case 时，它就变成了一个引用类型

```swift
enum ArithmeticExpression {
    case number(Int)
    indirect case addition(ArithmeticExpression, ArithmeticExpression)
    indirect case multiplication(ArithmeticExpression, ArithmeticExpression)
}

// You can also write indirect before the beginning of the enumeration, to enable indirection for all of the enumeration’s cases that need it:

indirect enum ArithmeticExpression {
    case number(Int)
    case addition(ArithmeticExpression, ArithmeticExpression)
    case multiplication(ArithmeticExpression, ArithmeticExpression)
}

// The code below shows the ArithmeticExpression recursive enumeration being created for (5 + 4) * 2:

let five = ArithmeticExpression.number(5)
let four = ArithmeticExpression.number(4)
let sum = ArithmeticExpression.addition(five, four)
let product = ArithmeticExpression.multiplication(sum, ArithmeticExpression.number(2))

// 使用递归函数可以简单的处理递归数据：

func evaluate(_ expression: ArithmeticExpression) -> Int {
    switch expression {
    case let .number(value):
        return value
    case let .addition(left, right):
        return evaluate(left) + evaluate(right)
    case let .multiplication(left, right):
        return evaluate(left) * evaluate(right)
    }
}

print(evaluate(product))
// Prints "18"
```

