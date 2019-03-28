# swift-notes

> Swift 5 学习笔记。

### A Swift Tour

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

2. Swift 类型严格，因此 `if` 条件返回 `boolean` 值：
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


### The Basics



### Basic Operators



### Strings and Characters

1. 多行字符串的缩进问题


### Collection Types



### Control Flow



### Functions



### Closures



### Enumerations



### Classes and Structures



### Properties



### Methods



### Subscripts



### Inheritance



### Initialization



### Deinitialization



### Optional Chaining



### Error Handling



### Type Casting



### Nested Types



### Extensions



### Protocols



### Generics



### Automatic Reference Counting



### Memory Safety



### Access Control



### Advanced Operators
