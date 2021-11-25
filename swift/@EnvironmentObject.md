# @EnvironmentObject用法:
作用:用来将对象放到环境中，需要主动将对象注入要使用该变量的view中，该view及其子view都能从环境中提取到该对象
1. 创建一个可被观察的类，将要观察的属性Published出去  
```swift
Class Program: ObservableObject {
	@Published var language = “swift”
}
```
2. 在view中显示这个属性
```swift
struct FirestView: View {
	//使用@EnvironmentObject自动从环境中提取这个类的数据
	@EnvironmentObject var program: Program
	var body: some View {
		//显示数据
		Text(program.language)
	}
}
```
//别的视图中也能直接使用
```swift
struct SecondView: View {
	//使用@EnvironmentObject自动从环境中提取这个类的数据
	@EnvironmentObject var program: Program
	var body: some View {
		//显示数据
		Text(program.language)
	}
}
```
3. 最后一步，将对象注入的相应的环境中
```swift
//别的视图中也能直接使用
struct ContentView: View {
	var body: some View {
		VStack {
            FirestView()
            SecondView()
        }
	}
}
struct GardenApp: App {

    @StateObject private var program = Program()

    var body: some Scene {
        WindowGroup {
	//将对象注入到ContentView的环境中，这样它和它的子视图都能通过@EnvironmentObject直接访问这个对象
            ContentView()
                .environmentObject(program)
        }
    }
}
```
