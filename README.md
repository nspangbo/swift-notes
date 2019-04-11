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

1. Swift 的枚举类型比 C 语言中的枚举类型强大很多，后者仅仅将一组相关联的值分配给一组整型值，而前者除了原始值支持整型、浮点型、字符串、字符类型外，还支持关联值，以及递归枚举。枚举是值类型，声明语法如下：
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

1. Swift 中的结构体相对于其他语言，更接近类的表现，两者有如下共同点：
    1. 可定义属性
    2. 可定义方法
    3. 可定义下标
    4. 可提供构造器
    5. 可以扩展以超越默认实现
    6. 可以遵循协议以提供某种标准功能
    7. 值类型
    
    相对于结构体，类有如下特性：
    1. 支持继承
    2. 支持运行时类型转换
    3. 支持析构器
    4. 引用类型（引用计数）

2. Swift 中的整型、浮点型、布尔型、字符串、数组、字典等类型，其底层实现都是结构体，因此都是值类型。值类型连带其值类型的属性，按照语义都会在复制时进行拷贝。但是苹果通过写时复制技术优化了这一过程，使拷贝仅在必要时发生。

## Properties

1. Swift 中的属性类似于 OC 中的属性，但前者不再使用实例变量作为属性的底层存储。同时属性分为存储属性和计算属性，前者存储值，后者通过计算，操作另一个值，但可能并没有与之对应的一个实例变量存在。存储属性只能用于类和结构体中，计算属性则能用于类、结构体、枚举中。同样的，属性有实例属性和类型属性之分。我们还可以为存储属性或者继承自父类的存储属性或者计算属性添加属性观察者。

2. 一个常量结构体，即使结构体内部有变量属性，也无法更改，因为值类型的对象在赋值给常量时，其所有的属性都将被标记为常量，引用类型则没有此特性。

3. 使用 `lazy` 将一个存储属性标记为惰性属性，其值只有在第一次使用时才会计算，惰性属性必须用 `var` 标识。惰性属性通常用于其值依赖于某个外部因素的场景，因为在初始化时外部因素可能还没有就绪。另外在初始化操作非常复杂和耗时的场景下，惰性属性也适用，这样可以避免这类对象之后没有适用，而提前初始化浪费资源。重要提示，惰性属性需要由使用者自己保证线程安全，当惰性属性没有初始化时，在多个线程同时访问，将无法保证属性仅初始化一次。全局变量或常量总是具有惰性，并且不需要使用 `lazy` 标记。

4. 计算属性在不通时候访问可能会得到不同的值，因此需要使用 `var` 声明。
    ```swift
    struct AlternativeRect {
        var origin = Point()
        var size = Size()
        var center: Point {
            get {
                let centerX = origin.x + (size.width / 2)
                let centerY = origin.y + (size.height / 2)
                return Point(x: centerX, y: centerY)
            }
            set {
                origin.x = newValue.x - (size.width / 2)
                origin.y = newValue.y - (size.height / 2)
            }
        }
    }
    ```

5. 属性观察期可用于非惰性存储属性和继承自父类的存储或计算属性，设置是惰性存储属性。
    ```swift
    class TypeA {
        lazy var number = {
            return 1
        }()
    }

    class TypeB: TypeA {
        override var number: Int {
            willSet {}
            
            didSet {}
        }
    }

    print(TypeB().number)
    // output: 1
    ```
    在类的整个初始化过程中，修改自身属性不会触发属性观察期，但修改继承自父类的属性（调用父类 `init` 方法之后）则会触发属性观察期。如果把一个具有属性观察器的属性以输入输出参数的形式传递给一个方法，由于 [copy-in copy-out](https://docs.swift.org/swift-book/ReferenceManual/Declarations.html#ID545) 内存模型，属性的观察器会在进入方法和方法返回时分别调用一次。在属性观察器内部更改当前属性的值，不会触发属性观察器，但如果在 `willSet` 更改，其值会在 `didSet` 中被新值覆写。

6. 类型属性类似于 OC 的类属性，可以是存储变量或常量属性，也可以是计算变量属性，由于类本身没有初始化方法，因此必须为其类型属性提供默认值，此类属性没有线程安全问题，在第一次使用前，即使通过多个线程同时访问，也能保障属性只初始化一次。类型属性是惰性的，但不需要使用 `lazy` 标记。
    ```swift
    struct SomeStructure {
        static var storedTypeProperty = "Some value."
        static var computedTypeProperty: Int {
            return 1
        }
    }
    enum SomeEnumeration {
        static var storedTypeProperty = "Some value."
        static var computedTypeProperty: Int {
            return 6
        }
    }
    class SomeClass {
        static var storedTypeProperty = "Some value."
        static var computedTypeProperty: Int {
            return 27
        }
        class var overrideableComputedTypeProperty: Int {
            return 107
        }
    }
    ```
    使用 `static` 标记类型属性，只有类中的类型计算属性，可以使用 `class` 标记以允许子类覆写，类型存储属性只能用 `static` 标记，且不允许子类覆写。

## Methods

1. Swift 中类、结构体、枚举均可以定义自己的实例方法和类型方法。但是在值类型（结构体、枚举）中，方法如果需要修改对象本身，需要使用 `mutating` 标记，该类方法甚至可以将一个新的对象赋值给 `self`。

2. 类型方法使用 `static` 标记，类也可以使用 `class` 标记其类型方法，以允许子类覆写。

## Subscripts

1. 下标是访问集合、列表、序列中元素的快捷方式，类、结构体、枚举类型均可以定义下标，数组、字典通过下标方式访问其中元素就是最好的例子。每种类型都可以定义多个下标，而下标可以有多个参数，甚至是可变参数，但参数不能是输入输出参数，也不能有默认值，下标通常还会返回一个值。下标通过是否提供 `set` 方法来决定其读写性：
    ```swift
    // 可读可写的下标声明
    subscript(index: Int) -> Int {
        get {
            // return an appropriate subscript value here
        }
        set(newValue) {
            // perform a suitable setting action here
        }
    }
    ```

## Inheritance

1. 只有类可以继承自另一个类，这是类区别于 Swift 中其他类型的基本行为。通过继承，子类可以访问其超类中的属性、方法和下标，也可以通过重写，提供自己的实现版本，子类还可以通过重写继承自父类的属性，向任何属性添加属性观察器。

2. Swift 不要求类必须继承自某一个基类，所有没有父类的类，都作为基类存在。

3. 通过将属性、方法、下标，甚至整个类标记成 `final`，来防止被重写或者被继承。

## Initialization

1. Swift 中类、结构体、枚举都可以定义构造器已保证在使用相关实例前初始化所有存储属性，类还可以定义析构器已在对象销毁前释放相关资源。和 OC 不同，Swift 中的构造器没有返回值。

2. 给属性设置默认值，或者在初始化过程中赋值，都不会触发属性观察器。如果属性总是使用相同的默认值，则推荐将其作为属性定义的一部分，而非在初始化过程中赋值。

3. 如果结构体或者类的所有存储属性都有默认值，则 Swift 为其提供一个默认初始化方法，通过在类型后边附加一对括号实现调用：
    ```swift
    class ShoppingListItem {
        var name: String?
        var quantity = 1
        var purchased = false
    }
    var item = ShoppingListItem()
    ```

4. 对于结构体来说，如果没有在声明体内自定义构造器，那么即使存储属性没有默认值，Swift 也会为其提供一个成员构造器：
    ```swift
    struct Size {
        var width: Double 
        var height: Double
    }
    let twoByTwo = Size(width: 2.0, height: 2.0)
    ```
    一旦在声明体内自定义了构造器，则默认构造器（例如上述成员构造器）将不再可用，此约束防止调用者通过调用默认构造器意外地绕过了更加复杂的自定义构造器逻辑。此时我们可以将自定义构造器通过扩展的形式声明，已达到既能访问成员构造器，又能自定义构造器的目的。

5. 类型的构造器可以通过调用另一个构造器完成初始化过程的行为，称为构造器委派（Initializer Delegation）。值类型和引用类型的构造器委派不同，前者因为不支持继承，所以一个构造器只能委派该类型中另一个构造器完成初始化过程，逻辑相对简单。而后者因为支持继承，子类的构造器可以委派自己的其他构造器或者父类的构造器完成初始化过程，此过程相对复杂，需要有明确的策略来保证对象在使用前，所有存储属性均得到初始化。由此引用类型定义了指定构造器和便利构造器，并定义了如下规则：
    1. 指定构造器必须调用父类的一个指定构造器（如果有父类）
    2. 便利构造器必须调用同类中的另一个构造器（便利构造器或者指定构造器），且最终必须调用到同类的一个指定构造器上

    由此可见 Swift 中初始化分为两个阶段：
    1. 第一阶段：类负责初始化由其引入的所有存储属性（子类初始化自己引入的存储属性，然后调用父类的指定构造器，初始化父类引入的存储属性，以此类推）
    2. 第二阶段：继承链中的所有存储属性均得到初始化后，在类的实例被外界使用之前，有机会对继承链中的属性做进一步定制

    为保障上述两个阶段正确完成，编译器会做如下检查：
    1. 指定构造器在调用父类的指定构造器之前，必须初始化所有自己引入的存储属性
    2. 对继承的属性赋值之前，必须确保已经调用了父类的指定构造器，否则赋予的值将在后续调用父类指定构造器时被覆写，编译器会直接报错
    3. 在对任何属性赋值之前，必须确保便利构造器调用了指定构造器，否则在便利构造器中的赋值，将会在指定构造器中被覆写，编译器会直接报错
    4. 在完成第一阶段（继承链中所有节点均完成第一阶段）前，不能访问属性，不能调用实例方法

6. 指定构造器是类的主要构造器，保证完全初始化所有引入的存储属性，并通过调用父类合适的指定构造器完成整个继承链的初始化过程。每个类都至少有一个指定构造器，可能是自己实现的，也可能继承自父类。其语法类似方法定义，但是没有返回值：
    ```swift
    init(parameters) {
        statements
    }
    ```

7. 便利构造器是为了方便调用而定义的构造器，其内部可能对入参做处理或者过滤，但最终一定会调用该类的一个指定构造器。类根据具体需要决定是否需要提供便利构造器。其语法和指定构造器类似，但是需要使用 `convenience` 标记：
    ```swift
    convenience init(parameters) {
        statements
    }
    ```

8. Swift 不同于 OC，构造器默认情况下不会被继承，这可以防止超类的构造器被子类重写后，导致属性不能完全初始化。例如子类引入了新的存储属性，如果此时继承了超类的构造器，调用者对其进行调用，则无法初始化新引入的存储属性。

    子类可以通过 `override` 标识，提供超类构造器的覆写版本，即使是覆写了超类的默认构造器也一样。但是，如果子类定义了和超类便利构造器相同的构造器，则不需要此标记。因为子类的便利构造器，只能调用该类中的其他便利构造器或者指定构造器，进而调用超类的指定构造器，至始至终都无法调用到超类的便利构造器上，所以这种情况下实际没有覆写超类的便利构造器。

    超类的构造器，只有在某些绝对安全的场景下才会被子类继承。假设子类为新引入的所有存储属性提供了默认值，且：
    1. 子类没有定义任何指定构造器，则子类将继承超类的所有构造器
    2. 子类提供了超类所有的指定构造器实现（可能通过 1 中的完整继承，或者通过提供覆写版本），则子类将继承超类所有的便利构造器

    子类即使提供了自己的便利构造器，上述规则也同样适用。另外如果子类将超类的指定构造器重写为便利构造器，也同样作为上述规则 2 的一部分，即同样视为提供了超类的指定构造器实现。

9. OC 中的构造器方法可能返回 `nil` 以表示初始化对象失败，Swift 中由于构造器没有返回值，因此使用一下语法声明一个可能构造失败的构造器：
    ```swift
    func init?() {
        if true {
            // ...
        } else {
            // 尽管构造器没有返回值，此处也应该使用 return 语句
            return nil
        }
    }
    ```
    由于构造器使用统一的名称 `init`，因此不同构造器只能通过其参数加以区分，所以我们不能用同样的参数声明一个可失败构造器和一个不可失败构造器。可失败构造器可以委派到当前类型的其他可失败构造器，或者父类的可失败构造器，构造环节中的任何一个节点失败，都将直接终止整个构造过程，不会执行后续的代码。可失败构造器也可以委派到一个不可失败构造器，通常用于对现有的不可失败构造器增加条件检查的场景。

    子类可以将父类的可失败构造器覆，甚至覆写成不可失败构造器，但是不能将父类的不可失败构造器覆写成可失败构造器。

    具有原始值的枚举类型，有一个默认的可失败构造器，以应对传入的值无法匹配到任何 case 的情况。

10. 除了指定构造器和便利构造器以外，Swift 还引入了必要构造器的概念。通过 `required` 标记的构造器，要求所有子类都必须覆写该构造器，并且覆写时同样需要使用 `required` 标记。在覆写必要构造器时，不用使用 `override` 标记。如果满足构造器的继承条件，子类也不必显式的给出必要构造器的实现。

11. 除了在定义属性时直接赋予默认值外，还可以通过一个闭包或者方法来为其赋值。此类闭包或方法只会在类型实例初始化时执行一次。
    ```swift
    class SomeClass {
        let someProperty: SomeType = {
            // create a default value for someProperty inside this closure
            // someValue must be of the same type as SomeType
            return someValue
        }()
    }
    ```

## Deinitialization

1. 只有类可以定义析构器，且每个类至多只能定义一个析构器。我们不能从代码中主动调用析构器方法，而必须由系统自动调用。析构器没有参数列表，语法如下：
    ```swift
    deinit {
        // perform the deinitialization
    }
    ```
    实例在 `deinit` 方法执行并返回之后才会被销毁，因此在 `deinit` 方法中，我们依旧可以访问实例的所有属性和方法。

    子类默认继承超类的析构器，并且在子类的析构器返回之前，自动执行超类的析构器，即使子类没有自己实现析构器，超类的析构器也会调用。

## Optional Chaining

1. 由于可选类型的引入，方法、属性、下标的访问可能都会返回可选类型，于是把调用或查询结果可能为空的方法、属性、下标的操作称为可选链。链中任何一个环节出现 `nil` 都将导致整个查询的结果为 `nil`，否则查询的结果将是一个可选类型。和强制解包一个可选值类似，当可选值有值时，可以正常调用其上的方法、属性或者下标，区别在于可选链在可选值为 `nil` 时会正常返回 `nil`，而强制解包会导致运行时错误。

2. 在可选值上通过可选链调用方法，同样可以检测到方法是否调用。因为没有明确返回值的方法，实际会隐式的返回一个空元组，即 `()`，也就是 `Void`，而在可选链中调用此类方法，则会返回 `()?` 或者 `Void?`：
    ```swift
    if john.residence?.printNumberOfRooms() != nil {
        print("It was possible to print the number of rooms.")
    } else {
        print("It was not possible to print the number of rooms.")
    }
    // Prints "It was not possible to print the number of rooms."
    ```

## Error Handling

1. Swift 为运行时抛出、捕获、传播和处理错误提供了一流的支持。`Error` 协议是一个空协议，定义所谓的错误。通过 `throw` 关键字抛出一个遵循 `Error` 协议的错误，用 `throws` 标记可能会抛出错误的方法。处理错误的几种方式如下：
    ```swift
    // 1. do-catch 方式
    do {
        try expression
        statements
    } catch pattern 1 {
        statements
    } catch pattern 2 where condition {
        statements
    } catch {
        statements
    }

    // 2. 将可能抛出错误的语句转换为可选值
    func someThrowingFunction() throws -> Int {
        // ...
    
    let x = try? someThrowingFunction()

    // 3. 确定不会抛出错误时，强制解包
    let photo = try! loadImage(atPath: "./Resources/John Appleseed.jpg")
    ```

2. 一个方法可能并不会完全执行其方法体内的语句，例如中途 `return` 或者抛出异常，都会立即终止后续代码的执行。Swift 引入 `defer` 语句，来封装一个代码块内必须执行的一些逻辑，无论是中途 `return`、`break` 还是抛出了异常，都会在退出当前代码块之前执行 `defer` 块内的代码，如果当前代码块内定义了多个 `defer` 块，则会按照定义顺序的逆序执行。`defer` 常见的应用场景是执行一些必要的清理工作。
    ```swift
    func processFile(filename: String) throws {
        if exists(filename) {
            let file = open(filename)
            defer {
                close(file)
            }
            while let line = try file.readline() {
                // Work with the file.
            }
            // close(file) is called here, at the end of the scope.
        }
    }
    
    // 执行顺序
    class FileHandler {
        func dealFile() {
            defer {
                print("defer 1")
            }
            defer {
                print("defer 2")
            }
            
            for index in 1...3 {
                defer {
                    print("loop .. defer \(index)")
                }
                print("loop .. \(index)")
            }
            
            defer {
                print("defer 3")
            }
            
            print("end")
        }
    }
    FileHandler().dealFile()

    // output
    // loop .. 1
    // loop .. defer 1
    // loop .. 2
    // loop .. defer 2
    // loop .. 3
    // loop .. defer 3
    // end
    // defer 3
    // defer 2
    // defer 1
    ```

## Type Casting

1. `is` 关键字用于判断某对象是否是某个类型的实例：
    ```swift
    class MediaItem {}
    class Movie: MediaItem {}
    class Song: MediaItem {}

    var movieCount = 0
    var songCount = 0

    for item in library {
        if item is Movie {
            movieCount += 1
        } else if item is Song {
            songCount += 1
        }
    }
    ```

2. 尝试将父类实例转换为子类实例，称为向下转换（downcasting）, `as?` 返回一个可选的转换结果，`as!` 强制转换，如果失败则会触发运行时错误。
    ```swift
    for item in library {
        if let movie = item as? Movie {
            print("Movie: \(movie.name), dir. \(movie.director)")
        } else if let song = item as? Song {
            print("Song: \(song.name), by \(song.artist)")
        }
    }
    ```

3. 尝试将子类实例转换为父类实例，称为向上转换（upcasts），通常用于 `switch` 语句中的模式匹配：
    ```swift
    var things = [Any]()

    things.append(0)
    things.append(0.0)
    things.append(42)
    things.append(3.14159)
    things.append("hello")
    things.append((3.0, 5.0))
    things.append(Movie(name: "Ghostbusters", director: "Ivan Reitman"))
    things.append({ (name: String) -> String in "Hello, \(name)" })

    for thing in things {
        switch thing {
        case 0 as Int:
            print("zero as an Int")
        case 0 as Double:
            print("zero as a Double")
        case let someInt as Int:
            print("an integer value of \(someInt)")
        case let someDouble as Double where someDouble > 0:
            print("a positive double value of \(someDouble)")
        case is Double:
            print("some other double value that I don't want to print")
        case let someString as String:
            print("a string value of \"\(someString)\"")
        case let (x, y) as (Double, Double):
            print("an (x, y) point at \(x), \(y)")
        case let movie as Movie:
            print("a movie called \(movie.name), dir. \(movie.director)")
        case let stringConverter as (String) -> String:
            print(stringConverter("Michael"))
        default:
            print("something else")
        }
    }
    ```

4. 无论是向下还是向上转换，原实例都不会有任何变化，转换成功后，仅仅是将其作为一个新的类型的实例对待。

## Nested Types

1. Swift 允许定义嵌套类型，即在一个类型中定义其他类型。使用时，采用链式语法：
    ```swift
    class TypeA {
        enum EnumA {
            case haha, hehe
        }
    }

    let typeA = TypeA()
    let enumA = TypeA.EnumA.haha
    ```

## Extensions

1. Swift 中的扩展用于向已有的类、结构体、枚举、协议，甚至无法访问其源代码的类型，增加功能实现，类似于 OC 中的 Category，但 Swift 的扩展没有名字。扩展可以向已有类型添加方法，但是不能覆盖已有方法的实现。扩展可以实现以下功能：
    1. 添加计算实例属性或计算类型属性（扩展不能添加存储属性，也不能为现有属性添加属性观察器）
    2. 定义实例方法或类型方法
    3. 提供新的便利构造器（扩展不能为引用类型添加指定构造器和析构器，这两者只能由引用类型的原始实现提供。如果值类型没有实现任何自定义的构造器，则通过扩展为其提供构造器之后，依旧可以访问其默认构造器和成员构造器。如果值类型的原始定义和扩展分别处于不同模块，则扩展中的构造器在调用原始定义中的构造器之前，均不能访问 `self`）
    4. 定义下标方法
    5. 定义和使用新的嵌套类型
    6. 使现有类型符合某协议
    7. 为协议实现默认实现

    通过扩展向现有类型添加方法，对于该类型的所有实例均有效，即使实例在扩展定义之前创建。

2. 扩展的语法形式：
    ```swift
    extension SomeType {
        // new functionality to add to SomeType goes here
    }
    ```

## Protocols

1. Swift 中的协议可以被类、结构体、枚举类型实现，还能通过扩展为协议中定义的方法提供默认实现，这样遵循该协议的类型都将默认具有这些实现，也可以提供自己的实现来覆盖默认行为，协议还支持多继承。

2. 协议中定义的属性，只要求其名称、类型以及读写权限，这些属性可以被实现为存储属性或者计算属性。协议中的属性始终用 `var` 标记，和类型属性不同，在协议声明中始终使用 `static` 标记类型属性。
    ```swift
    protocol SomeProtocol {
        var mustBeSettable: Int { get set }
        var doesNotNeedToBeSettable: Int { get }
    }
    ```

3. 协议中定义的方法，允许使用可变参数，但是无法为参数设置默认值，也没有方法体。如果希望方法可以改变对象本身，例如结构体中的方法，则同样需要使用 `mutating` 标记，在结构体类型实现该方法时，也需要保留 `mutating` 标记，类则没有此要求。
    ```swift
    protocol RandomNumberGenerator {
        func random() -> Double
        mutating func toggle()
    }
    ```
    在协议中定义的构造器方法，可以被实现为指定构造器或者便利构造器，类型在实现这些方法时需要用 `required` 标记这些方法，以保证子类也满足协议要求。

4. 继承自 `AnyObject` 的协议只能被类类型实现。当协议中存在可选的方法时，需要用 `optional` 标记这些可选方法，同时还需要使用 `@objc` 将协议本身和方法标记。
    ```swift
    @objc protocol CounterDataSource {
        @objc optional func increment(forCount count: Int) -> Int
        @objc optional var fixedIncrement: Int { get }
    }
    ```

## Generics


## Automatic Reference Counting

Swift 中解决循环强引用有两种方式：弱引用和无主引用。弱引用通常用于对象间的引用，因为无法保证对方不会释放，因此使用弱引用以在对方释放时自动将引用置为 `nil`。无主引用通常用于对象内部，比如对象内部的闭包和对象本身产生循环引用，因为两者同生共死，因此可以使用无主引用。

1. 弱引用：使用 `weak var` 声明的可选属性，在所引用的对象释放后会自动置为 `nil`，这一行为也不会触发属性观察器。

2. 无主引用：使用 `unowned` 声明，和弱引用类似，无主引用不会强持有对象，避免产生循环强引用。不同的是无主引用用于期望其引用的对象不会释放的场景，如果对象释放了，无主引用也不会自动置为 `nil`，此时再次访问属性可能导致运行时错误。

3. Swift 还提供了不安全的无主引用，使用 `unowned(unsafe)` 声明，通常用于出于性能考虑而需要禁用安全检查的场景，如果在引用对象释放后再次访问， 是不安全的操作。

4. 闭包由于捕获其上下文中的引用也可能造成循环引用，此时通过闭包捕获列表来解决：
    ```swift
    lazy var someClosure: (Int, String) -> String = {
        [unowned self, weak delegate = self.delegate!] (index: Int, stringToProcess: String) -> String in
        // 这里是闭包的函数体
    }
    ```

## Memory Safety



## Access Control



## Advanced Operators

1. Swift 默认不支持运算符溢出，但提供了 3 个支持溢出的高级运算符：`&+`、`&-`、`&*`，有符号整型数据的符号文同数值位一起参与溢出运算。

2. 按位取反运算符 `~`：1 变 0，0 变 1
    ```swift
    let initialBits: UInt8 = 0b00001111
    let invertedBits = ~initialBits // 等于 0b11110000
    ```

3. 按位与运算符 `&`：都为 1 则为 1
    ```swift
    let firstSixBits: UInt8 = 0b11111100
    let lastSixBits: UInt8  = 0b00111111
    let middleFourBits = firstSixBits & lastSixBits // 等于 00111100
    ```

4. 按位或运算符 `|`：只要有一个 1 则为 1
    ```swift
    let someBits: UInt8 = 0b10110010
    let moreBits: UInt8 = 0b01011110
    let combinedbits = someBits | moreBits // 等于 11111110
    ```

5. 按位异或运算符 `^`： 不同为 1 相同为 0
    ```swift
    let firstBits: UInt8 = 0b00010100
    let otherBits: UInt8 = 0b00000101
    let outputBits = firstBits ^ otherBits // 等于 00010001
    ```

6. 按位左移（`>>`）和按位右移（`<<`）运算符：
    1. 对于无符号整型数据，因移位超出范围的位都被丢弃，因移位产生的空位用 0 填充，即逻辑移位。
    2. 对于有符号正整型数据，第一位符号位，0 为正，剩下的为数值位。有符号正整数和无符号整数存储方式一样。
    3. 对于有符号负整型数据，第一位符号位，1 为负，剩下的为数值位。数值位存储采用二进制补码形式，即存储 2 的 n 次方减去（其实际值的绝对值），n 是数值位的位数。例如 -4 的存储形式如下：
        ```swift
        1 1111100 = -4
        1111100 = 124 (2 ^ 7 - 4)
        ```
    补码形式看起来奇怪，但有一下有点：
    1. 如果要对两个负数做加法，只需要将符号位在内的所有比特位进行标准的二进制加法，然后丢弃溢出的位即可。
    2. 使用二进制补码可以使负数的按位左移和右移运算得到跟正数同样的效果，即每向左移一位就将自身的数值乘以 2，每向右一位就将自身的数值除以 2。要达到此目的，对有符号整数的右移有一个额外的规则：当对有符号整数进行按位右移运算时，遵循与无符号整数相同的规则，但是对于移位产生的空白位使用符号位进行填充，而不是用 0。这个行为可以确保有符号整数的符号位不会因为右移运算而改变，这通常被称为算术移位。由于正数和负数的特殊存储方式，在对它们进行右移的时候，会使它们越来越接近 0。在移位的过程中保持符号位不变，意味着负整数在接近 0 的过程中会一直保持为负。

7. 运算符重载：
    ```swift
    struct Vector2D {
        var x = 0.0, y = 0.0
    }

    extension Vector2D {
        static func + (left: Vector2D, right: Vector2D) -> Vector2D {
            return Vector2D(x: left.x + right.x, y: left.y + right.y)
        }
    }
    ```
    前缀运算符和后缀运算符需要分别使用 `prefix` 和 `postfix` 标记：
    ```swift
    extension Vector2D {
        static prefix func - (vector: Vector2D) -> Vector2D {
            return Vector2D(x: -vector.x, y: -vector.y)
        }
    }
    ```
    复合运算符：需要把运算符的左参数设置成 `inout` 类型，因为这个参数的值会在运算符函数内直接被修改。
    ```swift
    extension Vector2D {
        static func += (left: inout Vector2D, right: Vector2D) {
            left = left + right
        }
    }
    ```
    不能对默认的赋值运算符（`=`）进行重载。只有复合赋值运算符可以被重载。同样地，也无法对三元条件运算符 （`a ? b : c`） 进行重载。
    
    自定义运算符：新的运算符要使用 `operator` 关键字在全局作用域内进行定义，同时还要指定 `prefix`、`infix` 或者 `postfix` 修饰符：
    ```swift
    extension Vector2D {
        static prefix func +++ (vector: inout Vector2D) -> Vector2D {
            vector += vector
            return vector
        }
    }
    ```
    自定义中缀运算符的优先级：每个自定义中缀运算符都属于某个优先级组。优先级组指定了这个运算符相对于其他中缀运算符的优先级和结合性。而没有明确放入某个优先级组的自定义中缀运算符将会被放到一个默认的优先级组内，其优先级高于三元运算符。
    ```swift
    infix operator +-: AdditionPrecedence
    extension Vector2D {
        static func +- (left: Vector2D, right: Vector2D) -> Vector2D {
            return Vector2D(x: left.x + right.x, y: left.y - right.y)
        }
    }
    ```