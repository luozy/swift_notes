#### Table of Content

* [Simple Values](https://github.com/MicroAndSpeed/swift_notes/blob/master/0002_Tour_Swfit2.md#simple-values)
* [Control Flow](https://github.com/MicroAndSpeed/swift_notes/blob/master/0002_Tour_Swfit2.md#control-flow)
* [Functions and Closures](https://github.com/MicroAndSpeed/swift_notes/blob/master/0002_Tour_Swfit2.md#functions-and-closures)
* [Objects and Classes](https://github.com/MicroAndSpeed/swift_notes/blob/master/0002_Tour_Swfit2.md#objects-and-classes)
* [Enumerations and Structures](https://github.com/MicroAndSpeed/swift_notes/blob/master/0002_Tour_Swfit2.md#enumerations-and-structures)
* [Protocols and Extensions](https://github.com/MicroAndSpeed/swift_notes/blob/master/0002_Tour_Swfit2.md#protocols-and-extensions)
* [Generics](https://github.com/MicroAndSpeed/swift_notes/blob/master/0002_Tour_Swfit2.md#generics)

#### Simple Values

`var`用于声明变量，`let`用于声明常量。

```Swift
var myVariable = 42
myVariable = 50
let myConstant = 42
```

可以使用冒号`:`进行显示声明变量、常量的类型。

```Swift
let implicitInteger = 70
let implicitDouble = 70.0

let explicitDouble: Double = 70
```

变量/常量的值是不能够进行隐式的类型转换的，也就是说，如果我们需要转换一个值到不同的类型，就需要将值赋给显示声明类型的另一个变量/常量。

```Swift
let label = "The width is "
let width = 94

let widthLabel = label + String(width)
```

可以使用`\(...)`的方式，对字符串进行拼接。

```Swift
let apples = 3
let oranges = 5
let appleSummary = "I have \(apples) apples."
let fruitSummary = "I have \(apples + oranges) pieces of fruit."
```

使用`[...]`来声明数组或者字典类型，然后再`[...]`中使用数值来选择数组或字典类型变量的元素。

_ps：`,`允许在数组或字典类型的最后一个元素的后面。_

```Swift
var shoppingList = ["catfish", "water", "tulips", "blue paint"]
shoppingList[1] = "bottle of water"

var occupations = [
    "Malcolm": "Captain",
    "Kaylee": "Mechanic",
]
occupations["Jayne"] = "Public Relations"
```

可以使用初始化字符来声明空数组或空字典。

```Swift
let emptyArray = [String]()
let emptyDictionary = [String: Float]()
```

如果类型可以被推断出来，也可以不显示表明类型信息。

```Swift
shoppingList = []
occupations = [:]
```

#### Control Flow

使用`if-else`与`switch-case`进行条件控制，使用`for-in`、`while`与`repeat-while`进行循环控制。

_ps： 标识条件的圆括号是可选的，标识操作体的大括号是必须的。_

```Swift
let individualScores = [75, 43, 103, 87, 12]
var teamScore = 0

for score in individualScores {
    if score > 50 {
        teamScore += 3
    } else {
        teamScore += 1
    }
}
print(teamScore)
```

可以将`let`与`if-else`结合起来使用，在`if`语句的条件中，使用`let`声明一个常量，并赋给这个常量一个`optional`变量。当变量为`nil`时，条件为`false`。

```Swift
var optionalString: String? = "Hello"
print(optionalString == nil)
 
var optionalName: String? = "John Appleseed"
var greeting = "Hello!"
if let name = optionalName {
    greeting = "Hello, \(name)"
}
```

`switch`支持所有的数据类型以及比较方式。

```Swift
let vegetable = "red pepper"
switch vegetable {
case "celery":
    let vegetableComment = "Add some raisins and make ants on a log."
case "cucumber", "watercress":
    let vegetableComment = "That would make a good tea sandwich."
case let x where x.hasSuffix("pepper"):
    let vegetableComment = "Is it a spicy \(x)?"
default:
    let vegetableComment = "Everything tastes good in soup."
}
```

`for-in`可以用来遍历数组或字典。

_ps：由于字典中的元素是没有顺序的，所以遍历字典时，遍历的顺序是随机的。_

```Swift
let interestingNumbers = [
    "Prime": [2, 3, 5, 7, 11, 13],
    "Fibonacci": [1, 1, 2, 3, 5, 8],
    "Square": [1, 4, 9, 16, 25],
]

var largest = 0

for (kind, numbers) in interestingNumbers {
    for number in numbers {
        if number > largest {
            largest = number
        }
    }
}

print(largest)
```

`while`条件成立时，开始循环。

```Swift
var n = 2
while n < 100 {
    n = n * 2
}
print(n)
```

`while`条件成立时，跳出循环。

```Swift
var m = 2
repeat {
    m = m * 2
} while m < 100
print(m)
```

可以使用`N..<M`在循环条件中生成一个从N到M的数值序列。

```Swift
var firstForLoop = 0
for i in 0..<4 {
    firstForLoop += i
}
print(firstForLoop)
```

或

```Swift
var secondForLoop = 0
for var i = 0; i < 4; ++i {
    secondForLoop += i
}
print(secondForLoop)
```

#### Functions and Closures

使用`func`声明函数，使用`->`标识返回值类型。

```
func greet(name: String, day: String) -> String {
    return "Hello \(name), today is \(day)."
}

greet("Bob", day: "Tuesday")
```

使用一个元组，来返回多个值。

```
func calculateStatistics(scores: [Int]) -> (min: Int, max: Int, sum: Int) {
    var min = scores[0]
    var max = scores[0]
    var sum = 0
    
    for score in scores {
        if score > max {
            max = score
        } else if score < min {
            min = score
        }
        sum += score
    }
    
    return (min, max, sum)
}

let statistics = calculateStatistics([5, 3, 100, 3, 9])

print(statistics.sum)
print(statistics.2)
```

函数可以接收多个同样类型的传入参数，然后将他们放入一个参数数组当中。

```
func sumOf(numbers: Int...) -> Int {
    var sum = 0
    for number in numbers {
        sum += number
    }
    return sum
}
sumOf()
sumOf(42, 597, 12)
```

可以在函数中定义声明函数。

```
func returnFifteen() -> Int {
    var y = 10
    func add() {
        y += 5
    }
    add()
    return y
}
returnFifteen()
```

函数是一等公民，也就是说，函数可以返回另一个函数。

```
func makeIncrementer() -> (Int -> Int) {
    func addOne(number: Int) -> Int {
        return 1 + number
    }

    return addOne
}

var increment = makeIncrementer()
increment(7)
```

可以将函数作为传入参数。

```
func hasAnyMatches(list: [Int], condition: Int -> Bool) -> Bool {
    for item in list {
        if condition(item) {
            return true
        }
    }
    return false
}

func lessThanTen(number: Int) -> Bool {
    return number < 10
}

var numbers = [20, 19, 7, 12]
hasAnyMatches(numbers, condition: lessThanTen)
```

函数是一种特殊的闭包，一种可以在稍后被调用的代码段。可以直接使用`{...}`创建闭包，使用`in`关键字来区分输入输出参数与函数体。

```
numbers.map({
    (number: Int) -> Int in
    let result = 3 * number
    return result
})
```

当闭包的类型是已知的，例如一个代理方法的回调，我们可以省略参数的类型、返回值的类型，或者两者都省略。

```
let mappedNumbers = numbers.map({ number in 3 * number })
print(mappedNumbers)
```

可以关联数字与参数来代替使用参数名称。

```
let sortedNumbers = numbers.sort { $0 > $1 }
print(sortedNumbers)
```

#### Objects and Classes

类声明：

```
class Shape {
    var numberOfSides = 0
    func simpleDescription() -> String {
        return "A shape with \(numberOfSides) sides."
    }
}
```

初始化、属性赋值、成员方法调用：

```
var shape = Shape()
shape.numberOfSides = 7
var shapeDescription = shape.simpleDescription()
```

使用`init`函数作为构造函数，使用`deinit`函数作为析构函数：

```
class NamedShape {
    var numberOfSides: Int = 0
    var name: String
    
    init(name: String) {
        self.name = name
    }
    
    func simpleDescription() -> String {
        return "A shape with \(numberOfSides) sides."
    }
}
```

在类名后接`:`用以标识父类，子类中使用`override`关键字标识重写父类同名方法：

```
class Square: NamedShape {
    var sideLength: Double
    
    init(sideLength: Double, name: String) {
        self.sideLength = sideLength
        super.init(name: name)
        numberOfSides = 4
    }
    
    func area() ->  Double {
        return sideLength * sideLength
    }
    
    override func simpleDescription() -> String {
        return "A square with sides of length \(sideLength)."
    }
}

let test = Square(sideLength: 5.2, name: "my test square")
test.area()
test.simpleDescription()
```

成员属性可以有一个提取器和一个设置器：

```
class EquilateralTriangle: NamedShape {
    var sideLength: Double = 0.0
    
    init(sideLength: Double, name: String) {
        self.sideLength = sideLength
        super.init(name: name)
        numberOfSides = 3
    }
    
    var perimeter: Double {
        get {
            return 3.0 * sideLength
        }
        set {
            sideLength = newValue / 3.0
        }
    }
    
    override func simpleDescription() -> String {
        return "An equilateral triangle with sides of length \(sideLength)."
    }
}

var triangle = EquilateralTriangle(sideLength: 3.1, name: "a triangle")
print(triangle.perimeter)

triangle.perimeter = 9.9
print(triangle.sideLength)
```

可以使用`willSet`来标识类成员属性的值**一直等于**：

```
class TriangleAndSquare {
    var triangle: EquilateralTriangle {
        willSet {
            square.sideLength = newValue.sideLength
        }
    }
    var square: Square {
        willSet {
            triangle.sideLength = newValue.sideLength
        }
    }
    init(size: Double, name: String) {
        square = Square(sideLength: size, name: name)
        triangle = EquilateralTriangle(sideLength: size, name: name)
    }
}

var triangleAndSquare = TriangleAndSquare(size: 10, name: "another test shape")
print(triangleAndSquare.square.sideLength)
print(triangleAndSquare.triangle.sideLength)

triangleAndSquare.square = Square(sideLength: 50, name: "larger square")
print(triangleAndSquare.triangle.sideLength)
```

在操作`optional`对象时，可以在后面加上`?`标识符，如果可选对象为`nil`，则整个表达式的结果为`nil`。

```
let optionalSquare: Square? = Square(sideLength: 2.5, name: "optional square")
let sideLength = optionalSquare?.sideLength
```

#### Enumerations and Structures

使用`enum`来创建一个枚举类型。与其他类型类似，枚举类型也可以有自己的成员函数。

```
enum Rank: Int {
    case Ace = 1
    case Two, Three, Four, Five, Six, Seven, Eight, Nine, Ten
    case Jack, Queen, King

    func simpleDescription() -> String {
        switch self {
        case .Ace:
            return "ace"
        case .Jack:
            return "jack"
        case .Queen:
            return "queen"
        case .King:
            return "king"
        default:
            return String(self.rawValue)
        }
    }
}

let ace = Rank.Ace
let aceRawValue = ace.rawValue
```



```
if let convertedRank = Rank(rawValue: 3) {
    let threeDescription = convertedRank.simpleDescription()
}
```



```
enum Suit {
    case Spades, Hearts, Diamonds, Clubs
    func simpleDescription() -> String {
        switch self {
        case .Spades:
            return "spades"
        case .Hearts:
            return "hearts"
        case .Diamonds:
            return "diamonds"
        case .Clubs:
            return "clubs"
        }
    }
}
let hearts = Suit.Hearts
let heartsDescription = hearts.simpleDescription()
```



```
struct Card {
    var rank: Rank
    var suit: Suit
    func simpleDescription() -> String {
        return "The \(rank.simpleDescription()) of \(suit.simpleDescription())"
    }
}
let threeOfSpades = Card(rank: .Three, suit: .Spades)
let threeOfSpadesDescription = threeOfSpades.simpleDescription()
```



```
enum ServerResponse {
    case Result(String, String)
    case Error(String)
}

let success = ServerResponse.Result("6:00 am", "8:09 pm")
let failure = ServerResponse.Error("Out of cheese.")
 
switch success {
case let .Result(sunrise, sunset):
    let serverResponse = "Sunrise is at \(sunrise) and sunset is at \(sunset)."
case let .Error(error):
    let serverResponse = "Failure...  \(error)"
}
```

#### Protocols and Extensions

```
protocol ExampleProtocol {
    var simpleDescription: String { get }
    mutating func adjust()
}
```



```
class SimpleClass: ExampleProtocol {
    var simpleDescription: String = "A very simple class."
    var anotherProperty: Int = 69105
    func adjust() {
        simpleDescription += "  Now 100% adjusted."
    }
}
var a = SimpleClass()
a.adjust()
let aDescription = a.simpleDescription
 
struct SimpleStructure: ExampleProtocol {
    var simpleDescription: String = "A simple structure"
    mutating func adjust() {
        simpleDescription += " (adjusted)"
    }
}
var b = SimpleStructure()
b.adjust()
let bDescription = b.simpleDescription
```



```
extension Int: ExampleProtocol {
    var simpleDescription: String {
        return "The number \(self)"
    }
    mutating func adjust() {
        self += 42
    }
}
print(7.simpleDescription)
```



```
let protocolValue: ExampleProtocol = a
print(protocolValue.simpleDescription)
// print(protocolValue.anotherProperty)  // Uncomment to see the error
```

#### Generics

```
func repeatItem<Item>(item: Item, numberOfTimes: Int) -> [Item] {
    var result = [Item]()
    for _ in 0..<numberOfTimes {
        result.append(item)
    }
    return result
}
repeatItem("knock", numberOfTimes:4)
```



```
// Reimplement the Swift standard library's optional type
enum OptionalValue<T> {
    case None
    case Some(T)
}
var possibleInteger: OptionalValue<Int> = .None
possibleInteger = .Some(100)
```


```
func anyCommonElements <T, U where T: SequenceType, U: SequenceType, T.Generator.Element: Equatable, T.Generator.Element == U.Generator.Element> (lhs: T, _ rhs: U) -> Bool {
    for lhsItem in lhs {
        for rhsItem in rhs {
            if lhsItem == rhsItem {
                return true
            }
        }
    }
    return false
}
anyCommonElements([1, 2, 3], [3])
```
