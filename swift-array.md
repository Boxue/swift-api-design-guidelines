# `Array`的桥接规则

当`Array`中的元素是任何class类型或`@objc`修饰的类型时，`Array`都会被桥接到Obejctive-C处理，并通过一致性转换，桥接回Swift，这叫做bridged verbatim；

一个非bridge verbatim的类型`T`可以通过遵从`BridgedToObjectiveC` protocol来定义当它在Swift到Objective-C之间桥接的过程：

```swift
protocol _BridgedToObjectiveC {
    typealias _ObjectiveCType: AnyObject
	func _bridgeToObjectiveC() -> _ObjectiveCType
	class func _forceBridgeFromObjectiveC(_: _ObjectiveCType) -> Self
}
```

泛型数组只有当其元素需要桥接到Objective-C时，数组类型才桥接到Objective-C处理，这些数组的元素类型遵从`_ConditionallyBridgedToObjectiveC` protocol：

```swift
protocol _ConditionallyBridgedToObjectiveC : _BridgedToObjectiveC {
	class func _isBridgedToObjectiveC() -> Bool
	class func _conditionallyBridgeFromObjectiveC(_: _ObjectiveCType) -> Self?
}
```

无论是桥接到Objective-C，还是桥接回Swift，让一个`T._isBridgedToObjectiveC()`等于`false`的类型遵从`_ConditionallyBridgedToObjectiveC`是一个编码错误。这个错误通常在运行时才能被分析。`_conditionallyBridgeFromObjectiveC`可以用来尝试从Objective-C桥接回Swift，并且，当桥接失败时，返回`nil`。

# `Array`的类型转换规则

在下面的各种转换规则中：`Base`是`AnyObject`或其派生类；`Derived`是`Base`的派生类，X是一个遵从`_BridgedToObjectiveC`的类型。

* **Trival bridging**（注：从Swift -> Objective-C）：把`[Base]`隐式转换为`NSArray`的算法复杂度是O(1)。只要直接把Array的内部缓存返回就可以了，它的类型就是一个`NSArray`。
* **Trival bridging back**（注：从Objective-C -> Swift）：把`NSArray`隐式转换成`[AnyObject]`的算法复杂度是O(1)，再加上一个对`NSArray`调用`copy()`的开销；
* **在不同`Array`类型之间的隐式转换**：
	* `[Derived]`转换成`[Base]`的算法复杂度是O(1)；
	* `[X]`转换成`[X._ObjectiveType]`的算法复杂度是O(N)；

> 以上两种隐式类型转换都可能发生trival bridging。
	
* **Checked conversions** 通过 `as [U]`把`[T]`转换成`[U]?`的算法复杂度是O(N)：
	* **Checked downcasting**：把`[Base]`转换为`[Derived]?`；
	* **Checked bridging back**：把`[T]`转换为一个`[X]?`，其中`X._ObjectiveCType`是`T`，或者T的派生类；
* **Forced conversions**：当在Swift和Objective-C之间通过桥接交互时，强制把`[AnyObject]`或`NSArray`隐式转换成`[T]`
	*  **Forced downcasting**：把`[AnyObject]`转换成`[Derived]`的算法复杂度是O(1)；
	* **Forced bridging back**：把`[AnyObject]`转换成`[X]`的时间复杂度是O(N)；

例如：当用户通过Swift编写了一个接受`[NSView]`类型的方法时，这个方法暴露给Objective-C时，是一个接受`NSArray`为参数的方法。在Objective-C中被调用的时候，这个`NSArray`会被强制转换回`[View]`。

当数组中的任何元素发生forced conversion失败时，通常是发生了一个可以捕获的错误。对于forced downcasting来说，这个错误可能会被延后到发生错误的对象被访问的时候才能捕捉到。

> Checked和Forced conversion都可能伴随从`NSArray`的tribal bridging back.
