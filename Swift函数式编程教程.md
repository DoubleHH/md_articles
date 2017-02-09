# Swift函数式编程教程

[Swift函数式编程教程英文版](https://www.raywenderlich.com/82599/swift-functional-programming-tutorial)

当从Objective-C（文章其余地方将简称OC）编程转移到Swift过程中，将OC中的概念对应到Swfit是非常符合逻辑的。你知道在OC中如何创建类，那在Swift也是一样。当然，Swfit有一些完全新的特性诸如泛型和范围操作数，但仍然还有你已经知道一些小的非常精妙的技术。（OK，可能也不那么小！）

但，Swfit不仅仅是为应用提供了一个更好的语法。使用这门新语言，你能有机会改变解决问题及编码的思路。结合Swift，函数式编程技术在你的编程武器中成为了一个可行的重要的部分。

函数式编程是一个理论性强的话题，因此这篇教程将通过例子来说明它。你将看到许多的函数编程的例子，这些例子看起来是熟悉、命令式的编程方式，之后你可以尽所能在解决相同问题的时考虑使用函数式编程技术。

> 注意：这篇Swfit教程假设你已经知道了Swift编程的基础。如果你对Swfit很陌生，我们建议你先看看[其他Swfit的教程](https://www.raywenderlich.com/?page_id=2519#swift)

# 什么是函数式编程？

简洁的说，函数式编程是是一种编程范式，强调通过数学式的函数来计算，函数具有永恒不可变性及表达式语法，尽可能少地使用参数和状态位的特性。

因为极少有共享的状态且每个函数像是应用代码海洋里的一座孤岛，所以它更加容易测试。函数式编程之所以流行是因为它使得异步和并行更加简单地协同工作。为当今多核时代提供了一种提高性能的途径。

是时候开始将方法变为函数式方式了！

# 简单的数组筛选

咱们以一个非常简单的事情作为开始：一个简单的数学位计算。你的第一个任务是写一个简单的Swift写的用于找到1到10中的偶数的函数。虽一个非常小的任务，确是对一个函数式编程很棒的介绍。

## 老的筛选方法

创建一个Swift的工作环境，保存在你喜欢的任何地方。然后贴下面的代码到你新创建的Swift文件中：

~~~swift
var evens = [Int]()
for i in 1...10 {
  if i % 2 == 0 {
    evens.append(i)
  }
}
println(evens)
~~~

该函数生成出了我们预先想要的结果

> [2, 4, 6, 8, 10]

（如果你看不到控制台的输出，记得通过`View/Assistant Editor/Show Assistant Editor`选项打开辅助编辑）

这块小代码很简单，核心算法是这样的：

1. 创建一个空数组；
2. 1-10的循环迭代器（记得是1...10是包含1和10的）；
3. 若符合条件（数字是偶数），则添加到数组内；

上述代码是实质上是命令式编程。给出明确的使用一些基本的控制命令诸如`if,for-in`指令告诉计算机找出偶数。

代码运行的很好，只是有一点验证数字是否是偶数是隐藏在循环当中的。而且存在一些严重耦合，我们期望的将偶数添加到数组的动作是包含在条件当中的。如果你希望在App的其他地方打印偶数数组，只能通过拷贝粘贴的方式来重用代码。

让我们来改成函数式吧~

## 函数式筛选

将以下代码贴到你的文件中：

~~~swift
func isEven(number: Int) -> Bool {
  return number % 2 == 0
}
evens = Array(1...10).filter(isEven)
println(evens)
~~~

可以看到，函数式编程的结果跟命令式编程一样：

> [2, 4, 6, 8, 10]

让我们看的更仔细点。它由两部分组成：

1. 数组代码段是一个简单方便生成包含1到10的数组方式。范围操作数`...`创建一个包含两端点的范围；
2. 筛选声明处是函数式编程模仿所在。这个筛选方法，对数组是显示的（基础方法），创建并返回了一个新的数组，该数组包含了只有通过筛选器里的函数返回True的元素。在这个例子中，`isEven`提供给了`filter`。

将`isEven`函数作为参数传递给了`filter`函数，但记住函数仅仅是有名字的闭包。试试添加以下更加简明的代码到你的文件中：

~~~swift
evens = Array(1...10).filter { (number) in number % 2 == 0 }
println(evens)
~~~

再一次，确认三个方法的返回结果是一致的。以上代码可以说明编译器从使用上下文推断出参数`number`的类型并且返回闭包的类型。

如果你想让你的代码更加简明，再做一步这么来写：

~~~swift
evens = Array(1...10).filter { $0 % 2 == 0 }
println(evens)
~~~

上述代码使用了参数简写，隐式返回，类型推荐...且成功了！

> 使用简写的参数表达是一种习惯或者偏好。个人来说，觉得像上面简单的例子，简写参数很好。但是，对于更加复杂的情况我更倾向于显示的参数表达。编译器虽然不关心变量名字，但它们可以创建一个与人类不同的世界！（原文连续的意思是说，不写参数名计算机可以任意翻译人们写的代码，构建出不同的含义，这并不是我们想要的。）

函数式的写法显然比命令式的更加简洁。这个简单的例子展示出了所有函数式语言所具有的一些有趣的特性：

1. 高阶函数：这种函数的参数一个函数，或者返回值是一个函数。在这个例子中，`filter`就是一个高阶函数，它可以接收一个函数作为参数；
2. 一级函数：你可以将函数当做是任意变量，可以将它们赋值给变量，也可以将它们作为参数传给其他函数；
3. 闭包：实际上就是匿名函数；

你可能注意到OC中的block也有一些类似的特性。但swfit在函数编程上走的更远，它通过混合使用更加简明的语法和内建的函数诸如`filter`。

## Filter筛选器背后的魔法

Swift有很多功能化方法，例如`map, join, reduce...`。那么在这些方法的背后是怎么实现的呢？

我们来看看`filter`背后的魔法并加上我们自己的实现。

在相同的文件中，添加以下代码：

~~~swift
func myFilter<T>(source: [T], predicate:(T) -> Bool) -> [T] {
  var result = [T]()
  for i in source {
    if predicate(i) {
      result.append(i)
    }
  }
  return result
}
~~~

上述代码是一个泛型函数，它接受一个包含类型T的数组的源（source）和一个以T类型实例为入参并返回bool值得判定函数（predicate）。

`myFilter`函数看起来更像是我们刚开始写的命令式函数。主要区别在于你可以提供一个检查条件的函数而不是硬编码在函数中。

试试新添加的实现，添加以下代码：

~~~swift
evens = myFilter(Array(1...10)) { $0 % 2 == 0 }
println(evens)
~~~

再一次说明，输出结果是一样的！

**挑战**：上述方法是全局的，看看你是否能让它变成数组的一个方法。

1. 你可以通过扩展Array添加`myFilter`方法；
2. 

# Reducing

之前的是一个很简单的例子，只用了一个函数式方法。接下来，在上面的基础上，运用函数式编程技术实现更加复杂的逻辑。

创建一个新的工作文件，准备下一个任务吧~

## Manual reduction

本阶段你的任务会复杂一点点：取出1到10的偶数并求和。这就是熟知的reduce函数，接收一组输入且返回一个输出。

我相信你有能力自己写完这个逻辑，但我已经写好了~ 添加以下代码到工作文件：

~~~swift
var evens = [Int]()
for i in 1...10 {
  if i % 2 == 0 {
    evens.append(i)
  }
}
 
var evenSum = 0
for i in evens {
  evenSum += i
}
 
println(evenSum)
~~~

结果如下：

> 30

上面的命令行代码还是和之前的例子一样，在`for-in`循环语句中做加法。

让我们来看看函数式会如何编写吧~

## 函数式Reduce（Functional Reduce）

添加如下代码到你的工作区：

~~~swift
evenSum = Array(1...10)
    .filter { (number) in number % 2 == 0 }
    .reduce(0) { (total, number) in total + number }
 
println(evenSum)
~~~

你会看到结果是：
> 30

上述代码段包含了数组的创建和`filter`的使用。这两个操作的结果是五个数字的数组`[2, 4, 6, 8, 10]`。最后一步使用的是`reduce`。

`reduce`是功能极其丰富的数组方法，可以为数组的每一个元素执行一个方法，并累加结果。

为了理解`reduce`是如何工作的，看看它的签名是有帮助的。

~~~swift
func reduce<U>(initial: U, combine: (U, T) -> U) -> U
~~~

第一个参数是类型为U的初始值。在目前的代码中，初始值是0且是`Int`型的（这里的U就一直是`Int`了）。第二个参数是`combine`函数，这个函数会对每个数组元素执行一遍。

`combine`接收连个参数，第一个是类型是U，且是上一个`combine`函数的调用结果；第二个参数是正在执行`combine`函数的数组元素。`reduce`执行的返回结果就是最后一个`combine`函数的返回结果。

这里面繁盛了很多事情，我们可以一步步拆解它。

在代码中，第一个reduce后的结果如下：

![first](https://koenig-media.raywenderlich.com/uploads/2014/09/Iteration1.png)

`combine`第一个入参的初始值是0，入参的第二个参数是数组的第一个值2。`combine`函数将它们两想加，返回2。

第二个循环如下图介绍：

![](https://koenig-media.raywenderlich.com/uploads/2014/09/Iteration2.png)

在第二个循环，`combine`的入参是第一个`combine`的返回值和下一个数组元素值。将他们两想加即2 + 4 = 6。

继续循环处理所有的数组元素可以得到下面这张表：

![](https://koenig-media.raywenderlich.com/uploads/2014/09/Iteration3.png)

加粗且边上有星号的值就是最终的执行结果。

这是一个简单例子；事实上，使用`reduce`你可以执行各种各样有趣的功能强大的转换。下面是几个运用例子。

添加以下代码到你的工作区：

~~~swift
let maxNumber = Array(1...10)
            .reduce(0) { (total, number) in max(total, number) }
println(maxNumber)
~~~

这段代码用于找出数组中的最大值。这个例子中，返回结果显而易见~ 在这里要记住，结果就是最终`reduce`执行的计算max数值的循环结果。

如果你还在努力思索为何是这么运行的，为什么创建类似于上述表格来记录每次`combine`的入参和返回值呢。

至今为止你所见到的`reduce`都是将一个整型数组转换为一个整型数字。显然，`reduce`有两个参数，U和T，是不同的，而且必须得他们没必要是整型。这意味着你能将包含一个类型的数组转换成另一种类型的数。

添加以下代码到你的工作文件中：

~~~swift
let numbers = Array(1...10)
  	.reduce("numbers: ") {(total, number) in total + "\(number) "}
println(numbers)
~~~

执行结果如下：

> numbers: 1 2 3 4 5 6 7 8 9 10

这个例子展示了`reduce`将一个整型数组转换成一个`String`数字。  
通过一些练习，你会发现可以用各种各样的有趣且好玩的方式使用`reduce`。

**挑战**：看看你是否能将一个包含位的数组转换成一个整型值。输入如下：

> let digits = ["3", "1", "4", "1"]

你的`reduce`结果需要返回3141的整形值。

## Reduce背后的魔法

在之前的段落中，你实现了自己的简单的`filter`的方法。现在我们来实现自己的`reduce`方法。

添加如下代码到工作区：

~~~swift
extension Array {
  func myReduce<T, U>(seed:U, combiner:(U, T) -> U) -> U {
    var current = seed
    for item in self {
      current = combiner(current, item as T)
    }
    return current
  }
}
~~~

上述代码添加了`myReduce`方法到数组中，模仿了内建的`reduce`函数。这个函数简单的遍历每个数组元素，每一次遍历都会调用`combiner`函数。

为测试上述代码，可以用`myReduce`替换掉当前工作区内的`reduce`函数。

此时，你也许会想，“我为什么会想要自己实现`filter`和`reduce`方法？”。回答是，“和很可能不会~”

但是，你可能会想要在Swfit中扩展当前的函数范式，实现你自己想要的函数式方法。看明白并且理解函数式多么容易实现对于你自己去实现一些强大的诸如`reduce`样的函数是非常重要且有激励性的。

# 建立索引

是时候解决一些更复杂的问题了，意味着你要新建另一个新的工作区啦。你明白你想这么做~

在本段中，你将使用函数式编程技术把一组单词通过他们的首字母分成不同的组。  
在你新创建的工作区内添加如下代码：

~~~swift
import Foundation
 
let words = ["Cat", "Chicken", "fish", "Dog",
                      "Mouse", "Guinea Pig", "monkey"]
~~~

为了完成本阶段的任务，你将通过首字母将他们分组（大小写敏感）。添加以下代码做准备：

~~~swift
typealias Entry = (Character, [String])
 
func buildIndex(words: [String]) -> [Entry] {
  return [Entry]()
}
println(buildIndex(words))
~~~

`Entry`为每个entry定义了元祖类型。这个例子中使用类型重命名是代码更易读










