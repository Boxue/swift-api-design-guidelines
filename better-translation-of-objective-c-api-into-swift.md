# 更好的把Objective-C APIs转换成Swift版本

## 提交review前必读

做为下面三份文档的一部分，它们的内容是彼此关联的：

* [SE-0023 API设计指南](https://github.com/apple/swift-evolution/blob/master/proposals/0023-api-guidelines.md)
* [SE-0006 在标准库中应用设计指南](https://github.com/apple/swift-evolution/blob/master/proposals/0006-apply-api-guidelines-to-the-standard-library.md)
* [SE-0005 更好的把Objective-C APIs转换成Swift版本](https://github.com/apple/swift-evolution/blob/master/proposals/0005-objective-c-name-translation.md)

这三份文档的内容是相互关联的（例如：标准库中一个API的调整和某个API guideline是对应的，或根据某条设计指南制定的Clang importer规则，等等）。正因为存在这些内容交叉，为了保证讨论是可维护的，我们希望你：

* **在提交review之前，对以上三份文档中的全部内容，有一个基本的了解**；
* **在提交以上三个文档的review时，请参照每个文档的review声明**。在你提交review时，如果文档间交叉引用有助于帮你阐述观点，你应该包含它们（这也是被提倡的做法）。

## 简介

这份提议描述了我们如何改进Swift的“Clang Importer”，它有两个功能：首先，把C和Objective-C的APIs映射成Swift版本；其次，翻译Objective-C中的函数、类型、方法和属性等的名字，让它们满足[API设计指南](https://github.com/apple/swift-evolution/blob/master/proposals/0023-api-guidelines.md)中的要求，这些要求，是我们在设计Swift 3时，建立的原则。

我们的方法专注于Objective-C版本的[Cocoa编码指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)和Swift版本的[API设计指南](https://github.com/apple/swift-evolution/blob/master/proposals/0023-api-guidelines.md)之间的差异。使用一些简单的语言学分析方法，协助我们把Objective-C中的名字自动转换成更加Swift“原汁原味”的名字。

转换的结果，可以在[Swift 3 API Guidelines Review](https://github.com/apple/swift-3-api-guidelines-review)这个repository中查看。这个repository中包含了用[Swift 2](https://github.com/apple/swift-3-api-guidelines-review/tree/swift-2)和[Swift 3](https://github.com/apple/swift-3-api-guidelines-review/tree/swift-3)编写的Objective-C APIs项目，以及一些已经迁移到Swift 3版本的示例代码。你也可以通过[对比这两个分支](https://github.com/apple/swift-3-api-guidelines-review/compare/swift-2...swift-3)来查看所有的改动。

## 动机

Objective-C版本的[Cocoa编码指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)为使用Objective-C创建简单、一致的API提供了完整的框架。但是Swift是一门不同的编程语言，特别是，它是一门支持类型推导、泛型编程和重载等语言特性的强类型语言。于是，基于Objective-C编写的APIs搭配上Swift就有点儿水土不服，这些API在Swift里用起来显得很啰嗦。例如：

```swift
let content = listItemView.text.stringByTrimmingCharactersInSet(
    NSCharacterSet.whitespaceAndNewlineCharacterSet())
```

这明显是一个Objective-C风格的函数调用。如果我们用Swift编写，结果看上去应该是这样的：

```swift
let content = listItemView.text.trimming(.whitespaceAndNewlines)
```

这显然是更遵循[Swift API设计指南](https://github.com/apple/swift-evolution/blob/master/proposals/0023-api-guidelines.md)中的用法，特别是，我们忽略掉了那些编译器已经能强制约束我们使用的类型名称（例如：view，string, character set等）。这份提议的目的，就是让从Objective-C引入API更加“Swift原汁原味”，让Swift开发者在使用Objective API时，有和使用Swift“原生代码”更为一致的开发体验。

这份提议中的解决方案对Objective-C框架（例如：Cocoa和Cocoa Touch）和任何可以在“Swift混合项目”中使用的Objective-C API是相同的。要说明的是，[Swift核心库](https://swift.org/core-libraries/)重新实现了Objective-C框架中的API，所以，对这些API（名称的）的改动都会在Swift 3核心库的实现中体现出来。

## 提议的解决方案

提议的解决方案引入了一种定义Objective-C [Cocoa编码指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)和[Swift API设计指南](https://github.com/apple/swift-evolution/blob/master/proposals/0023-api-guidelines.md)区别的方式，这种方式可以帮助我们通过设置一些列规则，参照Cocoa编码指南以及Objective-C中既定的习俗，把前者变换成后者。这是对用Clang importer进行名称翻译的一种启发式扩展。例如：把Objective-C中的全局`enum`常量变成Swift中的cases（这要去掉Objective-C为全局enum常量名设置的前缀）以及把Objective-C中的工厂方法（例如：`+[NSNumber numberWithBool:]`）映射成Swift中的初始化方法（`NSNumber(bool: true)`）。

这份提议中描述的启发式方法需要通过覆盖大量的Objective-C API进行迭代、调校和试验，以确保它最终可以正常工作。但是，它仍旧是不可能完美工作的，一定会有一些API，经过“翻译”之后，会导致其不如原来表意清晰。因此，我们的目标是确保绝大多数的Objective C API都可以在翻译之后更加的Swift原汁原味。并且允许Objective-C API的作者对于那些不满意的翻译，在Objective-C头文件中，通过API注释说明问题。

这份提议的解决方案对Clang importer引入了以下这些改变：

1. **泛化`swift_name`属性的应用范围**：Clang的`swift_name`现在只能用于重命名`enum`的cases以及工厂方法。当它被引入到Swift后，它应该被泛化成允许重命名任意的C或Objective-C的语言元素，以方便C或Objective-C API的作者更好的调校重命名的过程。
2. **去掉冗余的类型名称**：Objective-C Cocoa编码指南要求方法声明中要带有每一个参数的描述。当这个描述重申了参数的类型时，方法的名字就违背了Swift编码指南中关于“忽略不需要的字符”的设计要求。因此，执行翻译时，我们应该去掉那些描述类型的部分。
3. **添加默认参数**：如果Objective-C API的声明强烈暗示参数需要默认参数，应该为这样的API在引入Swift时，添加默认参数。例如，一个表示选项集合的参数，可以被设置成\[\]。
4. **为第一个参数添加label**：如果方法的第一个参数有默认值，[应该为这个参数设置一个参数label](https://swift.org/documentation/api-design-guidelines#first-argument-label)。
5. **给Bool语义的属性添加“is”前缀**：[Bool属性应该在读取的时候，表达断言的语义](https://swift.org/documentation/api-design-guidelines#boolean-assertions)，但是Objective-C Cocoa编码指南中，[禁止在属性名中使用单词“is”](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE)。因此，当引入这样的属性时，为它们添加“is”前缀。
6. **表达值语义的名字，首字母小写**：在Swift API设计指南中，要求对“非类型声明（non-type declarations）”使用小写字母。包括，`enum`中的`case`以及属性或函数的声明。因此，引入Objective-C中没有前缀的值时，让这些名字的首字母小写（例如：一个叫做`URLHandler`的属性应该变成`urlHandler`）。
7. **让实现`compare(_:) -> NSComparisonResult`的类遵从Comparable protocol**：在Objective-C中，类对象的比较结果，都是通过“排序”的方式判断的（注：例如`NSOrderedDescending`和`NSOrderedAscending`）。导入过程中，通过让这些类遵从`Comparable` protocol，可以让比较操作的实现更正规（注：通过`Comparable`提供的运算符方法）。

为了感受下这些转换规则带来的实际效果，看下`UIBezierPath`从Swift 2：

```swift
class UIBezierPath : NSObject, NSCopying, NSCoding {
    convenience init(ovalInRect: CGRect)
    func moveToPoint(_: CGPoint)
    func addLineToPoint(_: CGPoint)
    func addCurveToPoint(_: CGPoint, controlPoint1: CGPoint, controlPoint2: CGPoint)
    func addQuadCurveToPoint(_: CGPoint, controlPoint: CGPoint)
    func appendPath(_: UIBezierPath)
    func bezierPathByReversingPath() -> UIBezierPath
    func applyTransform(_: CGAffineTransform)
    var empty: Bool { get }
    func containsPoint(_: CGPoint) -> Bool
    func fillWithBlendMode(_: CGBlendMode, alpha: CGFloat)
    func strokeWithBlendMode(_: CGBlendMode, alpha: CGFloat)
    func copyWithZone(_: NSZone) -> AnyObject
    func encodeWithCoder(_: NSCoder)
}
```

移植到Swift 3后的变化：

```swift
class UIBezierPath : NSObject, NSCopying, NSCoding {
    convenience init(ovalIn rect: CGRect)
    func move(to point: CGPoint)
    func addLine(to point: CGPoint)
    func addCurve(to endPoint: CGPoint, controlPoint1 controlPoint1: CGPoint, controlPoint2 controlPoint2: CGPoint)
    func addQuadCurve(to endPoint: CGPoint, controlPoint controlPoint: CGPoint)
    func append(_ bezierPath: UIBezierPath)
    func reversing() -> UIBezierPath
    func apply(_ transform: CGAffineTransform)
    var isEmpty: Bool { get }
    func contains(_ point: CGPoint) -> Bool
    func fill(_ blendMode: CGBlendMode, alpha alpha: CGFloat)
    func stroke(_ blendMode: CGBlendMode, alpha alpha: CGFloat)
    func copy(with zone: NSZone = nil) -> AnyObject
    func encode(with aCoder: NSCoder)
}
```

可以看到，在Swift 3版本里，原来API里很多描述类型信息的部分都被去掉了。转换后的结果，更接近Swift API设计指南中的要求。现在，Swift开发者可以通过类似`foo.copy()`这样的方式，拷贝任何遵从`NSCopying`的对象，而不用再像原来`foo.copyWithZone(nil)`这样的方式。

## 设计细节

这部分描述了上述改变规则中第2-5条的试验性实现细节。真正的实现在Swift代码树中，主要是[lib/Basic/StringExtras.cpp](https://github.com/apple/swift/blob/master/lib/Basic/StringExtras.cpp)中的`omitNeedlessWords`函数。

接下来的描述，和参与名称翻译的Objective-C API紧密相关。例如：`startWithQueue:compeltionHandler:`是个有两个selector片段的方法，`startWithQueue`和`completionHandler`。这个selector翻译成Swift版本应该是`startWithQueue(_:completionHandler:)`。

### 去掉冗余的名称

Objective-C API中经常会包含参数或返回值的类型名称，它们应该在Swift API中统统被去掉。下面的这些规则，用于识别并且去掉这些表示类型名称的单词。\[[详见API设计原则之忽略不需要的单词]\]。

#### 定义类型名称

匹配的过程是在老版本Swift API的selector片段里，搜索特定的**类型名称**后缀，这些类型名称定义如下：

* 对于绝大部分Objective-C类型来说，类型名称就是忽略`nullable`之后，Swift引入的名称，例如：

Objective-C type  | Type name
----------------- | -------------
float             | Float
nullable NSString | String
UIDocument        | UIDocument
nullable UIDocument | UIDocument
NSInteger         | NSInteger
UIUInteger        | NSUInteger
CGFloat           | CGFloat

* 当Objective-C类型是一个block时，Swift中的类型名称是`Block`；
* 当Objective-C的类型是一个函数指针或引用时，Swift中的类型名称是`Function`；
* 当Objective-C的类型是一个除`NSInteger`、`NSUInteger`或`CGFloat`之外的typedef时，Swift中的类型名称则是这些typedef实际代表的类型的名称。例如，Objective-C中的类型是`UILayoutPriority`，实际上它是一个`float`类型的typedef，我们将（在老版本的Swift API里）尝试匹配字符串`Float`。\[详见API设计原则之为弱类型提供信息补偿\]。

#### 匹配

为了在selector片段中删除冗余的类型信息，我们需要在selector中匹配包含上述类型信息的字符串。

全部匹配都是由以下两个基本规则来管理的：

* **在单词开始和结束的边界进行匹配**：无论是selector片段内部，还是类型名称，单词边界位置都是一个字符串的开始或结束，以及每一个大写字母前面。把每一个大写字母作为单词边界可以让我们匹配到“大写字母缩略词”，而无需单独维护一个特殊的缩略词或前缀列表；

例如，下面的`URL`是匹配的：

```swift
func documentForURL(_: NSURL) -> NSDocument?
```

但是，下面的View，由于在单词中间，它是不能被匹配的。

```swift
var thumbnailPreview : UIView  // not matched
```

* **匹配的字符扩展到类型名称的结尾**：因为我们支持匹配一个类型名称的后缀，因此：

```swift
func constraintEqualToAnchor(anchor: NSLayoutAnchor) -> NSLayoutConstraint?
```

可以被简化成：

```swift
func constraintEqualTo(anchor: NSLayoutAnchor) -> NSLayoutConstraint?
```

基于以上两个原则，我们可以执行以下一系列的匹配过程：

* **基本匹配**：

    * selector片段中的字串匹配类型名称的字串，例如：`appendString`中的`String`匹配`NSString`中的`String`：`func appendString(_:  NSString)`；
    * Selector片段中的`Index`匹配类型名称中的`Int`，例如：`func characterAtIndex(_: Int) -> unichar`；
    
* **集合相关匹配（Collection matches）**：
    * Selector片段中的`Indexes`或`Indices`匹配类型名称中的`IndexSet`，例如：`func removeObjectsAtIndexes(_: NSIndexSet)`；
    * 如果selector片段中的复数名词的单数形式匹配集合中元素的类型，那么selector中的复数名词匹配集合类型名称，例如：`func arrangeObjects(_: [AnyObject]) -> [AnyObject]`

**特殊后缀匹配**：

#### 删除类型名称时的限制


#### 删除步骤


#### 添加默认参数


#### 为第一个参数添加label


#### 为Bool语义的属性添加“is”前缀


### 实现比较方法时遵从的准则


### 对已有代码的修改


### 声明