#Worktile iOS

##概述

Worktile 的 iOS 主工程

##Build
- iOS的主工程为LessChat-iOS
- 工程通过配置文件：`/Config/XXX.xcconfig` 进行配置

##工程结构（依赖关系）
- 主工程以多个target形式管理依赖的模块
- LessChat-iOS（主工程）依赖：LessChatKit、lesschatcore、WorktileCore、应用模块
- LessChatKit依赖：lesschatcore、第三方库（多为UI）
- lesschatcore依赖：OC\C\C++的第三方库
- WorktileCore依赖：lesschatcore、第三方库
- 应用模块：LessChatKit、[WorktileCore]、[lesschatcore]

##Modules
> 模块化开发的产物，作为一个完整的分离模块，一般是一个完整的功能，目前以target的形式存在主工程中

###构建过程
1.创建Cocoa Touch Framework工程
2.修改Development target不能高于主工程
3.工程结构可自行设计
4.保持umbrella文件(`$FramewotkName$.h`)可访问性为public
5.由于Swift的Framework工程不允许`Bridging-Header.h`文件，所以用到的Object-C头文件的写在umbrella文件中
6.类和变量的访问性(`public/internal/private`)要求注清楚

###LessChatSandbox
>该工程脱离于主工程，用户单独开发modules

- 将创建的Module项目直接拖到该工程下
- 该工程还直接引用了LessChatKit、[lesschatcore](https://github.com/atinc/lcios)和WorktileCore工程，便于调试和模块化开发
- 包括了基本的登录流程

##编码规范 (CODE STYLE)
[YCTech Code Style](https://github.com/atinc/lesschatcodestyle)
[Worktile Swift Code Style](https://github.com/atinc/lcios/blob/dev/Swiftt%20代码规范.md)


##LessChatKit
>项目中UI中公用的部分，作为一个Framework被主工程及Submodules引用

- 引用方式：`#import <LessChatKit/LessChatKit.h>`
- 包括公用的ViewController、View(自定义View及Cell等)、Commons、Utils、公用的第三方库等
- 由于一些特殊原因，暂时在LessChatKit引用了LessChatCore.framework <https://github.com/atinc/lcios>

##lesschatcore
核心库，包括网络请求、数据解析及数据模型、缓存、im等，详情请查看 [lesschatcore README](https://github.com/atinc/lccore/blob/master/README.md)

##WorktileCore
Swift 版本的核心库，用来替代 lesschatcore [WorktileCore README](/WorktileCore/README.md)

##结构设计
>MVC模式设计

- Classes : ViewControllers
- View : Storyboards
- Resources : 国际化文件以及一些资源文件
- Vendor : 第三方库
- LessChatKit : 项目中公共的UI库
- lesschatcore : 核心库
- WorktileCore : swift 版本核心库
- Modules : 功能模块

##iOS工程UI层编码规范（基于Swift）
> 以下定义之外遵循 [GitHub's Swift Style Guide](https://github.com/github/swift-style-guide) 规范，并且接受[SwiftLint](https://github.com/realm/SwiftLint)的检查
> 以下默认是必须遵循

1. `UIViewController`继承`LCKViewController`
1. `UINavigationController`继承`LCKNavigationController`
1. `UIViewTableView`使用`LCKTableView`，数据加载时需要对应设置`LCKTableView.status`
1. 每个类要注释说明，按照以下格式：

```
/// 此类的功能说明
///
/// 参数: 必要的参数
///
/// @since 当前版本号
/// @author 作者
/// @update by 更新作者 更新内容
```
类内部的注释：

```
// MARK: - Public
// MARK: - Commons
// MARK: - Property
// MARK: - Lifecycle
// MARK: - Privete
每个分组的注释之间空两行
```

示例 Controller 格式：

```
/// 这是***
///
/// @since 6.0
/// @author yuanjilee
enum EnumName {
  // 枚举声明的作用域：按照使用范围决定写在类内部还是外部
}

/// 这是***
///
/// 参数: <#参数#>
///
/// @since 6.0
/// @author yuanjilee
class ClassName {

  // MARK: - Public Property
  
  // MARK: - Private Property
     // 私有变量，包括静态变量
  
  // MARK: - Lifecycle
  
}

extension ClassName {
  
  // MARK: - Public (公开方法)

}

extension ClassName {
  
  // MARK: - Protocols
  
  
  // MARK: - Protocols
}


extension ClassName {
  
  // MARK: - Private
    // 包括 _setupAppearance() _setupDataSource()
  
}

extension ClassName {
  
  // MARK: - Event
  
}


```
方法名描述不清楚的方法要注释（尽量实现自文档化），比较晦涩的代码要注释
不要用`魔法数字`，如：比如说 ```if section == 0 { return 3 }```  0跟3是表述不明，用到的地方要注释，或者声明为常量，格式：private let kXXXXX = XXX

1. 私有方法用 private声明，并且以_(下划线)开头
1. ViewController 建议实现 `_setupAppearance()`、`_setupDataSource()`两个私有方法，分别处理UI和数据源
	1. 写一个功能的时候，首先去 LessChatKit 中看一下这个功能是不是已经封装过，还要看一下`Vendor`中的第三方库，是否提供了这个功能；若两个模块中用到的相同的功能，要提取出来，放到 LessChatKit 中
1. 相同的代码不要写两遍，封装
1. assets中的资源文件(图片)，拖入之前要重命名为英文，规范是 `icon_<模块名>_<位置>_<名称>[_<其它描述>]`，例：icon_task_list_more
1. 严格遵循MVC的架构，View中的事件要用过Block或者delegate传递出来，不要在View中做业务处理（比如网络请求）
1. 页面之间传值一般要传ID，通过Fetch方法获取到ID对应的对象，以防止操作指针产生的一些错误
1. UITableview 实现了自动Cell高度，使用时注意。
1. 由于是Universal的工程，要考虑到横竖屏切换的情况，所以，要求所有的View都通过AutoLayout约束，除非你有更好的方法；StoryBoard设置约束的时候，SizeClass要设置为wAny&hAny
1. 项目实现了国际化，而且实现了应用内切换语言，使用`LCKLocalizedString()`实现字符串的国际化，同时注意日期格式及系统自动设置的文字（如Done、Cancel、Back等，不要用系统的）
1. 使用debugPrint() 而不是 print()
1. 目前项目中编码规范有不统一的地方，新写的代码遵循新的编码规范。此编码规范会不断更新与改进，遵循的同时也要灵活运用

##第三方库(FRAMEWORK)

iOS:

<1> MBProgressHUD (https://github.com/jdg/MBProgressHUD)
Copyright (c) 2013 Matej Bukovinski
Licence: MIT

<2> SDWebImage (https://github.com/rs/SDWebImage)
Copyright (c) 2009 Olivier Poitrey
Licence: MIT

<3> Nimbus (https://github.com/jverkoey/nimbus)
Copyright (c) Jeff Verkoeyen 2011-2014
Licence: Apache 2.0 (http://www.apache.org/licenses/LICENSE-2.0)

<4> SZTextView (https://github.com/glaszig/SZTextView)
Copyright (c) 2013 glaszig <glaszig@gmail.com>
Licence: MIT

<5> google-code-prettify (https://code.google.com/p/google-code-prettify)
Copyright (c) Google Inc.
Licence: Apache License 2.0

<6> FXBlurView (https://github.com/nicklockwood/FXBlurView)
Copyright (C) 2013 Charcoal Design
Licence description:
This software is provided 'as-is', without any express or implied warranty. In no event will the authors be held liable for any damages arising from the use of this software.

Permission is granted to anyone to use this software for any purpose, including commercial applications, and to alter it and redistribute it freely, subject to the following restrictions:

The origin of this software must not be misrepresented; you must not claim that you wrote the original software. If you use this software in a product, an acknowledgment in the product documentation would be appreciated but is not required.
Altered source versions must be plainly marked as such, and must not be misrepresented as being the original software.
This notice may not be removed or altered from any source distribution.

<7> ICETutorial (https://github.com/icepat/ICETutorial)
Copyright (c) 2013 Patrick Trillsam
Licence: MIT

<8> THChatInput (https://github.com/skela/THChatInput)
Copyright (c) 2014 Marat
Licence: MIT

<9> SWTableViewCell (https://github.com/CEWendel/SWTableViewCell)
Copyright (c) 2013 Chris Wendel
Licence: MIT

<10> YLProgressBar (https://github.com/yannickl/YLProgressBar)
Copyright 2012 - present, Yannick Loriot.

<11> MJRefresh (https://github.com/CoderMJLee/MJRefresh)

<12> FontAwesome (https://github.com/PrideChung/FontAwesomeKit)
Licence: MIT

<13> SnapKit (https://github.com/SnapKit/SnapKit) - Swift版的AutoLayout库
Copyright (c) 2011-2015 SnapKit Team
Licence: MIT

<14> IQKeyboardManager (https://github.com/hackiftekhar/IQKeyboardManager)
Licence: MIT

<15> MMMarkdown (https://github.com/mdiep/MMMarkdown)
An Objective-C framework for converting Markdown to HTML.
Licence: MIT

<16> DZNEmptyDataSet (https://github.com/dzenbot/DZNEmptyDataSet)
Licence: MIT

<17> CVCalendar (https://github.com/Mozharovsky/CVCalendar)
A custom visual calendar for iOS 8+ written in Swift (2.0).
Licence: MIT

<18> CTAssetsPickerController (https://github.com/chiunam/CTAssetsPickerController)
iOS control that allows picking multiple photos and videos from user's photo library.
Licence: MIT


##License
See `LICENSE` for details.
##FAQ

