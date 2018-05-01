# Swift 规范

参考：[Swift 最佳实践]，[The Swift Programming Language]，[swift-style-guide]

[Swift 最佳实践]: https://github.com/KevinHM/ios-good-practices-the-lastest-version/edit/master/Swift-Best-Practices.md
[The Swift Programming Language]: https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/index.html
[swift-style-guide]: https://github.com/github/swift-style-guide


### 原则

* 自文档形式的命名，不要起模棱两可的命名（变量、类型、方法等）
* 语法简洁，多用类型推导
* 访问控制权限默认从最小开始


### 命名

- 驼峰命名法(例如: " VehicleController, vehicleName " )
- 使用 Swift 模块来定义代码的命名空间，而非Objective-C 样式的类前缀(除非接口要与 Objective-C 交互)
- 不推荐使用任何形式的[匈牙利命名法]（比如：k 代表常量，m 代表方法）, 不要使用类似 `SNAKE_CASE` 这样的名字
- 没必要的情况下，命名不要使用缩写
- URL, HTTP, JSON 此类固定的词作为命名开始时全部小写，其它情况全部大写(例如：let url = ..., let fileURL, func downloadFile(_ fileName: String, URL: URL), class HTTPClient)


### 类型推导

尽可能的使用 Swift 的类型推导，以避免冗余的类型信息。例如：

推荐：

```Swift
var currentLocation = Location()
```

而非：

```Swift
var currentLocation: Location = Location()
```

### 能不写类型参数的就别写了


### 内省

让编译器自动推断所有的情况，这是可以做到的。在一些领域 self 应该被显式地使用，包括在 init 中设置参数，或者 `non-escaping`闭包。例如：

```Swift
struct Example {
    let name: String
    
    init(name: String) {
        self.name = name
    }
}
```

### 捕获列表的类型推导

在一个捕获列表( capture list )中指定参数类型会导致代码冗余。如果需要的话，仅指定类型即可。

```Swift
let people = [
    ("Mary", 42),
    ("Susan", 27),
    ("Charlie", 18),
]

let strings = people.map() {
    (name: String, age: Int) -> String in
    return "\(name) is \(age) years old"
}
```

如果编译器可以推导出来的话，完全可以把类型删掉：

```Swift
let strings = people.map() {
    (name, age) in 
    return "\(name) is \(age) years old"
}
```

使用编号的参数名 ("$0") 进一步降低冗长，往往能彻底消除捕获列表的代码冗余。在闭包中当参数名没有附带任何更多信息时仅使用编号形式即可( 如非常简单的映射和过滤器 )。

Apple 能够并且将会改变闭包的参数类型，通过他们的 Objective-C 框架的 Swift 变种提供出来。例如，`optionals` 被删除或更改为 `auto-unwrapping` 等。故意 under-specifying 可选并依赖 Swift 来推导类型，可以减少在这些情况下代码被破译的风险。

你应该避免指定返回类型，例如这个捕获列表( capture list )就是完全多余的:

```Swift
dispatch_async(queue) {
    ()->Void in
    print("Fired.")
}
```

### 常量

类型定义中使用的常量应当被申明成静态类型。例如:

```Swift
struct PhysicsModel {
    static var speedOfLightInAVacuum = 299_792_458
}

class Spaceship {
    static let topSpeed = PhysicsModel.speedOfLightInAVacuum
    var speed: Double
    
    func fullSpeedAhead() {
        speed = Spaceship.topSpeed
    }
}

```

将常量标示为 static ，允许它们可以被无类型的实例引用。

一般应该避免生成全局范围的常量，单例除外。


### 计算属性

如果你只是为了实现一个 getter，请使用短版 (short version) 的计算属性。例如：

推荐这样：

```Swift
class Example {
    var age: UInt32 {
        return arc4random()
    }
}
```

不要：

```Swift
class Example {
    var age: UInt32 {
        get {
            return arc4random()
        }
    }
}
```

如果你在属性里添加一个 `set` 或者 `didSet`， 那么你需要显示提供一个 `get`。

```Swift
class Person {
    var age: Int {
        get {
            return Int(arc4random())
        }
        set {
            pint("That's not your age.")
        }
    }
}
```

### 实例的转换

将一种类型的实例转换为到另一种类型实例时的`init()`方法：

```Swift
extension NSColor {
    convenience init(_ mood: Mood) {
        super.init(color: NSColor.blueColor)
    }
}
```

现在在 Swift 标准库中实现这种转换 `init` 方法似乎是首选。

"to" 方法是另一种合理的方法(虽然你应该遵循 Apple 的指引使用 `init` 方法)。

```Swift
struct Mood {
    func toColor() -> NSColor {
        return NSColor.blueColor()
    }
}
```

虽然你可能会使用 getter, 例如：

```Swift
struct Mood {
    var color: NSColor {
        return NSColor.blueColor()
    }
}
```

一般来说 getter 应该被限定返回接收类型的组件。例如，返回一个 `Circle` 实例的面积就很适合使用 getter,但是将一个 `Circle` 转换为一个 `CGPath` 使用 "to" 函数或者一个 `CGPath` 的 `init()` 扩展会更好。

### 单例

在 Swift 中，生成单例很简单：

```Swift
class ControversyManager {
    static let shared = ControversyManager()
}
```
Swift 的 runtime 将确保以一种线程安全的方式来创建和访问单例。

单例一般仅通过 `shared` 静态属性来访问.不要使用静态函数或者全局函数来访问单例。


### 代码组织的扩展

扩展应该被用于帮助组织代码。

当方法和属性是一个实例的外围延伸时，应该被迁移到某个扩展。注意，目前并不是所有的属性类型都可以被迁移到扩展 --- 在这个限制范围内尽你所能。

你应该使用扩展来帮助组织你的实例的定义。这方面很好的一个例子就是：一个实现了表视图数据源和委托协议的视图控制器。它没有将所有的表视图代码混成一个类，而是把数据源和委托方法放到了相关的协议扩展中。

可以根据你的觉得最好的方式将一个源文件随意分解并定义成任何的扩展，以重新组织这堆问题代码。别担心主类中的方法或扩展里指向方法和属性的结构定义。只要它们在一个 Swift 文件中就没事。

相反，主实例定义不应该指向在主 Swift 文件之外的主扩展中定义的元素。。。

### 链式 Setters

不要为了图方便而使用链式 setter 来替代简单的属性 setter ：

推荐：

```Swift
instance.foo = 42
instance.bar = "xyzzy"
```

不推荐：

```Swift
instance.setFoo(42).setBar("xyzzy")
```
比起链式 setter 传统的 setter 更简单并且需要的样板代码更少。


### 错误处理

Swift 2 的 `do/ try/ catch` 机制非常棒，推荐使用！(TODO: 拟定并提供示例)

### 避免使用 `try!`

通常推荐：

```Swift
do {
    try somethingThatMightThrow()
}
catch {
    fatalError("Something bad happened.")
}
```

不推荐：

```Swift
try! somethingThatMightThrow()
```

虽然这种形式显得有点啰嗦，但它为其他的开发者进行代码评审提供了清晰的上下文语境。

在没有更好的错误处理策略进化出来之前，使用 `try!` 作为一种临时的错误处理也是 OK 的。但是建议你定期审查你的代码是否错误的使用了 `try!`，因为它很可能上次悄悄地躲过了代码审查。


### 尽量避免 `try?`

`try?`是用来屏蔽(译者注：产生了错误但你不想做任何错误处理相关的事情)错误的，只有在你真的不关心错误的产生时，对你而言才有用。通常情况下你应该捕获错误并至少打印错误日志。


### 尽早 return 或者 break

当你遇到某些操作需要通过条件判断去执行，应当尽早地退出判断条件：你不应该用下面这种写法

```Swift
if n.isNumber {
	// Use n here
} else {
	return
}
```

用这个：

```Swift
guard n.isNumber else {
	return
}
// Use n here
```

或者你也可以用 if 声明，但是我们推荐你使用 guard
理由： 你一但声明 guard 编译器会强制要求你和 return, break 或者 continue 一起搭配使用，否则会产生一个编译时的错误。

如果可能的话，使用 `guard` 申明来处理提前返回或退出 (例如： 致命错误(fatal errors) 或 抛出错误 (thrown errors))。

### "限制性地" 控制访问

权限从窄到宽: `private` --> `fileprivate ` --> `internal` --> `public`，只有在有需要的时候，才考虑提高访问权限。

### 格式

- 用 tab，而非空格
- 文件结束时留一空行
- 用足够的空行把代码分割成合理的块
- 不要在一行结尾留下空白，也不要在空行留下缩进

### 对于只读属性和 subscript，选用隐式的 getters 方法

如果可以，省略只读属性和 subscript 的 get 关键字  
所以应该这样写：

```Swift
var myGreatProperty: Int {
	return 4
}

subscript(index: Int) -> T {
	return objects[index]
}
```

而不是：

```Swift
var myGreatProperty: Int {
	get {
		return 4
	}
}

subscript(index: Int) -> T {
	get {
		return objects[index]
	}
}
```

### 需要时才写上 self

必要的时候再加上 self, 比如在（逃逸）闭包里，或者 参数名冲突了

### 默认 class 为 final

class 应该用 final 修饰，并且只有在继承的有效需求已被确定时候才能去使用子类。即便在这种情况（前面提到的使用继承的情况）下，根据同样的规则（class 应该用 final 修饰的规则），类中的定义（属性和方法等）也要尽可能的用 final 来修饰
理由： 组合通常比继承更合适，选择使用继承则很可能意味着在做出决定时需要更多的思考。

### 首选 struct 而非 class

除非你需要 class 才能提供的功能（比如 identity 或 deinitializers），不然就用 struct
要注意到继承通常 不 是用 类 的好理由，因为 多态 可以通过 协议 实现，重用 可以通过 组合 实现。  
比如，这个类的分级  

```Swift
class Vehicle {
	let numberOfWheels: Int

	init(numberOfWheels: Int) {
		self.numberOfWheels = numberOfWheels
	}

	func maximumTotalTirePressure(pressurePerWheel: Float) -> Float {
		return pressurePerWheel * Float(numberOfWheels)
	}
}

class Bicycle: Vehicle {
	init() {
		super.init(numberOfWheels: 2)
	}
}

class Car: Vehicle {
	init() {
		super.init(numberOfWheels: 4)
	}
}
```

可以重构成酱紫：  

```Swift
protocol Vehicle {
	var numberOfWheels: Int { get }
}

func maximumTotalTirePressure(vehicle: Vehicle, pressurePerWheel: Float) -> Float {
	return pressurePerWheel * Float(vehicle.numberOfWheels)
}

struct Bicycle: Vehicle {
	let numberOfWheels = 2
}

struct Car: Vehicle {
	let numberOfWheels = 4
}
```
理由： 值类型更简单，容易分析，并且 let 关键字的行为符合预期。
