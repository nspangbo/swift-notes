# swift-notes

> Swift 5 学习笔记。

## A Swift Tour

1. 在跨行字符串中，每一行的有效缩进，是相对右引号（最后三个引号）的缩进，右引号的缩进必须大于所有行的缩进，否则编译器会报错。
    ```Swift
    let quotation = """
    Even though there's whitespace to the left,
    the actual lines aren't indented.
    Except for this line.
    Double quotes (") can appear without being escaped.
    I still have \(apples + oranges) pieces of fruit.
    """
    ```

2. Swift 是类型安全的，因此 `if` 条件返回 `boolean` 值：
    ```Swift
    if score { // error 
        ... 
    } 
    ```

3. `Switch` 语句支持任何类型的数据和多种比较操作，不再局限于整数和相等比较，分支默认不会贯穿，一个分支执行后默认直接跳出，因此不需要每个分支都写上 `break`，如果需要贯穿，需要显式声明 `fallthrough` 关键字。分支必须覆盖所有可能，否则需要有 default 分支。
    ```Swift
    let vegetable = "red pepper"
    switch vegetable {
    case "celery":
        print("Add some raisins and make ants on a log.")
    case "cucumber", "watercress":
        print("That would make a good tea sandwich.")
    case let x where x.hasSuffix("pepper"):
        print("Is it a spicy \(x)?")
    default:
        print("Everything tastes good in soup.")
    }
    // Prints "Is it a spicy red pepper?"
    ```

4. `defer` 定义的代码块，无论方法是否抛出了异常，都会在所有语句执行后，方法返回之前执行，因此可以用来做必要的清理工作，避免了 [异常安全问题](https://en.wikipedia.org/wiki/Exception_safety)。


## The Basics

1. 首先，变量命名不再局限于 [ASCII](https://en.wikipedia.org/wiki/ASCII) 编码啦！😁如果和系统保留关键字重名，可以用 `` 包裹起来，不过为什么一定要这样呢~
    ```Swift
    let `init` = "initialize"
    let π = 3.14159
    let 你好 = "你好世界"
    let 🐶🐮 = "dogcow"
    ```

2. Swift 提供 `Int`、`Int8`、`Int32`、`Int64` 等数据类型表示整数，在没有特别需求时，建议使用 `Int`，而非其他明确位数的类型。`Int` 是一个和平台相关的类型，即32位机中等价于 `Int32`，64位机中等价于 `Int64`，使用 `Int` 可以提高代码的一致性和互操作性。特别的，如果没有特别需求，即使知道所存储的数据时无符号的，也应该尽量使用 `Int` 而非 `UInt`，避免无谓的类型转换和类型推断。

3. Swift 提供 `Double` 和 `Float` 类型用于表示浮点数，两者分别具有至少15位和6位的十进制小数精度，当两者均可选时，`Double` 是首选类型。

4. Swift 是类型安全的语言，类型检查和类型推断都是在编译时进行的。在使用字面量初始化变量时，类型推断会选择上述2、3中的首选类型。

5. `typealias` 用于声明别名：
    ```Swift
    typealias AudioSample = UInt16
    ```

6. 强制解包(Forced Unwrapping)操作应该始终在确认可选值有值之后。
    ```Swift
    if convertedNumber != nil {
        print("convertedNumber has an integer value of \(convertedNumber!).")
    }
    // Prints "convertedNumber has an integer value of 123."
    ```

7. 可选绑定(Optional Binding)创建的临时变量仅在其后的闭包定义域中有效，而 `guard` 语句定义的临时变量则在 `guard` 所在定义域内均有效。

8. 隐式解包可选值(`Type!`)区别于可选值(`Type?`)，被认为首次赋值之后便一定有值，后续使用时不用再进行可选绑定，主要用于[类初始化期间](https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html#ID55)。业务方不建议使用该类型，因为复杂的业务可能无法保证变量满足隐式解包可选值的要求，从而导致程序崩溃。

9. 断言(assertions)仅在开发环境中有效，前置条件(preconditions)在开发环境和生产环境中均有效，除非以 unchecked mode(-Ounchecked) 模式编译，两者均发生在运行时。`fatalError` 方法无论什么环境下都会导致程序突出，建议仅用于开发期间标识为实现的功能。


## Basic Operators

1. 赋值运算符(=)不会返回任何值，避免在 C 系语言中，误将其用于 `if` 条件中。因此以下定义是无效的：
    ```Swift
    if x = y {
        // This is not valid, because x = y does not return a value.
    }
    ```

2. 算术运算符(`+`、`-`、`*`、`/`)区别于 C 系语言，默认不允许溢出，Swift 提供了3个支持溢出的高级运算符：`&+`、`&-`、`&*`。

3. 取余运算符(%)在 Swift 中和 C 以及其他语言中的实现略有不同，前者是取余运算，而后者往往是取模运算。因此在不同语言中，我们需要清楚 `%` 的含义。
    ```Swift
    // 求 a % b 的本质:
    // 1. c = a/b
    // 2. result = a - bc
    //
    // 取余和取模的差别在第一步中，前者向0舍入，后者向无穷小舍入

    // 例如计算 -7 % 4
    // 1. -7 / 4 = -1.75
    // 取余：-7 / 4 = -1.75 --> -1
    // 取模：-7 / 4 = -1.75 --> -2
    //
    // 2. 取余：result = -7 - (-1 * 4) = -3
    // 2. 取模：result = -7 - (-2 * 4) = 1
    ```

4. 算术运算符(`+`、`-`、`*`、`/`)对应的符合运算符(`+=`、`-=`、`*=`、`/=`)没有返回值：
    ```Swift
    var a = 1
    let b = a += 2 // 等价于 let b: ()
    ```

5. 除了 C 系语言中的常见比较运算符(`==`、`!=`、`>`、`<`、`>=`、`<=`)以外，Swift 还提供了两个用于判断引用是否指向同一对象的运算符：`===`、`！==`


## Strings and Characters

1. 扩展字符串分隔符(Extended String Delimiters)用于在是字符串特殊字符不生效的前提下，打印字符串，[这里](https://github.com/apple/swift-evolution/blob/master/proposals/0200-raw-string-escaping.md) 详细阐述了这一建议。
    ```Swift
    print("Line 1\nLine 2")
    // output
    // Line 1
    // Line 2

    print(#"Line 1\nLine 2"#)
    // output
    // Line 1\nLine 2

    print(#"Write an interpolated string in Swift using \(multiplier)."#)
    // Prints "Write an interpolated string in Swift using \(multiplier)."

    print(#"6 times 7 is \#(6 * 7)."#)
    // Prints "6 times 7 is 42."
    ```

2. Swift `String` 类型完全符合 Unicode 标准，`String` 类型构建于 Unicode 标量(Unicode scalar values)之上，Unicode 标量是一个唯一的21位二进制位数字，不过并非21位全部分配给字符，某些标量被保留，用于将来分配或者 UTF-16 编
码。每一个已分配的字符标量都有一个名字：
    ```Swift
    "a" --> LATIN SMALL LETTER A
    "🐥" --> FRONT-FACING BABY CHICK
    "é" --> LATIN SMALL LETTER E WITH ACUTE
    ```

3. 扩展字形集群(Extended Grapheme Clusters)表示一个人类可读的字符，每一个 `Character` 实例都是一个扩展字形集群。一个扩展字形集群可能由一个或者多个 Unicode 标量构成，同一个扩展字形集群也可能由一个或者多个 Unicode 标量以不同的形式构成。
    ```Swift
    let eAcute: Character = "\u{E9}"                         // é
    let combinedEAcute: Character = "\u{65}\u{301}"          // e followed by ́
    // eAcute is é, combinedEAcute is é
    ```
    字符、字符串等价判断使用标准等价标准：如果两者在语义和外观展现上一致，即使其底层使用不同的 Unicode 标量构成，也认为他们是等价的。

4. 由于扩展字形集群可能有多个 Unicode 标量组成，因此不同字符或者相同字符的不同表现形式可能需要由不同大小的内存单元来存储，也就是说，`String` 对象的每一个字符，可能都占用不同大小的内存单元。因此 `String` 对象的长度，只能通过遍历其每一个字符，以确定每一个扩展字形集群的边界来获取。所以长字符串的长度获取，是一个高消耗的操作。也正是由于扩展字形集群大小不一，`String` 对象无法像 C 系语言一样，把字符串作为集合，直接通过整数下标来操作。`String` 对象的随机访问，只能通过一系列和 index 相关的方法来进行。

## Collection Types



## Control Flow



## Functions



## Closures



## Enumerations



## Classes and Structures



## Properties



## Methods



## Subscripts



## Inheritance



## Initialization



## Deinitialization



## Optional Chaining



## Error Handling



## Type Casting



## Nested Types



## Extensions



## Protocols



## Generics



## Automatic Reference Counting



## Memory Safety



## Access Control



## Advanced Operators
