---
layout: post
title:  "Scala list列表"
date:   2019-11-08 08:03:35 +0530
categories:  Scala

---

Scala 列表类似于数组，它们所有元素的类型都相同，但是它们也有所不同：列表是不可变的，值一旦被定义了就不能改变，其次列表 具有递归的结构（也就是链接表结构）而数组不是。列表的元素类型 T 可以写成 List[T]。例如，以下列出了多种类型的列表：

```scala

// 字符串列表
val site: List[String] = List("Runoob", "Google", "Baidu")

//简写
val site =List("Runoob", "Google", "Baidu")

// 整型列表
val nums: List[Int] = List(1, 2, 3, 4)

// 空列表
val empty: List[Nothing] = List()

// 二维列表
val dim: List[List[Int]] =
   List(
      List(1, 0, 0),
      List(0, 1, 0),
      List(0, 0, 1)
   )


```

构造列表的两个基本单位是 Nil 和 ::Nil 也可以表示为一个空列表。以上实例我们可以写成如下所示：

```scala

// 字符串列表
val site = "Runoob" :: ("Google" :: ("Baidu" :: Nil))

//简写
val site = "sas"::"sasa"::"sasasasa"::Nil

// 整型列表
val nums = 1 :: (2 :: (3 :: (4 :: Nil)))

// 空列表
val empty = Nil

// 二维列表
val dim = (1 :: (0 :: (0 :: Nil))) ::
          (0 :: (1 :: (0 :: Nil))) ::
          (0 :: (0 :: (1 :: Nil))) :: Nil

```

## 列表操作

### 1.三大基本操作
Scala列表有三个基本操作：
* head 返回列表第一个元素
* tail 返回一个列表，包含除了第一元素之外的其他元素
* isEmpty 在列表为空时返回true

对于Scala列表的任何操作都可以使用这三个基本操作来表达。实例如下:

```scala
object Test {
   def main(args: Array[String]) {
      val site = "Runoob" :: ("Google" :: ("Baidu" :: Nil))
      val nums = Nil

      println( "第一网站是 : " + site.head )
      println( "最后一个网站是 : " + site.tail )
      println( "查看列表 site 是否为空 : " + site.isEmpty )
      println( "查看 nums 是否为空 : " + nums.isEmpty )
   }}
```

执行以上代码，输出结果为：
```scala
$ vim Test.scala 
$ scala Test.scala 
第一网站是 : Runoob
最后一个网站是 : List(Google, Baidu)
查看列表 site 是否为空 : false
查看 nums 是否为空 : true
```
### 2.连接列表

你可以使用 ::: 运算符或 List.:::() 方法或 List.concat() 方法来连接两个或多个列表。实例如下:
```scala
object Test {
   def main(args: Array[String]) {
      val site1 = "Runoob" :: ("Google" :: ("Baidu" :: Nil))
      val site2 = "Facebook" :: ("Taobao" :: Nil)

      // 使用 ::: 运算符
      var fruit = site1 ::: site2
      println( "site1 ::: site2 : " + fruit )
      
      // 使用 List.:::() 方法
      fruit = site1.:::(site2)
      println( "site1.:::(site2) : " + fruit )

      // 使用 concat 方法
      fruit = List.concat(site1, site2)
      println( "List.concat(site1, site2) : " + fruit  )
      
      //或者在文件头引入import List._
      //然后使用concat(site1,site2)
      
   }}
```

执行以上代码，输出结果为：
```scala
$ vim Test.scala 
$ scala Test.scala 
site1 ::: site2 : List(Runoob, Google, Baidu, Facebook, Taobao)
site1.:::(site2) : List(Facebook, Taobao, Runoob, Google, Baidu)
List.concat(site1, site2) : List(Runoob, Google, Baidu, Facebook, Taobao)
```
### 3.List.fill()

我们可以使用 List.fill() 方法来创建一个指定重复数量的元素列表：
```scala
object Test {
   def main(args: Array[String]) {
      val site = List.fill(3)("Runoob") // 重复 Runoob 3次
      println( "site : " + site  )

      val num = List.fill(10)(2)         // 重复元素 2, 10 次
      println( "num : " + num  )
   }}
```

执行以上代码，输出结果为：
```scala
$ vim Test.scala 
$ scala Test.scala 
site : List(Runoob, Runoob, Runoob)
num : List(2, 2, 2, 2, 2, 2, 2, 2, 2, 2)
```

### List.tabulate()

List.tabulate() 方法是通过给定的函数来创建列表。方法的第一个参数为元素的数量，可以是二维的，第二个参数为指定的函数，我们通过指定的函数计算结果并返回值插入到列表中，起始值为 0，实例如下：

```scala

object Test {
   def main(args: Array[String]) {
      // 通过给定的函数创建 5 个元素
      val squares = List.tabulate(6)(n => n * n)
      println( "一维 : " + squares  )

      // 创建二维列表
      val mul = List.tabulate( 4,5 )( _ * _ )      
      println( "多维 : " + mul  )
   }}
```
执行以上代码，输出结果为：
```scala
$ vim Test.scala 
$ scala Test.scala 
一维 : List(0, 1, 4, 9, 16, 25)
多维 : List(List(0, 0, 0, 0, 0), List(0, 1, 2, 3, 4), List(0, 2, 4, 6, 8), List(0, 3, 6, 9, 12))
```

### List.reverse

List.reverse 用于将列表的顺序反转，实例如下：
```scala
object Test {
   def main(args: Array[String]) {
      val site = "Runoob" :: ("Google" :: ("Baidu" :: Nil))
      println( "site 反转前 : " + site )

      println( "site 反转后 : " + site.reverse )
   }}
```

执行以上代码，输出结果为：
```scala
$ vim Test.scala 
$ scala Test.scala 
site 反转前 : List(Runoob, Google, Baidu)
site 反转后 : List(Baidu, Google, Runoob)
```

## filter
将列表中满足表达式的元素取出来生成新的列表

```scala
val list2=list0.filter((x)=>{x%2==0})
//取出所有偶数
```