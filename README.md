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

1. 多行字符串的缩进问题


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
