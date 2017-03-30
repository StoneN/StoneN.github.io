---
title: "Learn Swift Two"
layout: post
date: 2017-03-17 11:40
tag: iOS
category: blog
author: stonen
---

# Array

- Array initialization

  ```swift
  // Create an empty array
  var array1: Array<Int> = Array<Int>()
  var array2: [Int] = []
  var array3 = array2

  // Initialization with values
  var threeInts = [Int](repeating: 3, count: 3) // [3,3,3]
  var sixInts = threeInts + threeInts // [3,3,3,3,3,3]
  var fiveInts = [1,2,3,4,5]
  ```

- Common attributes

  ```swift
  array.count
  array.isEmpty
  ```

- Access element

  ```swift
  var index: Int
  var array: [Int]
  array[index] // 下表越界会抛出Runtime error，不建议使用
  array[0 ... 3] // 返回ArraySlice<Int>.Type
  /*
   *	Array包含：一个指向数组的指针 & 数组长度
   *	ArraySlice包含：一个指向数组的指针 & beginIndex & endIndex
   */

  // Traverse 遍历数组
  for value in array {
    ...
  }
  array.forEach { ...$0... }
  for (index, value) in array.enumerated() {
    ...
  }

  // Array modification
  array.append(1) // 末尾插入1
  array += [1,2,3] // 末尾插入1，2，3
  array.insert(0, at: array.startIndex) // 头部插入0，at必须合法
  array.remove(at: 0) // at必须合法
  array.removeLast() // array不为空
  array.popLast() // 删除最后一个值，当array为空时返回nil

  // 其他操作
  let oneIndex = array.index { $0 == 1 } //查找元素下标，不存在时返回nil
  let filterArray = array.filter { $0 % 2 == 0 } // 筛选，去掉不满足条件的元素，返回一个新Array
  array.first // array为空则返回nil
  array.last
  array.min() // array为空则返回nil
  array.max()
  array.elementsEqual([0,1,2]) // 当两个数组完全一样(包括内部元素的相对位置)时返回true
  array.elementsEqual([0,1,2], by: { $0 > $1 }) // 自定义比较规则
  array.starts(with: [0,1,2]) // 当array以[0,1,2]开头时返回true
  array.starts(with: [0,1,2], by: { $0 == $1 }) 
  array.sorted() // 排序，默认升序
  array.sorted(by: >) // 降序，'>'即'$0 > $1'的简写
  array.partition(by: { $0 < 1}) 
  // partition在原数组上操作，使它的前一部分不满足条件，后一部分满足条件，并返回分割点索引
  array.reduce(0, +) // 对array元素求和，'+'也是简写
  ```

- Array VS NSArray

  ```swift
  // Array的拷贝：copy on write
  var a = [1,2,3]
  let copyA = a
  // 目前为止 a 和 copyA 指向同一个内存空间
  a.append(4) // 此时两者被分开，a=[1,2,3,4]; copyA=[1，2，3]
  copyA.append(4) //❌ 用'let'创建的是常量数组，不可改变

  // NSArray的拷贝：引用语义
  let b = NSMutableArray(array: [1,2,3])
  let copyB: NSArray = b // 浅拷贝，b 和 copyB 指向同一个地址空间，更改 b 则 copyB 也会跟着变
  let deepCopyB = b.copy() as! NSArray // 深拷贝，b 和 deepCopyB 互不相干
  //NSMutableArray: 可变数组； NSArray: 常量数组， 与'let/var'无关
  ```

- array.map: 把array中的元素进行某种处理后返回

  ```swift
  // 将array的每个值平方之后，赋值给一个常量数组：
  let constSquares = array.map { $0 * $0 }

  // 实现自己的map方法：myMap
  extension Array {
    func myMap<T>(_ transform: (Element) -> T) -> [T] {
      var tmp: [T] = []
      tmp.reserveCapacity(count)
      for value in self {
        tmp.append(transform(value))
      }
      return tmp
    }
  }

  // flatMap：返回单个数组
  array1.flatMap { value1 in
    return array2.map { id in
    	(value1, value2)
    }
  } // [(value1, value2)...]
  ```

  ​

