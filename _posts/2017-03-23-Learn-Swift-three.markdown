---
title: "Learn Swift Three"
layout: post
date: 2017-03-23 20:13
tag: iOS
category: blog
author: stonen
---

# 条件判断和循环控制

### 条件判断

- if…else…

- switch…case...

  > 两点特别：
  >
  > 1. case语句必须exhaustive，即必须覆盖switch后表达式中的所有情况。当不需要处理其他情况时，要加上只包含一句break的default
  > 2. 默认情况下每个case后直接break，不用显式指定
  > 3. 在一个case里使用多个条件时，可以用逗号分隔开

### 循环控制

- for element in collection/range

  ```swift
  // for...in...常用于遍历集合类型，如：
  let array = [1, 2, 3, 4, 5]
  for value in array {
    print(value)
  }
  ```

  > 传统的三段式for循环在swift3中被移除

- while … {} / repeat {} while ...

  > repeat while == do while in C／java



# 样式匹配

### 匹配值的方式

- 使用 `case 匹配的值 = 要检查的对象` 的方式来进行判断

  ```swift
  // 用在条件判断语句中
  let point = (0, 0)
  if case (0, 0) = point {
    print("point是原点")
  }

  - 使用 `case value in collection/range` 的方式判断

    ~~~swift
    // 用在循环语句，控制循环条件
    let array = [0, 1, 0, 1, 0]
    for case 0 in array {
      print("0") // 打印3个0
    }
    // 用0和array的每个值依次比较，==则进入循环体否则直接作下次循环判断
  ```

### 把匹配的内容绑定到变量

- 直接绑定变量自身的值（value binding）

  ```swift
  // 通过case后的let x／let y直接绑定switch中的横／纵坐标，并在随后直接使用
  switch (0, 1) {
  case (let x, 0):
      print("(\(x), 0)")
  case (0, let y):
      print("(0, \(y))")
  default:
      break
  }
  ```


- 绑定到 enum 中的关联值

  ```swift
  enum Direction {
      case north, south, east, west(abbr: String)
  }

  let west = Direction.west(abbr: "W")

  if case .west = west {
      print(west) // west("W")
  }

  if case .west(let direction) = west {
      print(direction) // W
  }
  ```

### 自动提取optional的值

- 使用case来自动提取optional的非空值

  ```swift
  let skills: [String?] = 
      ["Swift", nil, "PHP", "JavaScirpt", nil]

  for case let skill? in skills {
      print(skill) // Swift PHP JavaScript
  }
  ```

### 自动绑定类型转换的结果

- 通过使用case来匹配对应类型

  ```swift
  for value in someValues {
      switch value {
      case let v as Int:
          print("Integer \(v)")
      case let v as Double:
          print("Double \(v)")
      case let v as String:
          print("String \(v)")
      default:
          print("Invalid value")
      }
  }
  // Integer 1
  // Double 1.0
  // String One
  ```

- 或如果你仅仅想判断类型，而不需要知道具体内容的话，还可以使用更简单的is操作符：

  ```swift
  for value in someValues {
  	switch value {
  	case is Int:
      	print("Integer")
  	// omit for simplicity...
  }
  ```

### 使用where约束条件

-  除了使用具体的数值对循环或分支条件进行约束外，还可以使用`where`进行更复杂的约束

  ```swift
  for i in 1...10 where i % 2 == 0 {
      print(i)
  }
  ```

- 在value bindling中使用 `where` 来进一步约束enum的Associated Value

  ```swift
  enum Power {
      case fullyCharged
      case normal(percentage: Double)
      case outOfPower
  }
  let battery = Power.normal(percentage: 0.1)

  switch battery {
  case .normal(let percentage) where percentage <= 0.1:
      print("Almost out of power") 
  case .normal(let percentage) where percentage >= 0.8:
      print("Almost fully charged")
  default:
      print("Normal battery status")
  }
  ```

### 逗号的使用

- 在 `switch...case...` 中表示逻辑或

  ```swift
  switch value {
  	case A,B:
  		print("是A，或是B")
  }
  ```

- 在 `if` 中表示逻辑与

  ```swift
  if A,B {
  	print("A，B均真")
  }
  ```

### 使用 `Tuple` 简化多个条件的比较

-  使用 if 来判断多个条件是这样的

  ```swift
  if A == a && B == b && C == c {
    	print("ABC同时满足")
  }
  ```

- 上面的情况可以使用case (tuple) = (tuple)来替代

  ```swift
  if case (a, b, c) = (A, B, C) {
    	print("ABC同时满足")
  }
  ```

> 由于swift标准库中只实现了 `Range ~= Value` 这种形式的比较，所以，当我们使用case的模式匹配时，只能把要匹配的值写在等号左边，把被匹配的对象写在等号右边。
>
> 如上例中：假如换成 `case (A, B, C) = (a, b, c)` 编译器就会报错！

