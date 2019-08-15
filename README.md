![](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1565686144362&di=60902c744272580f45120d8a163bd170&imgtype=jpg&src=http%3A%2F%2Fassets3.vc.cn%2Fassets.vc.cn%2Fsystem%2Fphotos%2Favatars%2F000%2F080%2F650%2Foriginal%2F69c9e7d654c8dc82095755ff2f0e291b.jpeg%3F1490066566)

# Welcome to Swift

## 环境

Swift 是苹果目前全力推进的语音，目前可用于苹果全平台、Linux 平台开发，也有成熟的方案用于服务器开发，机器学习领域还有 TensorFlow for Swift。

下文基于 Swift 5.1，是 Xcode 11 中的默认版本。相对于正式版本 5.0，仅下述少数几个新增特性无法使用：

1. 返回 Opaque 类型的函数，比如 `SwiftUI` 中的 `some` 返回值
2. `try?` 表达式可选嵌套问题
3. 大整数溢出问题：`UInt64(0xffff_ffff_ffff_ffff)`
4. 其他一些小众场景下的特性


## 概述

本节主要概述 Swift 有什么，以及和 OC 的一些区别，主要聚焦在语言基础部分。

### 基础数据类型

Swift 支持 `Int`系列类型、`Double`、`Float`、`Bool`、`String`、`Tuple` 等基础数据类型，这些类型都是值类型，通过结构体实现。

```swift
元组的使用
let http404Error = (404, "Not Found")
// http404Error is of type (Int, String), and equals (404, "Not Found")

let (statusCode, statusMessage) = http404Error
print("The status code is \(statusCode)")
// Prints "The status code is 404"
print("The status message is \(statusMessage)")
// Prints "The status message is Not Found"
```

所有上述类型都有对应的可选类型，比如：`Int?`

> Tips：区别于 OC，Swift 中的基础类型都是值类型，而非引用类型，同时新增了 OC 没有的元组类型。


### 基础数据结构

Swift 支持 `Array`、`Set`、`Dictionary` 等集合类型，这些类型都通过泛型结构体实现。Swift 是类型严格的语言，所以所有集合都需要通过泛型声明或者类型推断确定其元素的具体类型。

```swift
var numbers: [Int] // [Int]
numbers.append(1)
numbers.append(2)
numbers.append(3.3) // error: Cannot convert value of type 'Double' to expected argument type 'Int'

var strings = ["nspangbo"] // [String]
```

> `Array<String>`、`Array<String?>`、`Array<String?>`、`Array<String?>?` 是 4 种完全不同的类型。


### Variable & Constant（Mutable & Unmutable）

使用 `let` 定义常量，使用 `var` 定义变量。

```swift
var myVariable = 42
myVariable = 50
let myConstant = 42

let π = 3.14159
let 你好 = "你好世界"
let 🐶🐮 = "dogcow"

// NSString <--> let string: String
// NSMutableString <--> var string: String
```

> Tips：区别于 OC，可变不可变通过关键字 `let` 和 `var` 来定义，尽量不要使用 NSMutable 系列对象。


### 类型安全 & 类型推断 & 类型转换

1. 类型安全：Swift 中所有变量、常量、方法等，都有明确的类型，类型检查发生在编译阶段。
2. 类型推断：要么显式声明类型，要么通过类型推断确定类型，以保证类型安全一致性。
3. 类型转换：as?（可失败转换）、as!（强制转换，可能崩溃）、as（子类转父类）、is（类型判断）。

```swift
var number = 10.5 // Double
let emptyArray = [String]() // [String]
let emptyDictionary = [String: Float]() // [String : Float]
var occupations = ["Malcolm": "Captain", "Kaylee": "Mechanic",] // [String : String]

// (String) -> Bool
func printHello(_ name: String) -> Bool {
    print("Hello, \(name)!")
    return true
}

let score = 100
if score { ... } // error? why?
// 另外，if 条件也不用担心少写一个等号，why？
```

> Tips：所有东西都有明确类型，OC 中的隐式类型转换不再存在，类型转换相比于 OC 的方式更加安全。

### 可选类型

类似于 OC 中的 `nil`，不同之处在于可选类型可应用于所有类型，包括基础数据类型，方法、闭包等。一个类型和其可选类型，是两种不同的类型。`Int?` 标识其值可能存在，或者不存在，不存在不等同于值为 0。

```swift
var number: Int?
if number == 0 {
    print("number is 0")
} else {
    print("number is nil")
}
// output: number is nil
```

> Optional 是 Swift 实现许多 Feature 的重要支撑，是 Swift 最重要的组成之一，需要深入理解。

### 类 & 结构体

类、结构体都是一等类型，前者是引用类型，后者是值类型。区别于 OC，他们都能定义属性、方法、下标，都有初始化函数，都能遵循协议，都能扩展以增加功能。两者的区别在于值类型和引用类型在内存语义和内存管理上的差异，以及类独有的继承、类型转换、析构过程以及引用计数等特性。

特别的：

1. 除非明确需要用到继承、析构等特性，否则出于性能考虑，苹果总是建议我们使用结构体。
2. 区别于 C、C++、OC，即使使用对象，我们也不需要使用 * 去声明一个指针，并且如果不是必须使用到指针特性，苹果总是建议我们忽略掉和内存相关的实现，以使程序更加安全。Swift 几乎所有和手动内存操作的接口，都有 `unsafe` 前缀。

### 枚举

枚举同类、结构体一样，都是一等类型，同样支持定义属性、方法、初始化器，支持扩展，可以遵循协议等。相对于 OC 中的枚举，Swift 中枚举不需要一定对应一个整型值，其声明本身就是一种值。同时支持原始值和关联值，原始值类似于 OC 的中的枚举定义，不过不局限于整型类型，而关联值则更加灵活。

```swift
// 枚举原始值
enum ASCIIControlCharacter: Character {
    case tab = "\t"
    case lineFeed = "\n"
    case carriageReturn = "\r"
}

// 枚举关联值
enum Barcode {
    case upc(Int, Int, Int, Int)
    case qrCode(String)
}

// 枚举的递归定义，此处推荐王巍的 Kingfisher 框架，其中各种配置的实现充分利用了的各种枚举特性。
enum ArithmeticExpression {
    case number(Int)
    indirect case addition(ArithmeticExpression, ArithmeticExpression)
    indirect case multiplication(ArithmeticExpression, ArithmeticExpression)
}
```

因为枚举的灵活性，使得 Swift 中 Switch 语句同样变得异常灵活：

```swift
// 算术表达式的抽象
let five = ArithmeticExpression.number(5)
let four = ArithmeticExpression.number(4)
let sum = ArithmeticExpression.addition(five, four)
let product = ArithmeticExpression.multiplication(sum, ArithmeticExpression.number(2))

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
```
> Swift 中结构体和枚举的用途非常广泛和灵活，甚至比类的使用更加频繁，需要深入理解。

### 运算符

和 OC 一样，Swift 提供了常见的所有运算符支持，另外还增加了对溢出运算和区间运算的支持，赋值运算符也和 C 系语言中的赋值有所差别，同时移除了 `++` 和 `--` 运算符。

Swift 还支持自定义运算符和运算符重载，比如 `Collection` 协议就通过扩展实现了 `+` 运算符，因此两个 `String` 可以直接相加，而在 OC 中是不行的。

1. 赋值运算符：不再有返回值，因此 if 条件语句中不用担心少写一个等号。

	```swift
	if score = 100 { // error: Demo.playground:13:11: error: use of '=' in a boolean context, did you mean '=='?
		// ...
	}
	```

2. 溢出运算符：`&+`、`&-`、`&*`

	```swift
	var potentialOverflow = Int16.max
	// potentialOverflow equals 32767, which is the maximum value an Int16 can hold
	potentialOverflow += 1
	// this causes an error
	
	var unsignedOverflow = UInt8.max
	// unsignedOverflow equals 255, which is the maximum value a UInt8 can hold
	unsignedOverflow = unsignedOverflow &+ 1
	// unsignedOverflow is now equal to 0
	```

3. 区间运算符：

	```swift
	// ... 左闭右闭
	// ..< 左闭右开
	for index in 1...5 {
	    print("\(index) times 5 is \(index * 5)")
	}
	// 1 times 5 is 5
	// 2 times 5 is 10
	// 3 times 5 is 15
	// 4 times 5 is 20
	// 5 times 5 is 25
	
	for name in names[2...] {
	    print(name)
	}
	// Brian
	// Jack
	
	let range = ...5
	range.contains(7)   // false
	range.contains(4)   // true
	range.contains(-1)  // true
	```

### 流程控制

Swift 支持 `if`、`switch`、`for-in`、`while`、`repeat-while`，其中，变化最大的是废弃传统的 `for` 循环语句，以及 `switch` 语句的增强。

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

### 方法和闭包

Swift 中方法也是一种类型，并且支持许多 OC 中没有的特性，比如参数标签、可选参数、默认参数、多返回值、方法嵌套，方法类型等。

```swfit
func greet(person: String) -> String {
    let greeting = "Hello, " + person + "!"
    return greeting
}
```

上述方法的类型是 `(String) -> String`，该类型和 `String` 类型一样，可以用于任何 `String` 类型使用的场景，例如变量类型，方法参数，方法返回值等。

通过元组，方法可以返回多个参数：

```swift
func minMax(array: [Int]) -> (min: Int, max: Int) {
    var currentMin = array[0]
    var currentMax = array[0]
    for value in array[1..<array.count] {
        if value < currentMin {
            currentMin = value
        } else if value > currentMax {
            currentMax = value
        }
    }
    return (currentMin, currentMax)
}
```

方法定义可以嵌套，类似于 OC 中的局部 Block：

```swift
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    func stepForward(input: Int) -> Int { return input + 1 }
    func stepBackward(input: Int) -> Int { return input - 1 }
    return backward ? stepBackward : stepForward
}
var currentValue = -4
let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
// moveNearerToZero now refers to the nested stepForward() function
while currentValue != 0 {
    print("\(currentValue)... ")
    currentValue = moveNearerToZero(currentValue)
}
print("zero!")
// -4...
// -3...
// -2...
// -1...
// zero!
```

区别于 OC 中的 Block，Swift 的闭包在很大程度上解决了被广为诟病的用法繁琐问题，在语法层面做了很多优化：

1. 利用上下文推断参数和返回值类型
2. 隐式返回单表达式闭包，即单表达式闭包可以省略 `return` 关键字
3. 参数名称缩写
4. 尾随闭包语法

```swift
NSArray *sortedPhones = [phoneArray sortedArrayUsingComparator:^NSComparisonResult(NSString * _Nonnull obj1, NSString * _Nonnull obj2) {
	return [obj1 compare:obj2];
}];

let sortedPhones = phoneArray.sorted(by: >)
```

特别的，闭包是引用类型。

Swift 中的闭包新增了逃逸闭包和自动闭包的概念。简单来讲，逃逸闭包指一个方法传入的闭包参数，会在方法返回后的某一时刻执行，例如网络类接口的回调，需要使用 `@escaping` 声明。而自动闭包就如其名字，是一种自动创建的闭包，通过 `@autoclosure` 关键字修饰，其好处在于语法层面得到了简化，同时具有懒加载的特性。

特别的，过度使用 `autoclosures` 会让你的代码变得难以理解，上下文和函数名应该能够清晰地表明求值是被延迟执行的。


### 属性

属性的定义同 OC 中的属性，且同样支持类型属性，不同之处在于 Swift 中的属性没有对应的实例变量，其实际存储的底层实现也无法访问，属性的定义和访问方式更加统一。

属性分两种，计算属性和存储属性。计算属性可以应用于类、结构体、枚举，存储属性只能应用于类和结构体。属性是否可变同样仅通过 `let` 或者 `var` 定义，通过 `lazy` 关键字可以定义一个懒加载的属性，相比 OC 重写 `getter` 的方式方便很多。Swift 新增了属性观察期，可以让我们在属性的值改变前后加入自定义的处理流程。

```swift
struct CompactRect {
    var origin = Point()
    var size = Size()
    var center: Point {
        get {
            Point(x: origin.x + (size.width / 2),
                  y: origin.y + (size.height / 2))
        }
        set { // 如果没有 set 就是只读的计算属性
            origin.x = newValue.x - (size.width / 2)
            origin.y = newValue.y - (size.height / 2)
        }
    }
}

class StepCounter {
    var totalSteps: Int = 0 {
        willSet(newTotalSteps) {
            print("将 totalSteps 的值设置为 \(newTotalSteps)")
        }
        didSet {
            if totalSteps > oldValue  {
                print("增加了 \(totalSteps - oldValue) 步")
            }
        }
    }
}
```

特别的，请深入理解值类型和引用类型：

```swift
struct FixedLengthRange {
    var firstValue: Int
    let length: Int
}

let rangeOfFourItems = FixedLengthRange(firstValue: 0, length: 4)
// 该区间表示整数 0，1，2，3
rangeOfFourItems.firstValue = 6 // error? why?
```

这种行为是由于结构体属于值类型，当值类型的实例被声明为常量的时候，它的所有属性也就成了常量。属于引用类型的类则不一样，把一个引用类型的实例赋给一个常量后，依然可以修改该实例的可变属性。


### 下标

```swift
var numbers = [1, 2, 3,]
print(numbers[1])

var strings = "123456"
//print(strings[0]) // error: 'subscript(_:)' is unavailable: cannot subscript String with an Int, see the documentation comment for discussion
print(strings[strings.startIndex])
```

通过 `[]` 这种方式访问一个集合的元素，称之为下标访问。相比于 OC 中下标只能运用于集合、字典，Swift 增强了下标的能力，使其可以用于类、结构体、枚举中，其语法如下：

```swift
subscript(index: Int) -> Int {
    get {
      // 返回一个适当的 Int 类型的值
    }
    set(newValue) {
      // 执行适当的赋值操作
    }
}
```

一个类型可以定义多个下标，通过不同索引类型进行重载。下标不限于一维，你可以定义具有多个入参的下标满足自定义类型的需求。

一个具体的场景，在处理二维或者多维数据结构时，下标会让数据存取变得更容易：

```swift
struct Matrix {
    let rows: Int, columns: Int
    var grid: [Double]
    init(rows: Int, columns: Int) {
        self.rows = rows
        self.columns = columns
        grid = Array(repeating: 0.0, count: rows * columns)
    }
    func indexIsValid(row: Int, column: Int) -> Bool {
        return row >= 0 && row < rows && column >= 0 && column < columns
    }
    subscript(row: Int, column: Int) -> Double {
        get {
            assert(indexIsValid(row: row, column: column), "Index out of range")
            return grid[(row * columns) + column]
        }
        set {
            assert(indexIsValid(row: row, column: column), "Index out of range")
            grid[(row * columns) + column] = newValue
        }
    }
}
```

```
var matrix = Matrix(rows: 2, columns: 2)
```
![](https://docs.swift.org/swift-book/_images/subscriptMatrix01_2x.png)

```
matrix[0, 1] = 1.5
matrix[1, 0] = 3.2
```
![](https://docs.swift.org/swift-book/_images/subscriptMatrix02_2x.png)


### 继承

Swift 中的继承和 OC 中类似，不一样的地方在于通过继承，我们可以给父类的属性添加属性观察器，无论其是计算属性还是存储属性。

通过 `override` 关键字，我们可以覆写父类中的属性、方法、下标等，并且在编译层面保证了安全。具体来讲，override 关键字会提醒 Swift 编译器去检查该类的超类（或其中一个父类）是否有匹配重写版本的声明，这个检查可以确保你的重写定义是正确的，而非错误地提供了一个相同的定义。

通过 `final` 关键字，可以将类、属性、方法等声明为不可被重写，该特性同样在编译层面得到保证。


### 扩展

扩展可以给一个现有的类、结构体、枚举、协议添加新的功能，Swift 中的扩展不需要拥有被扩展类型的源代码，及支持逆向建模。和 OC 不同，Swift 中的扩展没有名字。简单来讲，扩展支持以下能力：

1. 添加计算型实例属性和计算型类属性
2. 定义实例方法和类方法
3. 提供新的构造器
4. 定义下标
5. 定义和使用新的嵌套类型
6. 使已经存在的类型遵循一个协议
7. 为协议提供默认实现

```swift
extension Int { // 我们并没有 Int 类型的源代码，但同样可以为其添加方法
    mutating func square() {
        self = self * self
    }
}
var someInt = 3
someInt.square()
// someInt 现在是 9
```

特别的，Swift 中的扩展，不能覆写已有方法。


### 协议

Swift 中的协议和 OC 类似，用于定义一套实现某一功能所需的方法、属性、下标等。区别于 OC，Swift 中类、结构体、枚举都可以实现协议，还可以通过扩展为协议提供默认实现，间接实现可选方法的定义。通过将 `AnyObject` 协议加入协议继承列表中，可以限制协议只能由类实现。

```swift
protocol SomeProtocol {
    var mustBeSettable: Int { get set }
    var doesNotNeedToBeSettable: Int { get }
    mutating func toggle()
}
```

特别的，由于结构体也能实现协议，所以定义协议时，如果某个方法可以改变其类型自身，则需要使用 `mutating ` 关键字声明，并且在结构体中实现该类方法时同样需要使用该关键字声明，类则没有这一限制。

协议和类、结构体等类型一样，也是一等类型。因此协议也可以用于属性类型，方法参数类型等场景下，同样支持扩展：

```swift
protocol TextRepresentable {
    var textualDescription: String { get }
}

extension TextRepresentable {
    var textualDescription: String {
        return "not implementation"
    }
}
```

特别的，如果需要实现类似 OC 中的可选协议方法，需要用 `@objc` 关键字标记协议本身和相关方法，使用 `optional` 标记相关方法：

```swift
@objc protocol CounterDataSource {
    @objc optional func increment(forCount count: Int) -> Int
    @objc optional var fixedIncrement: Int { get }
}
```


### 构造过程 & 析构过程

相较于 OC，Swift 更加安全，其中一点体现在 Swift 在编译层面保证了所有用到的数据在使用前一定会得到初始化。和 OC 不同，Swift 的构造器没有返回值，不同构造器只能通过参数列表的差异来区分，不像 OC 构造器有不同的名字。

具体来讲，类和结构体的构造器，必须负责为其所有的存储属性设置初始值，包括常量属性和变量属性，在 OC 中，大多数类型都有默认的初始值，而 Swift 没有。特别的，为存储属性设置默认值或者在构造过程中对其进行赋值，不会触发属性观察器（在属性观察器中改变属性本身，也不会导致循环触发问题，但是应该没有这种需求？）。另外，如果我们在构造器中对一个属性设置固定值，那么更好的实践方式应该是将其作为属性声明的一部分，以充分利用类型推到和默认构造器的便利。

默认构造器是指：如果类和结构体为所有的存储属性都提供了默认值，并且没有实现任何自定义构造器，那么 Swift 会为这些类型提供一个不带参数的默认构造器。

```swift
class ShoppingListItem {
    var name: String?
    var quantity = 1
    var purchased = false
}
var item = ShoppingListItem()
```

逐一成员构造器是指：对于结构体，如果没有实现任何自定义构造器，即使没有给所有属性设置默认值，Swift 也会提供一个参数列表包含所有属性的构造器。

```swift
struct Size {
    var width = 0.0, height = 0.0
}
let twoByTwo = Size(width: 2.0, height: 2.0)
```

同时，我们还可以省略任何一个有默认值的属性：

```swift
let zeroByTwo = Size(height: 2.0)
print(zeroByTwo.width, zeroByTwo.height)
// 打印 "0.0 2.0"

let zeroByZero = Size()
print(zeroByZero.width, zeroByZero.height)
// 打印 "0.0 0.0"
```

为了实现代码复用，Swift 提供了构造器代理机制，即类型的构造器可以通过其他构造器完成实例的部分构造过程，以避免多个构造器间的代码重复。值类型和引用类型的构造器代理规则不同，前者因为不能继承，所以值类型的构造器只能代理给自己的其他构造器，其基本表现形式为在一个构造器中调用 `self.init`，相对简单。而引用类型由于可以继承，所以其构造器必须保证自己以及继承来的属性都正确的被初始化，规则相对复杂。

特别注意，如果为一个值类型提供了自定义构造器，则无法访问其默认构造器和逐一成员构造器，这种规则避免了在一个更复杂的构造器中做了额外的重要设置，但有人不小心使用了自动生成的构造器而导致重要逻辑的缺失。类似于 OC 中，我们在自定义的构造器中做了配置，所以需要禁用基本的 `init` 方法来防止外部的错误调用，只不过 OC 需要我们通过一些关键字去约束，而 Swift 在编译层面提供了保证。

上述规则有一个方法例外，即如果我们既想提供自定义构造器，又想访问默认构造器和逐一成员构造器，我们可以把自定义构造器定义在类型的扩展中，而不是定义在原始类定义中。

对于引用类型的构造过程，会复杂一些，这里引入了指定构造器（designated initializers）和遍历构造器（convenience initializers）两个概念。

每个类必须拥有至少一个指定构造器，可能是自己实现，也可能通过继承得到，它负责完全初始化该类的存储属性，并负责调用父类的构造器，完成整个继承链的构造过程。

便利构造器是辅助性的，通常我们为一个类定义一个指定构造器和多个便利构造器，便利构造器最终必须调用该类的指定构造器。

```swift
// 指定构造器
init(parameters) {
    statements
}

// 便利构造器
convenience init(parameters) {
    statements
}
```

对于引用类型的构造器代理过程，有如下三个基本规则：

1. 指定构造器必须调用其直接父类的指定构造器；
2. 便利构造器只能调用同类的其他构造器；
3. 便利构造器最终必须调用到制定构造器。

简单来讲，指定构造器总是向上代理，便利构造器总是横向代理。

![](https://docs.swift.org/swift-book/_images/initializerDelegation02_2x.png)

基于上述规则，Swift 中引用类型的构造过程分为两个阶段：

1. 阶段一：初始化本类中的所有存储属性；
2. 阶段二：自定义过程，可以对本类的属性或者继承到的属性进行调整。

两个阶段的基本表现如下：

```swift
init() {
	// 阶段一：初始化
    self.xxx = xxx
    self.yyy = yyy
    
    // 继承链构造
    super.init()
    
    // 阶段二：自定义
    self.hello()
}
```

Swift 提供了 4 中安全检查，以保证上述两个阶段的正确性：

1. 指定构造器必须保证所有的存储属性都得到初始化后，才能将构造任务代理给父类的指定构造器；
2. 指定构造器必须在其父类完成构造过程后，才能对其继承的属性进行自定义，以防止被父类的构造过程意外的覆写；
3. 类似 2，便利构造器必须在调用指定构造器后，才能对属性进行自定义，以防止被指定构造器覆写；
4. 构造器在完成第一阶段的属性初始化之前，不能访问任何实例方法，不能访问任何属性的值。

Swift 的构造过程类似于系统的事件响应机制，阶段一是从子类到父类的必要构造过程，阶段二是从父类到子类的可选自定义过程。

和普通方法的覆写类似，构造器也可以被覆写，子类可以将父类的指定构造器覆写为指定构造器或者便利构造器，使用 `override` 标识。因为子类无法访问父类便利构造器，所以当子类的构造器和父类的便利构造器方法签名相同时，并不需要使用 `override` 标识。

区别于 OC，Swift 的构造器默认情况下是不会被子类继承的，以防止父类简单的构造过程无法正确实例化一个子类。构造器的继承仅发生在安全和适当的情况下，规则如下：

1. 子类中新引入的所有存储属性都有默认值；
2. 如果子类没有定义任何构造器，则将继承父类的所有指定构造器；
3. 如果子类通过继承或者自定义实现，提供了所有父类的指定构造器，则将自动继承父类的所有便利构造器。

基于上述规则，引入一个简单示例：

```swift
class Food {
    var name: String
    init(name: String) {
        self.name = name
    }

    convenience init() {
        self.init(name: "[Unnamed]")
    }
}
```

`Food` 类的构造链如下图：

![](https://docs.swift.org/swift-book/_images/initializersExample01_2x.png)

```swift
class RecipeIngredient: Food {
    var quantity: Int
    init(name: String, quantity: Int) {
        self.quantity = quantity
        super.init(name: name)
    }
    override convenience init(name: String) {
        self.init(name: name, quantity: 1)
    }
}
```

`RecipeIngredient` 类的构造链如下图：

![](https://docs.swift.org/swift-book/_images/initializersExample02_2x.png)

```swift
class ShoppingListItem: RecipeIngredient {
    var purchased = false
    var description: String {
        var output = "\(quantity) x \(name)"
        output += purchased ? " ✔" : " ✘"
        return output
    }
}
```

`ShoppingListItem` 类的构造链如下图：

![](https://docs.swift.org/swift-book/_images/initializersExample03_2x.png)

在 OC 中，一个简单的构造器如下：

```swift
- (instancetype)init {
    self = [super init];
    if (self) {
    	// ...
    }
    return self;
}
```

这类构造器的结果有两种：一个对象或者返回 `nil`。Swift 中的普通构造器一定会返回一个经过初始化的对象，为了支持构造过程失败的情况，引入了可失败构造器。可失败构造器对于结构体和枚举尤为重要，比如 Swift 中的枚举允许通过原始值初始化，而调用方传入的原始值不一定对应一个枚举值。

```swift
enum TemperatureUnit {
    case Kelvin, Celsius, Fahrenheit
    init?(symbol: Character) {
        switch symbol {
        case "K":
            self = .Kelvin
        case "C":
            self = .Celsius
        case "F":
            self = .Fahrenheit
        default:
            return nil
        }
    }
}

let fahrenheitUnit = TemperatureUnit(symbol: "F")
if fahrenheitUnit != nil {
    print("This is a defined temperature unit, so initialization succeeded.")
}
// 打印“This is a defined temperature unit, so initialization succeeded.”

let unknownUnit = TemperatureUnit(symbol: "X")
if unknownUnit == nil {
    print("This is not a defined temperature unit, so initialization failed.")
}
// 打印“This is not a defined temperature unit, so initialization failed.”
```

特别的，可失败构造器的签名，不能和普通构造器签名相同。并且严格来说，构造器都不支持返回值，因为构造器本身的作用，只是为了确保对象能被正确构造，因此我们只是用 `return nil` 表明可失败构造器构造失败，而不要用关键字 `return` 来表明构造成功。通普通构造器一样，可失败构造器也支持同样规则的代理，不过一旦任何一个环节构造失败，整个构造过程都会直接结束，后续逻辑都不会被执行。我们可以用非可失败构造器重写可失败构造器，但反过来却不行。

必要构造器通过 `required` 修饰，要求所有子类都必须实现该构造器。比如 `UIView` 的 `init?(coder aDecoder: NSCoder)` 方法。


### 泛型

同可选类型一样，泛型也是 Swift 最强大特性之一，很多标准库的实现，都是基于泛型代码构建的，比如数组、字段等。

关于泛型，一个经典的示例就是交换两个值。比如要求实现交换两个整数，交换两个字符串，甚至交换两个对象的函数。如果没有泛型，我们可能需要实现多个类似的方法：

```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}

func swapTwoStrings(_ a: inout String, _ b: inout String) {
    let temporaryA = a
    a = b
    b = temporaryA
}

func swapTwoDoubles(_ a: inout Double, _ b: inout Double) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

上述方法实现一样的功能，唯一的不同在于参数类型。借助泛型，我们可以提供更好的实现：

```swift
func swapTwoValues<T>(_ a: inout T, _ b: inout T) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

事实上，上述代码正是 Swift 标准库中 `swap` 函数的实现。

另一个很好的示例，是实现一个容器栈，考虑到容器元素的未知性和通用性，我们可以利用泛型提供更好的实现：

```swift
struct Stack<Element> {
    var items = [Element]()
    mutating func push(_ item: Element) {
        items.append(item)
    }
    mutating func pop() -> Element {
        return items.removeLast()
    }
}
```

泛型类型同样支持扩展，并且原始定义中的类型占位符在扩展中可以直接使用：

```swift
extension Stack {
    var topItem: Element? {
        return items.isEmpty ? nil : items[items.count - 1]
    }
}
```

上述代码中，我们使用一个类型占位符来标识一个未知的类型。除此之外，我们还可以通过 `where` 语句对占位类型设置约束。比如字典的定义，就要求 `Key` 是满足 `Hashable` 协议的。

```swift
public struct Dictionary<Key, Value> where Key : Hashable {}

// 一种等价的写法
public struct Dictionary<Key: Hashable, Value> {}
```

为了使协议具备泛型能力，Swift 在协议中引入了关联类型的概念：

```swift
protocol Container {
    associatedtype Item
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}

struct IntStack: Container {
    // IntStack 的原始实现部分
    var items = [Int]()
    mutating func push(_ item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
    // Container 协议的实现部分
    typealias Item = Int
    mutating func append(_ item: Int) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Int {
        return items[i]
    }
}
```

关联类型同样支持设置约束：

```swift
protocol Container {
    associatedtype Item: Equatable
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}

protocol SuffixableContainer: Container {
    associatedtype Suffix: SuffixableContainer where Suffix.Item == Item
    func suffix(_ size: Int) -> Suffix
}
```

> 在诸如 SnapKit、Kingfisher 等第三方框架中，广泛使用了泛型，建议深入理解。


### 错误处理

在 OC 中我们通常使用 `NSError` 或者 `NSException` 来表示错误，而错误处理逻辑相对混乱并且得不到保证，我们可以使用使用错误码去判断是什么错误，也可能通过异常捕获去处理错误，更严重的可能是在业务场景中，我们根本不会去处理错误。而 Swift 提供了 4 中错误处理方式，来满足不同的需求：

1. 通过 `throwing` 传递错误
2. 通过 `do-catch` 处理错误
3. 将错误转换成可选值处理
4. 禁用错误传递机制

具体来讲，Swift 中所谓的错误只需要遵循 `Error` 协议，而它其实是一个控协议。通过 `throw` 关键字抛出一个错误：

```swift
enum VendingMachineError: Error {
    case invalidSelection                     //选择无效
    case insufficientFunds(coinsNeeded: Int) //金额不足
    case outOfStock                             //缺货
}

throw VendingMachineError.insufficientFunds(coinsNeeded: 5)
```

Swift 之所以让错误处理更加一致和安全，是因为其在语法层面保证了错误一定得到处理。一个函数如果被 `throws` 标记，则表明该函数可能抛出一个错误，而函数调用方必须提供错误处理逻辑，即上述四种方式中的一种。

`try` 用于 `do-catch` 表达式中：

```swift
var vendingMachine = VendingMachine()
vendingMachine.coinsDeposited = 8
do {
    try buyFavoriteSnack(person: "Alice", vendingMachine: vendingMachine)
    print("Success! Yum.")
} catch VendingMachineError.invalidSelection {
    print("Invalid Selection.")
} catch VendingMachineError.outOfStock {
    print("Out of Stock.")
} catch VendingMachineError.insufficientFunds(let coinsNeeded) {
    print("Insufficient funds. Please insert an additional \(coinsNeeded) coins.")
} catch {
    print("Unexpected error: \(error).")
}
// 打印“Insufficient funds. Please insert an additional 2 coins.”
```

`try?` 用于用于将错误转换为可选值：

```swift
func someThrowingFunction() throws -> Int {
    // ...
}

let x = try? someThrowingFunction()

// 等价形式
let y: Int?
do {
    y = try someThrowingFunction()
} catch {
    y = nil
}
```

`try!` 区别于 `try?`，但是如果发生错误，则会触发一个运行时断言：

```swift
let photo = try! loadImage(atPath: "./Resources/John Appleseed.jpg")
```

因为错误的发生可能导致方法提前结束，所以 Swift 提供了一种更加实用且有保障的清理机制：

```swift
func processFile(filename: String) throws {
    if exists(filename) {
        let file = open(filename)
        defer {
            close(file)
        }
        while let line = try file.readline() {
            // 处理文件。
        }
        // close(file) 会在这里被调用，即作用域的最后。
    }
}
```

`defer` 语句的调用时机是在方法返回前（可能是正常结束，也可能是因为发生错误）。




### 自动引用计数

Swift 和 OC 一样，采用 ARC 机制管理内存，面对循环引用问题，Swift 提供了两种解决方案：

1. 弱引用（weak reference），使用 `weak` 关键字标识；
2. 无主引用（unowned reference），使用 `unowned` 关键字标识。

弱引用和无主引用都不会保留值，不会阻止 ARC 机制回收对象。不同之处在于，弱引用通常用于所引用的对象具有相对自己更短的生命周期的场景，以便在所引用对象释放后自动置为 `nil`，因此弱引用属性都是某种类型的可选类型。而无主引用通常用于所引用对象的生命周期和自身相同甚至更长的场景，主观上期望此类属性一直有值，并且如果引用对象释放，此类属性也不会被置为 `nil`，如果再访问会触发运行时错误，无主引用不是可选值，所以使用上会比弱引用方便一些。

闭包内的循环引用问题同样适用上述两种方案解决，基本语法如下：

```swift
lazy var someClosure: (Int, String) -> String = {
    [unowned self, weak delegate = self.delegate!] (index: Int, stringToProcess: String) -> String in
    // 这里是闭包的函数体
}
```


### 内存安全

Swift 通过 ARC 机制完成了绝大多数内存管理相关的工作，本节主要介绍 Swift 在内存独占性访问上的一些机制，了解这些潜在的内存访问风险，可以避免我们写出访问冲突的代码，加深对内存管理的理解。

内存的访问，通常发生在我们为变量赋值，或者给函数传参的时候，我们需要先理解内存访问冲突是怎么形成的？

内存访问的冲突会发生在代码尝试同时访问同一个存储地址的时侯，同一个存储地址的多个访问同时发生会造成不可预计或不一致的行为。在 Swift 里，有很多修改值的行为都会持续好几行代码，在修改值的过程中进行访问是有可能发生的。如下图：

![](https://docs.swift.org/swift-book/_images/memory_shopping_2x.png)

上述问题会让我们想到多线程中的安全问题，他们的原理确实是一样的，但是我们此处讨论的内存访问冲突，是可以在单线程环境中发生的。

简单来讲，发生内存访问冲突有 3 个条件：

1. 至少有一个写操作；
2. 多个操作访问同一个内存地址；
3. 多个操作在时间线上有重叠。

其中第三点是发生内存访问冲突的关键。通常情况下，内存访问都是瞬时的，不可被打断，但是有两个例外的场景比较常见：

1. `In-Out` 参数；
2. 对 `self` 的访问。

对于 `In-Out` 参数，函数会对其进行长期的写访问，直到函数结束，而非瞬时访问。如下所示：

```swift
var stepSize = 1

func increment(_ number: inout Int) {
    number += stepSize
}

increment(&stepSize)
// 错误：stepSize 访问冲突
```

![](https://docs.swift.org/swift-book/_images/memory_increment_2x.png)

另一个示例：

```swift
func balance(_ x: inout Int, _ y: inout Int) {
    let sum = x + y
    x = sum / 2
    y = sum - x
}
var playerOneScore = 42
var playerTwoScore = 30
balance(&playerOneScore, &playerTwoScore)  // 正常
balance(&playerOneScore, &playerOneScore)
// 错误：playerOneScore 访问冲突
```

同样的，该函数存在两个长期的写访问，当入参是同一个变量时，内存地址一样，所以同样存在内存访问冲突。

另一个常见的场景是在方法中访问 `self`：

```swift
struct Player {
    var name: String
    var health: Int
    var energy: Int

    static let maxHealth = 10
    mutating func restoreHealth() {
        health = Player.maxHealth
    }
}

extension Player {
    mutating func shareHealth(with teammate: inout Player) {
        balance(&teammate.health, &health)
    }
}

var oscar = Player(name: "Oscar", health: 10, energy: 10)
var maria = Player(name: "Maria", health: 5, energy: 10)
oscar.shareHealth(with: &maria)  // 正常
oscar.shareHealth(with: &oscar)
// 错误：oscar 访问冲突
```

![](https://docs.swift.org/swift-book/_images/memory_share_health_maria_2x.png)
![](https://docs.swift.org/swift-book/_images/memory_share_health_oscar_2x.png)

特别的，在实践中，绝大多数对于结构体属性的访问都是安全的重叠访问。限制结构体属性的重叠访问对于保证内存安全不是必要的，但因为访问独占权的要求比内存安全还要更严格，意味着即使有些代码违反了访问独占权的原则，也是内存安全的，所以如果编译器可以保证这种非专属的访问是安全的，那 Swift 就会允许这种行为的代码运行。特别是当你遵循下面的原则时，它可以保证结构体属性的重叠访问是安全的：

1. 你访问的是实例的存储属性，而不是计算属性或类的属性；
2. 结构体是本地变量的值，而非全局变量；
3. 结构体要么没有被闭包捕获，要么只被非逃逸闭包捕获了。

手动乌鸦脸。。。。。。。。。

> 内存访问冲突问题不容易跟进，也不太好理解，建议深入理解内存访问机制，避免这类问题。

### 访问控制

访问控制机制限定了代码的可见范围，允许我们隐藏代码的具体实现。访问级别可用于类、结构体、枚举等类型，也可以用于其中的属性、方法、构造器、下标等。

Swift 提供以下 5 中级别的访问控制：

1. `Open`：最高访问级别，可以被同一模块或者其他模块访问、继承和重写，只能用于类及类的成员；
2. `Public`：可以被同一模块访问、继承和重写，不能被其他模块访问、继承和重写；
3. `Internal`：默认级别，只能在模块内被访问；
4. `File-Private`：同一文件内可以被访问；
5. `Private`：最低访问权限，只能在源定义以及其扩展中被访问。

特别的，如果一个类型被 `Open` 标记，需要充分考虑到被外部模块使用和继承时，作为类型父类的影响。

Swift 中的访问级别遵循一个简单的原则：实体不能定义在具有更低访问级别（更严格）的实体中。比如一个 `Public` 的变量，其类型的访问级别不能是 `Internal`，`File-private` 或是 `Private`。因为无法保证变量的类型在使用变量的地方也具有访问权限。











