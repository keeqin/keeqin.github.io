
 # @NSCopying:
 功能:使引用类型的赋值变得跟值类型的一样
 ### 使用限制:
 1. 只能修饰类中的属性
 2. 只能用来修饰一个遵循NSCopying协议的属性
 使用方法:
 1. 创建一个遵循NSCopyingd协议的类Rectangle
 ``` swift
class Rectangle:  NSCopying {
    var width = 10
    var height = 10
    func copy(with zone: NSZone? = nil) -> Any {
        print("copyed")
        let copy = Rectangle()
        copy.width = width
        copy.height = height
        return copy
    }
}
 ```
 2. 创建一个包含类RoundRectangle,且有一个Rectangle类型的属性,在属性最前面添加@NSCopying修饰
 ``` swift
 class RoundRectangle {
    @NSCopying var rectangle: Rectangle?
    var corner = 5
}
 ```
 3. 验证
 ``` swift
    let r1 = Rectangle()  
    var roundRectangle = RoundRectangle(rectangle: r1)  
    r1 === roundRectangle.rectangle //true,两个是相同的实例  
    let r2 = Rectangle()  
    roundRectangle.rectangle = r2  
    r2 === roundRectangle.rectangle //false,两个是不相同的实例  
 ```
 ### 结论:使用构造函数初始化时是相同的实例,用=初始化或赋值时拷贝的实例
