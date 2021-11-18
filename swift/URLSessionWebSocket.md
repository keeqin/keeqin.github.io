# URL Loading System
1. 使用URLSession创建一个或多个URLSessionTask实例，可以用来下载文件或数据和上传文件或数据
2. URLSessionConfiguration对象可以用来控制行为，比如是否使用缓存和cookie或是否使用蜂窝网络
3. 一个session可以创建多个task
4. session可分为regular和private，private类型不能使用缓存
5. 获取数据是异步的，不会影响UI响应用户交互
 
## URLSession
#### 可以创建4种session
1. shared:不能自定义配置，不能使用delegate
2. default:可以自定义配置，可以使用delegate
3. ephemeral:和default相似,但不能使用cookies,caches和写文件到磁盘
4. background:可以在后台上传或下载内容
 
## URLSessionTask
#### task的4种类型
1. Data Task 发送或接收短数据
2. Upoad Task 类似Data Task,但可以在后台上传数据文件
3. Download Task 类似Data Task,但可以在后台下载数据文件
4. Websocket Task 用于websocket通信
 
## 使用delegate
#### 强引用:不使用时不失效session会导致内存泄漏
#### Session中的Task共享同一个delagate
#### 作用:
1. 验证失败
2. 收到数据
3. 数据可用于缓存
 
## 支持协议
#### data, file, ftp, http, and https (HTTP/1.1, HTTP/2, and HTTP/3)
## 特性
#### 异步、ATS、线程安全、session,task和它的configuration支持NSCopying


# swift WebSocket的使用:
## 创建一个urlSession
let session = URLSession(configuration: .default, delegate: nil, delegateQueue: nil)  
或者sharedURLSession(不能自定义配置,不能使用delegate)
let session = URLSession.shared
## 创建一个url
let url = URL(string: "ws://127.0.0.1")
## 创建一个websocketTask,声明为var在重连的时候使用，let不能重连
var webSocket = session.webSocketTask(with: MyWebSocket.url!)
## 开始连接
 webSocket.resume()
## 发送数据
```swift
webSocket.send(URLSessionWebSocketTask.Message.string("123")) { error in
    if let error = error {
        print("发送失败")
    }
}
```
## 接收数据
```swift
func rec() {
    webSocket.receive { result in
        switch result {
        case .failure(let error) :
            let code = (error as NSError).code
            print("收到一个错误:\(code)")
            return
        case .success(let msg) :
            switch msg {
            case .string(let str) :
                print(str)
            case .data(let data) :
                print(String(data: data, encoding: .utf8) ?? "未知数据")
            @unknown default:
                print("unkonw")
            }
        }
        rec()
    }
}
//启动接收数据
rec()
```
 
 
## websocket判断连接状态
 swift不想java那样有一个isConnect()返回连接状态的成员方法,所以得有自己的方法
1. 定义一个变量表示连接状态,默认未连接
 var isConnect = false
2. 创建一个遵循URLSessionWebSocketDelegate协议的类,在里面更改连接状态
```swift
class MyDelegate: NSObject, URLSessionWebSocketDelegate {
//连接成功时执行
    func urlSession(_ session: URLSession, webSocketTask: URLSessionWebSocketTask, didOpenWithProtocol rotocol: String?) {
        isConnect = true
    }
//连接连接失败或断开时执行
func urlSession(_ session: URLSession, webSocketTask: URLSessionWebSocketTask, didCloseWith closeCode: URLSessionWebSocketTask.CloseCode, reason: Data?) {
        isConnect = false
    }
}
```
3. 创建一个websocket类在里面处理相关消息
```swift
class MyWebSocket {
    static let url = URL(string: "ws://127.0.0.1")
    static let delegate = MyDelegate()
    static let session = URLSession(configuration: .default, delegate: delegate, delegateQueue: nil)
    static var webSocket = session.webSocketTask(with: MyWebSocket.url!)
    init() {
        rec()
        MyWebSocket.webSocket.resume()
    }
    func rec() {
        MyWebSocket.webSocket.receive { result in
        switch result {
            case .failure(let error) :
                let code = (error as NSError).code
            //将标志置为false
                isConnect = false
                MyWebSocket.webSocket = MyWebSocket.urlSession.webSocketTask(with: MyWebSocket.url!)
                MyWebSocket.webSocket.resume()
                self.rec()
                return
            case .success(let msg) :
                switch msg {
                    case .string(let str) :
                        print(str)
                    case .data(let data) :
                        print(String(data: data, encoding: .utf8) ?? "未知数据")
                    @unknown default:
                        print("unkonw")
                }
            }
            self.rec()
        }
    }
}
```