## 闭包
**闭包**是自包含的函数代码块。  
* 全局函数是一个有名字但是不会捕获任何值的闭包
* 嵌套函数是一个有名字并可以捕获其封闭函数域内值的闭包
* 闭包表达式是一个利用轻量语法所写的可以捕获其上下文中变量或常量值的匿名闭包
### 闭包表达式语法
一般形式：
```swift
{(parameters) -> return type in 
    statements
}
//也可以写成一行
```
对于一个排序函数,传入闭包：
```swift
//一般形式
name.sort(by: {(s1: String, s2: String) -> Bool in return s1 > s2 })

//自动类型推断和隐式返回
name.sort(by: { s1, s2 in s1 > s2 })

//参数名称缩写
name.sort(by: { $0 > $1 })
```
运算符法：
```swift
name.sort(by: >)
```

### 尾随闭包
将一个很长的闭包表达式作为最后一个参数传递给函数，将这个闭包替换成为尾随闭包的形式很有用。  
尾随闭包是一个写在函数圆括号之后的闭包表达式，并且不用写出参数标签
```swift
func someFunctionThatTakesAClosure(closure: () -> Void) {
    // 函数体部分
}

// 以下是不使用尾随闭包进行函数调用
someFunctionThatTakesAClosure(closure: {
    // 闭包主体部分
})

// 以下是使用尾随闭包进行函数调用
someFunctionThatTakesAClosure() {
    // 闭包主体部分
}
```

### 值捕获
闭包可以在其被定义的上下文中捕获常量或变量。即使定义这些常量和变量的原作用域已经不存在
### 闭包是引用类型
### 逃逸闭包
当一个闭包作为参数传到一个函数中，但是这个闭包在函数返回之后才被执行，则称该闭包从函数中逃逸。  
当你定义接受闭包作为参数的函数时，你可以在参数名之前标注 @escaping，用来指明这个闭包是允许“逃逸”出这个函数的。
### 自动闭包
```swift
var customersInLine = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
print(customersInLine.count)
// 打印出“5”

let customerProvider = { customersInLine.remove(at: 0) }
print(customersInLine.count)
// 打印出“5”

print("Now serving \(customerProvider())!")
// 打印出“Now serving Chris!”
print(customersInLine.count)
// 打印出“4”
```
## 枚举
### 枚举语法
```swift
enum CompassPoint {
    case north
    case south
    case east
    case west
}
var directionToHead = CompassPoint.west
```
### 使用switch匹配枚举值
```swift
directionToHead = .south
switch directionToHead {
case .north:
    print("Lots of planets have a north")
case .south:
    print("Watch out for penguins")
case .east:
    print("Where the sun rises")
case .west:
    print("Where the skies are blue")
}
// 打印“Watch out for penguins”
```
### 枚举成员遍历
要遍历需要遵循CaseIterable协议，会得到一个allCases属性，包含所有的值。
```swift
enum Beverage: CaseIterable {
    case coffee, tea, juice
}
let numberOfChoices = Beverage.allCases.count
print("\(numberOfChoices) beverages available")
// 打印“3 beverages available”

for beverage in Beverage.allCases {
    print(beverage)
}
// coffee
// tea
// juice
```
### 关联值
### 原始值
给枚举类型填充一个基本类型，枚举中的元素就全部是这种类型，并且有一个默认的原始值，也可手动赋值。
```swift
enum ASCIIControlCharacter: Character {
    case tab = "\t"
    case lineFeed = "\n"
    case carriageReturn = "\r"
}
enum CompassPoint: String {
    case north, south, east, west
}
enum Planet: Int {
    case mercury = 1, venus, earth, mars, jupiter, saturn, uranus, neptune
}

let earthsOrder = Planet.earth.rawValue
// earthsOrder 值为 3

let sunsetDirection = CompassPoint.west.rawValue
// sunsetDirection 值为 "west"
```
### 递归枚举

## 类和结构体
可以关联一组*属性*和*方法*的代码块  
示例：
```swift
struct Resolution {
    var width = 0
    var height = 0
}
class VideoMode {
    var resolution = Resolution()
    var interlaced = false
    var frameRate = 0.0
    var name: String?
}
let someResolution = Resolution()
let someVideoMode = VideoMode()
```
### 结构体类型的逐一构造
需要给没有默认值的属性赋值，否则编译会报错
```swift
let vga = Resolution(width: 640, height: 480)
```
*类*类型不需要注意构造

### 结构体和枚举是值类型
传递时会开辟一个新空间
除了结构体和枚举，Swift 中所有的基本类型：整数（integer）、浮点数（floating-point number）、布尔值（boolean）、字符串（string)、数组（array）和字典（dictionary），都是值类型，其底层也是使用结构体实现的。
### 类是引用类型】
传递时不会开辟新空间
相当于C语言中分配内存，然后定义一个指针指向该内存区域
#### 恒等运算符
=== 和 !==
使用这两个运算符检测两个常量或者变量是否引用了同一个实例
## 属性
分为存储属性和计算属性。存储属性只能用于类和结构体，计算属性可以用于类、结构体和枚举。
### 存储属性
一个存储属性就是存储在特定类或结构体实例里的一个常量或变量。  
存储属性可以是变量存储属性（用关键字 var 定义），也可以是常量存储属性（用关键字 let 定义）。
### 延时加载的存储属性
使用lazy关键字，当属性第一次被调用时才会计算其初始值，而不是创建结构体或类的时候。lazy 必须与 var 一起使用。
```swift
class DataManager {
    //延时加载耗时的属性
    lazy var importer = DataImporter()
    var data: [String] = []
    // 这里会提供数据管理功能
}
```
### 计算属性
计算属性让属性不直接存储值，而是提供一个 getter 和一个可选的 setter，来间接获取和设置其他属性或变量的值。

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
#### 简化getter声明
当getter中只有一条表达式时可以省略return关键字
#### 只读计算属性
只有 getter 没有 setter 的计算属性叫只读计算属性。只读计算属性返回一个值，但不能设置新的值。  
必须使用 var 声明一个只读属性，因为它的值不是固定的，比如返回一个立方体的体积：
```swift
struct Cuboid {
    var width = 0.0, height = 0.0, depth = 0.0
    var volume: Double {
        return width * height * depth
    }
}
let fourByFiveByTwo = Cuboid(width: 4.0, height: 5.0, depth: 2.0)
print("the volume of fourByFiveByTwo is \(fourByFiveByTwo.volume)")
// 打印“the volume of fourByFiveByTwo is 40.0”
```
### 属性观察器
```swift
class StepCounter {
    var totalSteps: Int = 0 {
        //赋值前执行
        willSet(newTotalSteps) {
            print("将 totalSteps 的值设置为 \(newTotalSteps)")
        }

        //赋值完成后执行
        didSet {
            if totalSteps > oldValue  {
                print("增加了 \(totalSteps - oldValue) 步")
            }
        }
    }
}
```
### 属性包装器
@propertyWapper修饰的结构体作为属性包装器
### 全局变量和局部变量
全局变量是在函数、方法、闭包或任何类型之外定义的变量。局部变量是在函数、方法或闭包内部定义的变量。  
在全局或局部范围都可以定义计算型变量和为存储型变量定义观察器。计算型变量跟计算属性一样，返回一个计算结果而不是存储值，声明格式也完全一样。
### 类型属性
struct,enum,class中的属性用static修饰。类型属性可以直接通过类型访问，而不是通过实例访问
## 实例方法
结构体，枚举，类中的方法叫实例方法。  
结构体和枚举是值类型。默认情况下，值类型的属性不能在它的实例方法中被修改。加上mutating就可以修改。
```swift
struct Point {
    var x = 0.0, y = 0.0
    mutating func moveBy(x deltaX: Double, y deltaY: Double) {
        x += deltaX
        y += deltaY
    }
}
var somePoint = Point(x: 1.0, y: 1.0)
somePoint.moveBy(x: 2.0, y: 3.0)
print("The point is now at (\(somePoint.x), \(somePoint.y))")
// 打印“The point is now at (3.0, 4.0)”
```