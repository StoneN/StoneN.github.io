---
title: "Learn Swift One"
layout: post
date: 2017-03-15 21:11
tag: iOS
category: blog
author: stonen
---

- type inference **&** type annotation

  ```swift
  var x = 3
  var y:Int = 3
  ```

- 整数和浮点数

  ```swift
  // 1. 整数分：Int/UInt 以及 Int8/16/32/64 UInt8/16/32/64

  // 2. 各进制表示15：
  let shi = 15
  let ba = 0o17
  let shiLiu = 0xF
  let er = 0b1111

  // 3. 十进制表示时可用'_'作千分位分隔符，如1000 000：
  let million = 1_000_000




  // 1. 浮点数分：Float（小数点后6位）和 Double（小数点后15位）
  var zeroPointForteen = 0.14 // 默认是Double类型

  // 2. 用科学计数法表示小数：
  var PI = 314e-2	// 314*10的-2次方

  // 3. 可以把不同类型的数字的值直接相加，但不能把不同类型的变量相加：
  var PI = 3 + 0.14 // ✅
  var three = 3
  var PI = three + 0.14 // ✅
  var PI = 3 + zeroPointForteen // ✅
  var PI = three + zeroPointForteen // ❌
  var PI = Double(Three) + zeroPointForteen // ✅ 
  // 使用'Three'的值重新生成一个Double类型的值参与运算
  ```

- 字符串

  - 扩展ASCII表：// 每个字符对应8位ASCII数值，只用了7位
    - ISO/IEC 8859，启用第8位，为西欧国家扩展字符表
    - Variable length Unicode，构成同一个字符的“编码单位”组合也是可变的
  - Swift里的String不是一个集合类型，而是一个提供从多个维度展现一个字符视图的类型
    - UTF-8 View
    - UTF-16 View
    - Scalar View
    - Character collections

  ```swift
  // String不能使用下标来取字符，但它有一些API可供调用：
  let cafe = "cafe"
  let startIndex = cafe.startIndex  //第一个字符的位置索引
  let endIndex = cafe.endIndex      //最后一个字符的下一个位置索引
  let index = cafe.index(cafe.startIndex, offsetBy: 2) //String.CharacterView.Index.Type
  let index2 = cafe.index(cafe.startIndex, offsetBy: 2, limitedBy: cafe.endIndex)
  // Optional<String.CharacterView.Index>.Type

  cafe[startIndex] // "c"
  cafe[endIndex] // ❌ 
  cafe[2] // ❌
  cafe[index] // "f"
  cafe[index2!] // "f"

  // 基于unicode的字符串常用操作：
  let cafe = "cafe"
  // 1. 获取前缀
  let temp = cafe.characters.prefix(3) // String.CharacterView
  String(temp) // "caf"

  // 2. 遍历String
  var mixStr = "Swift很有趣"
  for (index, value) in mixStr.characters.enumerated() {
    print("\(index) \(value)")
  }

  // 3. 插入字符
  if let index = mixStr.characters.index(of: "很") {
    mixStr.insert(contentsOf: " 3.0".characters, at: index) // "Swift 3.0很有趣"
  } 
  if let cnIndex = mixStr.characters.index(of: "很") {
    mixStr.replaceSubrange(cnIndex ..< mixStr.endIndex, with: " is interesting!")
  } // "Swift 3.0 is interesting!"

  // 4. String的剪裁
  var pianduan = mixStr.characters.suffix(12) // 获取第12个字符之后的内容，返回String.CharacterView
  String(pianduan) // "interesting!"
  pianduan.dropLast() // String.CharacterView
  String(pianduan) // "interesting"

  // 5. String的分割
  var fenge = mixStr.characters.split(separator: " ") // Array<String.CharacterView>.Type
  fenge.map(String.init) // Array<String>.Type

  // 利用Closure将奇数位字符分割：
  var i = 0
  mixStr.characters.split { _ in
      if i > 0 {
          i = 0
          return true
      }
      else {
          i += 1
          return false
      }
  }.map(String.init)
  // ["S", "i", "t", "3", "0", "i", " ", "n", "e", "e", "t", "n", "!"]
  ```


- Tuple 用来打包数据

  ```swift
  let me = (name: "Stone", age: 22, email: "stonen.xt@gmail.com")
  // (.0 "Stone", .1 22, .2 "stonen.xt@gmail.com")
  type(of: me) // (String, Int, String).Type
  me.0 // "Stone"
  me.1 // 22
  me.email // "stonen.xt@gmail.com"

  //比较大小【只允许同类型Tuple进行比较，且元素个数<=6】
  let tuple12 = (1, 2)
  let tuple123 = (1, 2, 3)
  let tuple13 = (1, 3)
  tuple12 < tuple123 // ❌不同type不能比较
  tuple12 < tuple13  // ✅
  ```

- 操作符

  ```swift
  // Assignment operator 赋值操作符
  let a = 10

  // Arithmetic opertor 算术运算符
  let sum = a + b
  let sub = a - b
  let mul = a * b
  let div = a / b
  let mod = a % b // 不允许对浮点数取模，如：8 % 2.5 ❌
  8.truncatingRemainder(dividingBy: 2.5) // ✅表示 8 % 2.5 = 0.5

  // Compound assignment 复合运算赋值操作符
  a += 1
  a -= 1
  a *= 1
  a /= 1
  a %= 1
  // Swift不支持自加自减操作符
  a++ // ❌
  a-- // ❌
  ++a // ❌
  --a // ❌

  // Comparison 比较操作符
  a == b
  a != b
  a > b
  a < b
  a >= b
  a <= b
  a === b // 判断两个变量是否引用自同一个对象
  a !== b

  // Ternary Conditional Operator 三元条件操作符
  value ？ "true" : "false"

  // Nil Coalscing Operator 判断optional是否为nil
  let userInput: String? = "A"
  userInput ?? "isNil" // 当userInput为nil，返回'??'后的值，否则返回本身

  // Range operator
  1 ... 3 // 表示：1, 2, 3
  1 ..< 3 // 表示：1, 2

  // logic operator
  !; &&; ||
  ```

  ​

