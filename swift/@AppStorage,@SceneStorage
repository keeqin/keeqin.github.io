# @AppStorage 
* 数据可持久化，app退出后数据仍保留
* 仅包装了UserDefault，数据可以UserDefault正常读取
* 可保存的数据类型同UserDefault，不适合保存复杂类型数据
* 在app的任意View层级都可适用，不过在app层使用并不起作用（不报错）

# @SceneStorage
* 数据作用域仅限于Scene中
* 生命周期同Scene一致，当前在PadOS下，如果强制退出一个两分屏显示的app,系统在下次打开app时有时会保留上次的Scene信息。不过，如果如果单独退出一个Scene，数据则失效
* 支持的类型基本等同于@AppStorage，适合保存轻量数据
* 比较适合保存基于Scene的特质信息，比如TabView的选择，独立布局等数据
