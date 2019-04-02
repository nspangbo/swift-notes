# swift-notes

> Swift 5 学习笔记。

## A Swift Tour

1. 在跨行字符串中，每一行的有效缩进，是相对右引号（最后三个引号）的缩进，右引号的缩进必须大于所有行的缩进，否则编译器会报错。
    ```swift
    let quotation = """
    Even though there's whitespace to the left,
    the actual lines aren't indented.
        Except for this line.
    Double quotes (") can appear without being escaped.
    I still have \(apples + oranges) pieces of fruit.
    """
    
    // 输出
    //Even though there's whitespace to the left,
    //the actual lines aren't indented.
    //  Except for this line.
    //Double quotes (") can appear without being escaped.
    //I still have \(apples + oranges) pieces of fruit.
    ```

2. Swift 是类型安全的，因此 `if` 条件返回 `boolean` 值：
    ```swift
    if score { // error 
        ... 
    } 
    ```

3. `Switch` 语句支持任何类型的数据和多种比较操作，不再局限于整数和相等比较，分支默认不会贯穿，一个分支执行后默认直接跳出，因此不需要每个分支都写上 `break`，如果需要贯穿，需要显式声明 `fallthrough` 关键字。分支必须覆盖所有可能，否则需要有 default 分支。
    ```swift
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
    ```swift
    let `init` = "initialize"
    let π = 3.14159
    let 你好 = "你好世界"
    let 🐶🐮 = "dogcow"
    ```

2. Swift 提供 `Int`、`Int8`、`Int32`、`Int64` 等数据类型表示整数，在没有特别需求时，建议使用 `Int`，而非其他明确位数的类型。`Int` 是一个和平台相关的类型，即32位机中等价于 `Int32`，64位机中等价于 `Int64`，使用 `Int` 可以提高代码的一致性和互操作性。特别的，如果没有特别需求，即使知道所存储的数据时无符号的，也应该尽量使用 `Int` 而非 `UInt`，避免无谓的类型转换和类型推断。

3. Swift 提供 `Double` 和 `Float` 类型用于表示浮点数，两者分别具有至少15位和6位的十进制小数精度，当两者均可选时，`Double` 是首选类型。

4. Swift 是类型安全的语言，类型检查和类型推断都是在编译时进行的。在使用字面量初始化变量时，类型推断会选择上述2、3中的首选类型。

5. `typealias` 用于声明别名：
    ```swift
    typealias AudioSample = UInt16
    ```

6. 强制解包(Forced Unwrapping)操作应该始终在确认可选值有值之后。
    ```swift
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
    ```swift
    if x = y {
        // This is not valid, because x = y does not return a value.
    }
    ```

2. 算术运算符(`+`、`-`、`*`、`/`)区别于 C 系语言，默认不允许溢出，Swift 提供了3个支持溢出的高级运算符：`&+`、`&-`、`&*`。

3. 取余运算符(%)在 Swift 中和 C 以及其他语言中的实现略有不同，前者是取余运算，而后者往往是取模运算。因此在不同语言中，我们需要清楚 `%` 的含义。
    ```swift
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
    ```swift
    var a = 1
    let b = a += 2 // 等价于 let b: ()
    ```

5. 除了 C 系语言中的常见比较运算符(`==`、`!=`、`>`、`<`、`>=`、`<=`)以外，Swift 还提供了两个用于判断引用是否指向同一对象的运算符：`===`、`！==`


## Strings and Characters

1. 扩展字符串分隔符(Extended String Delimiters)用于在是字符串特殊字符不生效的前提下，打印字符串，[这里](https://github.com/apple/swift-evolution/blob/master/proposals/0200-raw-string-escaping.md) 详细阐述了这一建议。
    ```swift
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
    ```swift
    "a" --> LATIN SMALL LETTER A
    "🐥" --> FRONT-FACING BABY CHICK
    "é" --> LATIN SMALL LETTER E WITH ACUTE
    ```

3. 扩展字形集群(Extended Grapheme Clusters)表示一个人类可读的字符，每一个 `Character` 实例都是一个扩展字形集群。一个扩展字形集群可能由一个或者多个 Unicode 标量构成，同一个扩展字形集群也可能由一个或者多个 Unicode 标量以不同的形式构成。
    ```swift
    let eAcute: Character = "\u{E9}"                         // é
    let combinedEAcute: Character = "\u{65}\u{301}"          // e followed by ́
    // eAcute is é, combinedEAcute is é
    ```
    字符、字符串等价判断使用标准等价标准：如果两者在语义和外观展现上一致，即使其底层使用不同的 Unicode 标量构成，也认为他们是等价的。

4. 由于扩展字形集群可能有多个 Unicode 标量组成，因此不同字符或者相同字符的不同表现形式可能需要由不同大小的内存单元来存储，也就是说，`String` 对象的每一个字符，可能都占用不同大小的内存单元。因此 `String` 对象的长度，只能通过遍历其每一个字符，以确定每一个扩展字形集群的边界来获取。所以长字符串的长度获取，是一个高消耗的操作。也正是由于扩展字形集群大小不一，`String` 对象无法像 C 系语言一样，把字符串作为集合，直接通过整数下标来操作。`String` 对象的随机访问，只能通过一系列和 index 相关的方法来进行。

## Collection Types

1. 该章节没有预期之外的点。

## Control Flow

1. `switch` 语句中，需要完整处理所有可能的案例，因此要么穷举所有 case，要么提供 `default` 分支。每个 case 执行完之后直接跳出 `switch` 语句，不会隐式贯穿（除非显式指明 `fallthrough` 关键字），也不用写 `break` 声明。`switch` 语句的模式匹配非常强大，除了简单的值匹配以外，还支持多种匹配模式：
    ```swift
    // 区间匹配
    let approximateCount = 62
    let countedThings = "moons orbiting Saturn"
    let naturalCount: String
    switch approximateCount {
    case 0:
        naturalCount = "no"
    case 1..<5:
        naturalCount = "a few"
    case 5..<12:
        naturalCount = "several"
    case 12..<100:
        naturalCount = "dozens of"
    case 100..<1000:
        naturalCount = "hundreds of"
    default:
        naturalCount = "many"
    }
    print("There are \(naturalCount) \(countedThings).")
    // Prints "There are dozens of moons orbiting Saturn."

    // 元组
    let somePoint = (1, 1)
    switch somePoint {
    case (0, 0):
        print("\(somePoint) is at the origin")
    case (_, 0):
        print("\(somePoint) is on the x-axis")
    case (0, _):
        print("\(somePoint) is on the y-axis")
    case (-2...2, -2...2):
        print("\(somePoint) is inside the box")
    default:
        print("\(somePoint) is outside of the box")
    }
    // Prints "(1, 1) is inside the box"

    // 值绑定
    let anotherPoint = (2, 0)
    switch anotherPoint {
    case (let x, 0):
        print("on the x-axis with an x value of \(x)")
    case (0, let y):
        print("on the y-axis with a y value of \(y)")
    case let (x, y):
        print("somewhere else at (\(x), \(y))")
    }
    // Prints "on the x-axis with an x value of 2"

    // 条件限定
    let yetAnotherPoint = (1, -1)
    switch yetAnotherPoint {
    case let (x, y) where x == y:
        print("(\(x), \(y)) is on the line x == y")
    case let (x, y) where x == -y:
        print("(\(x), \(y)) is on the line x == -y")
    case let (x, y):
        print("(\(x), \(y)) is just some arbitrary point")
    }
    // Prints "(1, -1) is on the line x == -y"
    ```

2. Swift 支持5个控制转移关键字：`continue`、`break`、`fallthrough`、`return`、`throw`：
    1. `continue` 指示结束当前循环执行下次循环，只能用于循环语句中，不能用于 `enumerator` 语句中
    2. `break` 跳出当前控制语句，只能用于循环和 `switch` 语句中
    3. `fallthrough` 在 `switch` 语句中贯穿到下一个 case
    4. `return` 跳出当前方法，在 `enumerator` 语句中表示跳过当前遍历项进项下一项遍历
    5. `throw` 用于抛出一个异常，然后跳转到异常处理语句中

3. Swift 支持标签语句：
    ```swift
    // 语法
    label name: while condition {
        //statements
    }

    // 示例
    gameLoop: while square != finalSquare {
        diceRoll += 1
        if diceRoll == 7 { diceRoll = 1 }
        switch square + diceRoll {
        case finalSquare:
            // diceRoll will move us to the final square, so the game is over
            break gameLoop
        case let newSquare where newSquare > finalSquare:
            // diceRoll will move us beyond the final square, so roll again
            continue gameLoop
        default:
            // this is a valid move, so find out its effect
            square += diceRoll
            square += board[square]
        }
    }
    print("Game over!")
    ```

4. 检查 API 可用性：
    ```swift
    // 语法
    if #available(platform name version, ..., *) {
        statements to execute if the APIs are available
    } else {
        fallback statements to execute if the APIs are unavailable
    }

    // 示例
    if #available(iOS 10, macOS 10.12, *) {
        // Use iOS 10 APIs on iOS, and use macOS 10.12 APIs on macOS
    } else {
        // Fall back to earlier iOS and macOS APIs
    }
    ```

## Functions

1. Swift 中的方法都有自己的类型，这个类型和 `Int` 类型一样，可以用于任何其他类型使用的地方，例如将其作为参数、方法返回值等。方法的类型由其参数和返回值定义，例如 
    ```swift
    (Int, String) -> String
    ```
    方法可以至多有一个可变参数，可变参数在函数体类通过标签名指向的数组来访问。

## Closures

1. 闭包和方法的类似，是一个自包含的方法块，可以被传递和使用，对应于 OC 中的 Block，以及其他语言中的 Lambdas。闭包会捕获其定义域内的常量和变量，Swift 负责这些捕获行为的内存管理工作。全局函数是一种有名字但不捕获任何值得闭包；嵌套函数则是一种有名字，会捕获其定义域内值的闭包；闭包表达式是一种没有名字，轻量级的编写方式，可以从其定义域内捕获值。得益于 Swift 的类型推断，闭包也可以从上下文中推断出参数和返回值的类型，如果闭包中只有一个表达式，则其返回值将隐式作为闭包的返回值。闭包还支持输入输出参数，可变参数，但不支持参数默认值。闭包的语法如下：
    ```swift
    { (parameters) -> return type in
        statements
    }
    ```

1. 逃逸闭包：将一个闭包作为参数传递给一个方法，但是在方法返回之后，该闭包才执行，则称该闭包逃逸。方法可以通过将闭包参数继续传递，后续调用实现闭包逃逸。支持逃逸的闭包使用 `@escaping` 标识。

2. 自动闭包：指将方法参数自动封装成闭包的行为，自动闭包没有入参，有返回值。比如 `assert(condition:message:file:line:)` 方法的 `condition` 和 `message` 参数，都是一个自动闭包参数。通过 `@autoclosure` 将一个参数标记为自动闭包，距离如下：
    ```swift
    var persons = ["xxxpangbo", "0xwangbo"]

    func helloPerson(_ person: @autoclosure () -> String) {
        print("Hello, \(person()).")
    }

    helloPerson(persons.removeLast())
    // output: Hello, 0xwangbo.
    ```
    上述示例中，`helloPerson` 方法接受一个无入参，返回 `String` 对象的闭包，而我们在调用时，却直接参入了 `persons.removeLast()` 的返回值，正式因为 `person` 参数被标记成了 `@autoclosure`，我们才能这么做。在实际编程中，我们可能经常用到含有自动闭包的方法，但是我们很少自己定义和实现含有自动闭包参数的方法，滥用自动闭包，将使代码难以理解。

## Enumerations

1. Swift 的枚举类型比 C 语言中的枚举类型强大很多，后者仅仅将一组相关联的值分配给一组整型值，而前者除了原始值支持整型、浮点型、字符串、字符类型外，还支持关联值，以及递归枚举。枚举的声明语法如下：
    ```swift
    enum EnumerationType {
        // ...
    }
    ```

2. 枚举类型本身就是值，所以给每个 case 设置原始值不是必要的。原始值和关联值得区别在于，前者对于每一个枚举实例的每一个 case，原始值是不变的，而后者每个枚举实例的每个 case，只要求关联值类型一直，不通 case 之间，关联值没有类型限制，没有必然联系。
    ```swift
    enum Barcode {
        case upc(Int, Int, Int, Int)
        case qrCode(String)
    }
    ```

3. 递归枚举值枚举类型某些 case 的关联对象是枚举类型本身。例如：
    ```swift
    enum ArithmeticExpression {
        case number(Int)
        indirect case addition(ArithmeticExpression, ArithmeticExpression)
        indirect case multiplication(ArithmeticExpression, ArithmeticExpression)
    }

    // 等价写法
    indirect enum ArithmeticExpression {
        case number(Int)
        case addition(ArithmeticExpression, ArithmeticExpression)
        case multiplication(ArithmeticExpression, ArithmeticExpression)
    }
    ```
    该枚举存储三种算术表达式，普通数字，加法表达式，乘法表达式。示例用法如下：
    ```swift
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

    let five = ArithmeticExpression.number(5)
    let four = ArithmeticExpression.number(4)
    let sum = ArithmeticExpression.addition(five, four)
    let product = ArithmeticExpression.multiplication(sum, ArithmeticExpression.number(2))

    print(evaluate(product))
    // Prints "18"
    ```
    `evaluate` 方法是该枚举的核心实现。


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
