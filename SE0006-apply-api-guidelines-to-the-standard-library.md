# SE-0006 在标准库中应用API设计指南

## 第一部分 提交review前必读

做为下面三份文档的一部分，它们的内容是彼此关联的：

* [SE-0023 API设计指南](https://github.com/apple/swift-evolution/blob/master/proposals/0023-api-guidelines.md)
* [SE-0006 在标准库中应用设计指南](https://github.com/apple/swift-evolution/blob/master/proposals/0006-apply-api-guidelines-to-the-standard-library.md)
* [SE-0005 更好的把Objective-C APIs转换成Swift版本](https://github.com/apple/swift-evolution/blob/master/proposals/0005-objective-c-name-translation.md)

这三份文档的内容是相互关联的（例如：标准库中一个API的调整和某个API guideline是对应的，或根据某条设计指南制定的Clang importer规则，等等）。正因为存在这些内容交叉，为了保证讨论是可维护的，我们希望你：

* **在提交review之前，对以上三份文档中的全部内容，有一个基本的了解**；
* **在提交以上三个文档的review时，请参照每个文档的review声明**。在你提交review时，如果文档间交叉引用有助于帮你阐述观点，你应该包含它们（这也是被提倡的做法）。

## 第二部分 简介

[API设计指南]()作为Swift 3开发工作的一部分，让标准库做为这份指南的实现典范是很重要的。在各种类型的App中，标准库中的API都是最常使用的Swift API，并且，标准库也为其它的程序库提供了实现基础。

在这份提议中，我们回顾了整个标准库，并且让它的设计遵循API设计指南中的要求。

## 第三部分 提议的解决方案

实际的代码修改在[Swift repository](https://github.com/apple/swift/tree/swift-3-api-guidelines)的[swift-3-api-guidelines](https://github.com/apple/swift)分支。总体来说，这些变化可以归结于以下几个方面：

* 在protocol名称中，去掉了`Type`后缀。在一些特殊情况下，为了避免和一些基本类型名称混淆，我们需要添加`Protocol`后缀（尽管这些类型我们期望在Swift 3中被废除）；
* 在所有的API中，和`generator`相关的概念，都被重命名成了`iterator`；
* 删除了仅用于`CollectionOfOne`的索引类型`Bit`。我们推荐使用`Int`；
* 在“不安全的指针类型（unsafe pointer types）”中，泛型参数名从`Memory`改为了`Pointee`；
* 删除了“不安全的指针类型”的默认初始化方法，我们推荐使用`nil`；
* 删除了`struct PermutationGenerator`；
* 删除了`MutableSliceable` protocol，你可以使用`Collection where SubSequence : MutableCollection`；
* `sort()`重命名成了`sorted()`，`sortInPlace()`重命名成了`sort()`；
* `reverse()`重命名成了`reversed()`；
* `enumerate()`重命名成了`enumerated()`；
* 简化了`partition()` API；
* `SequenceType.minElement()`重命名为了`.min()`，`.maxElement()`重命名为了`.max()`；
* 删除了一些序列和集合配接器（Collection adapters）的初始化方法。我们建议你调用对应算法函数或方法；
* 把一些函数变成了类方法或反之；
* 把以null结尾的UTF-8数据变成`String`对象的工厂函数变成了`String`的一个初始化方法；

## 第四部分 API差异对比

这部分描述了Swift 2.2和这份提议中描述的API的差别。具体的实现，则在[swift-3-api-guidelines](https://github.com/apple/swift)分支。

对于那些会影响很多类型的重复修改，我们只列出其中一个。例如，`generate()`重命名为`makeIterator()`。我们只列出了在protocol中名称的差异，这样，因此，在对应的实现中，它们也将会被修改。如果一个类型名称被修改了，我们只列出了它们在修改前后声明的对比，因此，在这些被修改内容的应用场景中，也将使用新的名称。

* 在protocol名称中删除`Type`后缀：

```diff
-public protocol BooleanType { ... }
+public protocol Boolean { ... }

-public protocol SequenceType { ... }
+public protocol Sequence { ... }

-public protocol CollectionType : ... { ... }
+public protocol Collection : ... { ... }

-public protocol MutableCollectionType : ... { ... }
+public protocol MutableCollection : ... { ... }

-public protocol RangeReplaceableCollectionType : ... { ... }
+public protocol RangeReplaceableCollection : ... { ... }

-public protocol AnyCollectionType : ... { ... }
+public protocol AnyCollectionProtocol : ... { ... }

-public protocol IntegerType : ... { ... }
+public protocol Integer : ... { ... }

-public protocol SignedIntegerType : ... { ... }
+public protocol SignedInteger : ... { ... }

-public protocol UnsignedIntegerType : ... { ... }
+public protocol UnsignedInteger : ... { ... }

-public protocol FloatingPointType : ... { ... }
+public protocol FloatingPoint : ... { ... }

-public protocol ForwardIndexType { ... }
+public protocol ForwardIndex { ... }

-public protocol BidirectionalIndexType : ... { ... }
+public protocol BidirectionalIndex : ... { ... }

-public protocol RandomAccessIndexType : ... { ... }
+public protocol RandomAccessIndex : ... { ... }

-public protocol IntegerArithmeticType : ... { ... }
+public protocol IntegerArithmetic : ... { ... }

-public protocol SignedNumberType : ... { ... }
+public protocol SignedNumber : ... { ... }

-public protocol IntervalType : ... { ... }
+public protocol Interval : ... { ... }

-public protocol LazyCollectionType : ... { ... }
+public protocol LazyCollectionProtocol : ... { ... }

-public protocol LazySequenceType : ... { ... }
+public protocol LazySequenceProtocol : ... { ... }

-public protocol OptionSetType : ... { ... }
+public protocol OptionSet : ... { ... }

-public protocol OutputStreamType : ... { ... }
+public protocol OutputStream : ... { ... }

-public protocol BitwiseOperationsType { ... }
+public protocol BitwiseOperations { ... }

-public protocol ReverseIndexType : ... { ... }
+public protocol ReverseIndexProtocol : ... { ... }

-public protocol SetAlgebraType : ... { ... }
+public protocol SetAlgebra : ... { ... }

-public protocol UnicodeCodecType { ... }
+public protocol UnicodeCodec { ... }

-public protocol CVarArgType { ... }
+public protocol CVarArg { ... }

-public protocol MirrorPathType { ... }
+public protocol MirrorPath { ... }
```

* 在所有的API中，和`generator`相关的概念，都被重命名成了`iterator`：

```diff
-public protocol GeneratorType { ... }
+public protocol IteratorProtocol { ... }

 public protocol Collection : ... {
-  associatedtype Generator : GeneratorType = IndexingGenerator<Self>
+  associatedtype Iterator : IteratorProtocol = IndexingIterator<Self>

-  func generate() -> Generator
+  func makeIterator() -> Iterator
 }

-public struct IndexingGenerator<Elements : Indexable> : ... { ... }
+public struct IndexingIterator<Elements : Indexable> : ... { ... }

-public struct GeneratorOfOne<Element> : ... { ... }
+public struct IteratorOverOne<Element> : ... { ... }

-public struct EmptyGenerator<Element> : ... { ... }
+public struct EmptyIterator<Element> : ... { ... }

-public protocol ErrorType { ... }
+public protocol ErrorProtocol { ... }

-public struct AnyGenerator<Element> : ... { ... }
+public struct AnyIterator<Element> : ... { ... }

-public struct LazyFilterGenerator<Base : GeneratorType> : ... { ... }
+public struct LazyFilterIterator<Base : IteratorProtocol> : ... { ... }

-public struct FlattenGenerator<Base : ...> : ... { ... }
+public struct FlattenIterator<Base : ...> : ... { ... }

-public struct JoinGenerator<Base : ...> : ... { ... }
+public struct JoinedIterator<Base : ...> : ... { ... }

-public struct LazyMapGenerator<Base : ...> ... { ... }
+public struct LazyMapIterator<Base : ...> ... { ... }

-public struct RangeGenerator<Element : ForwardIndexType> : ... { ... }
+public struct RangeIterator<Element : ForwardIndex> : ... { ... }

-public struct GeneratorSequence<Base : GeneratorType> : ... { ... }
+public struct IteratorSequence<Base : IteratorProtocol> : ... { ... }

-public struct StrideToGenerator<Element : Strideable> : ... { ... }
+public struct StrideToIterator<Element : Strideable> : ... { ... }

-public struct StrideThroughGenerator<Element : Strideable> : ... { ... }
+public struct StrideThroughIterator<Element : Strideable> : ... { ... }

-public struct UnsafeBufferPointerGenerator<Element> : ... { ... }
+public struct UnsafeBufferPointerIterator<Element> : ... { ... }
```

* 删除了仅用于`CollectionOfOne`的索引类型`Bit`。我们推荐使用`Int`：

```diff
-public enum Bit : ... { ... }
```

* 删除了`struct PermutationGenerator`：

```diff
-public struct PermutationGenerator<
-  C : CollectionType, Indices : SequenceType
-  where C.Index == Indices.Generator.Element
-> : ... { ... }
```

* 删除了`MutableSliceable` protocol，你可以使用`Collection where SubSequence : MutableCollection`；

```diff
-public protocol MutableSliceable : CollectionType, MutableCollectionType {
-  subscript(_: Range<Index>) -> SubSequence { get set }
-}
```

* 在“不安全的指针类型（unsafe pointer types）”中，泛型参数名从`Memory`改为了`Pointee`；
* 删除了“不安全的指针类型”的默认初始化方法，我们推荐使用`nil`；

```diff
 // The same changes applied to `UnsafePointer`, `UnsafeMutablePointer` and
 // `AutoreleasingUnsafeMutablePointer`.
 public struct UnsafePointer<
-  Memory
+  Pointee
 > ... : {
-  public var memory: Memory { get set }
+  public var pointee: Pointee { get set }

   // Use `nil` instead.
-  public init()
 }

public struct OpaquePointer : ... {
   // Use `nil` instead.
-  public init()
}
```

* `sort()`重命名成了`sorted()`，`sortInPlace()`重命名成了`sort()`，并且，我们给Closure添加了参数label：

```diff
extension Sequence where Self.Iterator.Element : Comparable {
   @warn_unused_result
-  public func sort() -> [Generator.Element]
+  public func sorted() -> [Iterator.Element]
 }

 extension Sequence {
   @warn_unused_result
-  public func sort(
+  public func sorted(
-    @noescape                 isOrderedBefore: (Iterator.Element, Iterator.Element) -> Bool
+    @noescape isOrderedBefore isOrderedBefore: (Iterator.Element, Iterator.Element) -> Bool
   ) -> [Iterator.Element]
 }

 extension MutableCollection where Self.Iterator.Element : Comparable {
   @warn_unused_result(mutable_variant="sort")
-  public func sort() -> [Generator.Element]
+  public func sorted() -> [Iterator.Element]
 }

 extension MutableCollection {
   @warn_unused_result(mutable_variant="sort")
-  public func sort(
+  public func sorted(
-   @noescape                 isOrderedBefore: (Iterator.Element, Iterator.Element) -> Bool
+   @noescape isOrderedBefore isOrderedBefore: (Iterator.Element, Iterator.Element) -> Bool
   ) -> [Iterator.Element]
 }

 extension MutableCollection
   where
   Self.Index : RandomAccessIndex,
   Self.Iterator.Element : Comparable {

-  public mutating func sortInPlace()
+  public mutating func sort()

 }

 extension MutableCollection where Self.Index : RandomAccessIndex {
-  public mutating func sortInPlace(
+  public mutating func sort(
-    @noescape                 isOrderedBefore: (Iterator.Element, Iterator.Element) -> Bool
+    @noescape isOrderedBefore isOrderedBefore: (Iterator.Element, Iterator.Element) -> Bool
   )
 }
```

* `reverse()`重命名成了`reversed()`：

```swift
 extension SequenceType {
-  public func reverse() -> [Generator.Element]
+  public func reversed() -> [Iterator.Element]
 }

 extension CollectionType where Index : BidirectionalIndexType {
-  public func reverse() -> ReverseCollection<Self>
+  public func reversed() -> ReverseCollection<Self>
 }

 extension CollectionType where Index : RandomAccessIndexType {
-  public func reverse() -> ReverseRandomAccessCollection<Self>
+  public func reversed() -> ReverseRandomAccessCollection<Self>
 }

 extension LazyCollectionProtocol
   where Index : BidirectionalIndexType, Elements.Index : BidirectionalIndexType {

-  public func reverse()
+  public func reversed()
     -> LazyCollection<ReverseCollection<Elements>>
 }

 extension LazyCollectionProtocol
   where Index : RandomAccessIndexType, Elements.Index : RandomAccessIndexType {

-  public func reverse()
+  public func reversed()
     -> LazyCollection<ReverseRandomAccessCollection<Elements>>
 }
```

* `enumerate()`重命名成了`enumerated()`：

```swift
 extension Sequence {
-  public func enumerate() -> EnumerateSequence<Self>
+  public func enumerated() -> EnumeratedSequence<Self>
 }

-public struct EnumerateSequence<Base : SequenceType> : ... { ... }
+public struct EnumeratedSequence<Base : Sequence> : ... { ... }

-public struct EnumerateGenerator<Base : GeneratorType> : ... { ... }
+public struct EnumeratedIterator<Base : IteratorProtocol> : ... { ... }
```

* 简化了`partition()` API：

```diff
extension MutableCollection where Index : RandomAccessIndex {
   public mutating func partition(
-    range: Range<Index>,
-                              isOrderedBefore: (Iterator.Element, Iterator.Element) -> Bool
+    @noescape isOrderedBefore isOrderedBefore: (Iterator.Element, Iterator.Element) -> Bool
   ) -> Index
 }

 extension MutableCollection
   where Index : RandomAccessIndex, Iterator.Element : Comparable {

-  public mutating func partition(range: Range<Index>) -> Index
+  public mutating func partition() -> Index
}
```

* `SequenceType.minElement()`重命名为了`.min()`，`.maxElement()`重命名为了`.max()`，我们还给clousre参数添加了参数label：

```diff
 extension Sequence {
-  public func minElement(
+  public func min(
-    @noescape                 isOrderedBefore: (Iterator.Element, Iterator.Element) throws -> Bool
+    @noescape isOrderedBefore isOrderedBefore: (Iterator.Element, Iterator.Element) throws -> Bool
   ) rethrows -> Iterator.Element?

-  public func maxElement(
+  public func max(
-    @noescape                 isOrderedBefore: (Iterator.Element, Iterator.Element) throws -> Bool
+    @noescape isOrderedBefore isOrderedBefore: (Iterator.Element, Iterator.Element) throws -> Bool
   ) rethrows -> Iterator.Element?
 }
 
 extension Sequence where Iterator.Element : Comparable {
-  public func minElement() -> Iterator.Element?
+  public func min() -> Iterator.Element?

-  public func maxElement() -> Iterator.Element?
+  public func max() -> Iterator.Element?
 }

```

* 删除了一些序列和集合配接器（Collection adapters）的初始化方法。我们建议你调用对应算法函数或方法：

```diff
 extension Repeated {
-  public init(count: Int, repeatedValue: Element)
 }
+/// Return a collection containing `n` repetitions of `elementInstance`.
+public func repeatElement<T>(element: T, count n: Int) -> Repeated<T>

 public struct LazyMapSequence<Base : Sequence, Element> : ... {
   // Call `.lazy.map` on the sequence instead.
-  public init(_ base: Base, transform: (Base.Generator.Element) -> Element)
 }

 public struct LazyMapCollection<Base : Collection, Element> : ... {
   // Call `.lazy.map` on the collection instead.
-  public init(_ base: Base, transform: (Base.Generator.Element) -> Element)
 }

 public struct LazyFilterIterator<Base : IteratorProtocol> : ... {
   // Call `.lazy.filter` on the sequence instead.
-  public init(
-    _ base: Base,
-    whereElementsSatisfy predicate: (Base.Element) -> Bool
-  )
 }

 public struct RangeIterator<Element : ForwardIndex> : ... {
   // Use the 'generate()' method on the collection instead.
-  public init(_ bounds: Range<Element>)

   // Use the '..<' operator.
-  public init(start: Element, end: Element)
 }

 public struct ReverseCollection<Base : ...> : ... {
   // Use the 'reverse()' method on the collection.
-  public init(_ base: Base)
 }

 public struct ReverseRandomAccessCollection<Base : ...> : ... {
   // Use the 'reverse()' method on the collection.
-  public init(_ base: Base)
 }

 public struct Slice<Base : Indexable> : ... {
   // Use the slicing syntax.
-  public init(base: Base, bounds: Range<Index>)
 }

 public struct MutableSlice<Base : MutableIndexable> : ... {
   // Use the slicing syntax.
-  public init(base: Base, bounds: Range<Index>)
 }

 public struct EnumeratedIterator<Base : IteratorProtocol> : ... {
   // Use the 'enumerated()' method.
-  public init(_ base: Base)
 }

 public struct EnumeratedSequence<Base : IteratorProtocol> : ... {
   // Use the 'enumerated()' method.
-  public init(_ base: Base)
 }

 public struct IndexingIterator<Elements : Indexable> : ... {
   // Call 'iterator()' on the collection instead.
-  public init(_elements: Elements)
 }

 public struct HalfOpenInterval<Bound : Comparable> : ... {
   // Use the '..<' operator.
-  public init(_ start: Bound, _ end: Bound)
 }

 public struct ClosedInterval<Bound : Comparable> : ... {
   // Use the '...' operator.
-  public init(_ start: Bound, _ end: Bound)
 }
	•	Some functions were changed into properties and vice versa.
-public func unsafeUnwrap<T>(nonEmpty: T?) -> T
 extension Optional {
+  public var unsafelyUnwrapped: Wrapped { get }
 }

 public struct Mirror {
-  public func superclassMirror() -> Mirror?
+  public var superclassMirror: Mirror? { get }
 }

 public protocol CustomReflectable {
-  func customMirror() -> Mirror
+  var customMirror: Mirror { get }
 }

 public protocol Collection : ... {
-  public func underestimateCount() -> Int
+  public var underestimatedCount: Int { get }
 }

 public protocol CustomPlaygroundQuickLookable {
-  func customPlaygroundQuickLook() -> PlaygroundQuickLook
+  var customPlaygroundQuickLook: PlaygroundQuickLook { get }
 }

 extension String {
-  public var lowercaseString: String { get }
+  public func lowercased()

-  public var uppercaseString: String { get }
+  public func uppercased()
 }

 public enum UnicodeDecodingResult {
-  public func isEmptyInput() -> Bool {
+  public var isEmptyInput: Bool
 }
```

* 方法名和参数label名根据API设计指南中进行了修改（属于这个类目的一些修改已经在其他类目中指出了，因此就不在这里重复了）：

```diff
public protocol ForwardIndex {
-  func advancedBy(n: Distance) -> Self
+  func advanced(by n: Distance) -> Self

-  func advancedBy(n: Distance, limit: Self) -> Self
+  func advanced(by n: Distance, limit: Self) -> Self

-  func distanceTo(end: Self) -> Distance
+  func distance(to end: Self) -> Distance
 }

 public struct Set<Element : Hashable> : ... {
-  public mutating func removeAtIndex(index: Index) -> Element
+  public mutating func remove(at index: Index) -> Element
 }

 public struct Dictionary<Key : Hashable, Value> : ... {
-  public mutating func removeAtIndex(index: Index) -> Element
+  public mutating func remove(at index: Index) -> Element

-  public func indexForKey(key: Key) -> Index?
+  public func index(forKey key: Key) -> Index?

-  public mutating func removeValueForKey(key: Key) -> Value?
+  public mutating func removeValue(forKey key: Key) -> Value?
 }

 extension Sequence where Iterator.Element : Sequence {
   // joinWithSeparator(_:) => join(separator:)
-  public func joinWithSeparator<
+  public func joined<
     Separator : Sequence
     where
     Separator.Iterator.Element == Iterator.Element.Iterator.Element
-  >(separator: Separator) -> JoinSequence<Self>
+  >(separator separator: Separator) -> JoinedSequence<Self>
 }

 extension Sequence where Iterator.Element == String {
-  public func joinWithSeparator(separator: String) -> String
+  public func joined(separator separator: String) -> String
 }

 public class ManagedBuffer<Value, Element> : ... {
   public final class func create(
-    minimumCapacity: Int,
+    minimumCapacity minimumCapacity: Int,
     initialValue: (ManagedProtoBuffer<Value, Element>) -> Value
   ) -> ManagedBuffer<Value, Element>
 }

 public protocol Streamable {
-  func writeTo<Target : OutputStream>(inout target: Target)
+  func write<Target : OutputStream>(inout to target: Target)
 }

 public func dump<T, TargetStream : OutputStream>(
   value: T,
-  inout _ target: TargetStream,
+  inout to target: TargetStream,
   name: String? = nil,
   indent: Int = 0,
   maxDepth: Int = .max,
   maxItems: Int = .max
 ) -> T

 extension Sequence {
-  public func startsWith<
+  public func starts<
     PossiblePrefix : Sequence where PossiblePrefix.Iterator.Element == Iterator.Element
   >(
-    possiblePrefix: PossiblePrefix,
+    with possiblePrefix: PossiblePrefix,
     @noescape isEquivalent: (Iterator.Element, Iterator.Element) throws -> Bool
   ) rethrows -> Bool

-  public func startsWith<
+  public func starts<
     PossiblePrefix : Sequence where PossiblePrefix.Iterator.Element == Iterator.Element
   >(
-    possiblePrefix: PossiblePrefix
+    with possiblePrefix: PossiblePrefix
   ) -> Bool
 }

 extension CollectionType where Iterator.Element : Equatable {
-  public func indexOf(element: Iterator.Element) -> Index?
+  public func index(of element: Iterator.Element) -> Index?
 }

 extension CollectionType {
-  public func indexOf(predicate: (Iterator.Element) throws -> Bool) rethrows -> Index?
+  public func index(where predicate: (Iterator.Element) throws -> Bool) rethrows -> Index?
 }

 extension String.Index {
-  public func samePositionIn(utf8: String.UTF8View) -> String.UTF8View.Index
+  public func samePosition(in utf8: String.UTF8View) -> String.UTF8View.Index

-  public func samePositionIn(utf16: String.UTF16View) -> String.UTF16View.Index
+  public func samePosition(in utf16: String.UTF16View) -> String.UTF16View.Index

-  public func samePositionIn(unicodeScalars: String.UnicodeScalarView) -> String.UnicodeScalarView.Index
+  public func samePosition(in unicodeScalars: String.UnicodeScalarView) -> String.UnicodeScalarView.Index
 }

 extension String.UTF16View.Index {
-  public func samePositionIn(characters: String) -> String.Index
+  public func samePosition(in characters: String) -> String.Index

-  public func samePositionIn(utf8: String.UTF8View) -> String.UTF8View.Index
+  public func samePosition(in utf8: String.UTF8View) -> String.UTF8View.Index

-  public func samePositionIn(unicodeScalars: String.UnicodeScalarView) -> String.UnicodeScalarView.Index
+  public func samePosition(in unicodeScalars: String.UnicodeScalarView) -> String.UnicodeScalarView.Index
 }

 extension String.UTF8View.Index {
-  public func samePositionIn(characters: String) -> String.Index
+  public func samePosition(in characters: String) -> String.Index

-  public func samePositionIn(utf16: String.UTF16View) -> String.UTF16View.Index
+  public func samePosition(in utf16: String.UTF16View) -> String.UTF16View.Index

-  public func samePositionIn(unicodeScalars: String.UnicodeScalarView) -> String.UnicodeScalarView.Index
+  public func samePosition(in unicodeScalars: String.UnicodeScalarView) -> String.UnicodeScalarView.Index
 }

 extension String.UnicodeScalarView.Index {
-  public func samePositionIn(characters: String) -> String.Index
+  public func samePosition(in characters: String) -> String.Index

-  public func samePositionIn(utf16: String.UTF16View) -> String.UTF16View.Index
+  public func samePosition(in utf16: String.UTF16View) -> String.UTF16View.Index

-  public func samePositionIn(utf16: String.UTF16View) -> String.UTF16View.Index
+  public func samePosition(in utf16: String.UTF16View) -> String.UTF16View.Index
 }
```

* 小写所有的`enum case`和静态属性：

```diff
public struct Float {
-  public static var NaN: Float
+  public static var nan: Float
 }

 public struct Double {
-  public static var NaN: Double
+  public static var nan: Double

 public struct CGFloat {
-  public static var NaN: CGFloat
+  public static var nan: CGFloat
 }

 public protocol FloatingPoint : ... {
-  static var NaN: Self { get }
+  static var nan: Self { get }
 }

 public enum FloatingPointClassification {
-  case SignalingNaN
+  case signalingNaN

-  case QuietNaN
+  case quietNaN

-  case NegativeInfinity
+  case negativeInfinity

-  case NegativeNormal
+  case negativeNormal

-  case NegativeSubnormal
+  case negativeSubnormal

-  case NegativeZero
+  case negativeZero

-  case PositiveZero
+  case positiveZero

-  case PositiveSubnormal
+  case positiveSubnormal

-  case PositiveNormal
+  case positiveNormal

-  case PositiveInfinity
+  case positiveInfinity
 }

 public enum ImplicitlyUnwrappedOptional<Wrapped> : ... {
-  case None
+  case none

-  case Some(Wrapped)
+  case some(Wrapped)
 }

 public enum Optional<Wrapped> : ... {
-  case None
+  case none

-  case Some(Wrapped)
+  case some(Wrapped)
 }

 public struct Mirror {
   public enum AncestorRepresentation {
-    case Generated
+    case generated

-    case Customized(() -> Mirror)
+    case customized(() -> Mirror)

-    case Suppressed
+    case suppressed
   }

   public enum DisplayStyle {
-    case struct, class, enum, tuple, optional, collection
+    case `struct`, `class`, `enum`, tuple, optional, collection

-    case dictionary, `set`
+    case dictionary, `set`
   }
 }

 public enum PlaygroundQuickLook {
-  case Text(String)
+  case text(String)

-  case Int(Int64)
+  case int(Int64)

-  case UInt(UInt64)
+  case uInt(UInt64)

-  case Float(Float32)
+  case float(Float32)

-  case Double(Float64)
+  case double(Float64)

-  case Image(Any)
+  case image(Any)

-  case Sound(Any)
+  case sound(Any)

-  case Color(Any)
+  case color(Any)

-  case BezierPath(Any)
+  case bezierPath(Any)

-  case AttributedString(Any)
+  case attributedString(Any)

-  case Rectangle(Float64,Float64,Float64,Float64)
+  case rectangle(Float64,Float64,Float64,Float64)

-  case Point(Float64,Float64)
+  case point(Float64,Float64)

-  case Size(Float64,Float64)
+  case size(Float64,Float64)

-  case Logical(Bool)
+  case bool(Bool)

-  case Range(Int64, Int64)
+  case range(Int64, Int64)

-  case View(Any)
+  case view(Any)

-  case Sprite(Any)
+  case sprite(Any)

-  case URL(String)
+  case url(String)

-  case _Raw([UInt8], String)
+  case _raw([UInt8], String)
 }
```

* 把以null结尾的UTF-8数据变成`String`对象的工厂函数变成了`String`的一个初始化方法：

```swift
 extension String {
-  public static func fromCString(cs: UnsafePointer<CChar>) -> String?
+  public init?(validatingUTF8 cString: UnsafePointer<CChar>)

-  public static func fromCStringRepairingIllFormedUTF8(cs: UnsafePointer<CChar>) -> (String?, hadError: Bool)
+  public init(cString: UnsafePointer<CChar>)
+  public static func decodeCString<Encoding : UnicodeCodec>(
+    cString: UnsafePointer<Encoding.CodeUnit>,
+    as encoding: Encoding.Type,
+    repairingInvalidCodeUnits isReparing: Bool = true)
+      -> (result: String, repairsMade: Bool)?
 }
```

* 从`NSString`引入的方法按照新的设计指南重新进行了命名：

```swift
 extension String {
-  public static func localizedNameOfStringEncoding(
-    encoding: NSStringEncoding
-  ) -> String
+  public static func localizedName(
+    ofStringEncoding encoding: NSStringEncoding
+  ) -> String

-  public static func pathWithComponents(components: [String]) -> String
+  public static func path(withComponents components: [String]) -> String

-  public init?(UTF8String bytes: UnsafePointer<CChar>)
+  public init?(utf8String bytes: UnsafePointer<CChar>)

-  public func canBeConvertedToEncoding(encoding: NSStringEncoding) -> Bool
+  public func canBeConverted(toEncoding encoding: NSStringEncoding) -> Bool

-  public var capitalizedString: String
+  public var capitalized: String

-  public var localizedCapitalizedString: String
+  public var localizedCapitalized: String

-  public func capitalizedStringWithLocale(locale: NSLocale?) -> String
+  public func capitalized(with locale: NSLocale?) -> String

-  public func commonPrefixWithString(
-    aString: String, options: NSStringCompareOptions) -> String
+  public func commonPrefix(
+    with aString: String, options: NSStringCompareOptions = []) -> String

-  public func completePathIntoString(
-    outputName: UnsafeMutablePointer<String> = nil,
-    caseSensitive: Bool,
-    matchesIntoArray: UnsafeMutablePointer<[String]> = nil,
-    filterTypes: [String]? = nil
-  ) -> Int
+  public func completePath(
+    into outputName: UnsafeMutablePointer<String> = nil,
+    caseSensitive: Bool,
+    matchesInto matchesIntoArray: UnsafeMutablePointer<[String]> = nil,
+    filterTypes: [String]? = nil
+  ) -> Int

-  public func componentsSeparatedByCharactersInSet(
-    separator: NSCharacterSet
-  ) -> [String]
+  public func componentsSeparatedByCharacters(
+    in separator: NSCharacterSet
+  ) -> [String]

-  public func componentsSeparatedByString(separator: String) -> [String]
+  public func componentsSeparated(by separator: String) -> [String]

-  public func cStringUsingEncoding(encoding: NSStringEncoding) -> [CChar]?
+  public func cString(usingEncoding encoding: NSStringEncoding) -> [CChar]?

-  public func dataUsingEncoding(
-    encoding: NSStringEncoding,
-    allowLossyConversion: Bool = false
-  ) -> NSData?
+  public func data(
+    usingEncoding encoding: NSStringEncoding,
+    allowLossyConversion: Bool = false
+  ) -> NSData?

-  public func enumerateLinguisticTagsInRange(
-    range: Range<Index>,
-    scheme tagScheme: String,
-    options opts: NSLinguisticTaggerOptions,
-    orthography: NSOrthography?,
-    _ body:
-      (String, Range<Index>, Range<Index>, inout Bool) -> ()
-  )
+  public func enumerateLinguisticTags(
+    in range: Range<Index>,
+    scheme tagScheme: String,
+    options opts: NSLinguisticTaggerOptions = [],
+    orthography: NSOrthography? = nil,
+    _ body:
+      (String, Range<Index>, Range<Index>, inout Bool) -> ()
+  )

-  public func enumerateSubstringsInRange(
-    range: Range<Index>,
-    options opts:NSStringEnumerationOptions,
-    _ body: (
-      substring: String?, substringRange: Range<Index>,
-      enclosingRange: Range<Index>, inout Bool
-    ) -> ()
-  )
+  public func enumerateSubstrings(
+    in range: Range<Index>,
+    options opts:NSStringEnumerationOptions = [],
+    _ body: (
+      substring: String?, substringRange: Range<Index>,
+      enclosingRange: Range<Index>, inout Bool
+    ) -> ()
+  )

-  public func fileSystemRepresentation() -> [CChar]
+  public var fileSystemRepresentation: [CChar]

-  public func getBytes(
-    inout buffer: [UInt8],
-    maxLength maxBufferCount: Int,
-    usedLength usedBufferCount: UnsafeMutablePointer<Int>,
-    encoding: NSStringEncoding,
-    options: NSStringEncodingConversionOptions,
-    range: Range<Index>,
-    remainingRange leftover: UnsafeMutablePointer<Range<Index>>
-  ) -> Bool
+  public func getBytes(
+    inout buffer: [UInt8],
+    maxLength maxBufferCount: Int,
+    usedLength usedBufferCount: UnsafeMutablePointer<Int>,
+    encoding: NSStringEncoding,
+    options: NSStringEncodingConversionOptions = [],
+    range: Range<Index>,
+    remaining leftover: UnsafeMutablePointer<Range<Index>>
+  ) -> Bool

-  public func getLineStart(
-    start: UnsafeMutablePointer<Index>,
-    end: UnsafeMutablePointer<Index>,
-    contentsEnd: UnsafeMutablePointer<Index>,
-    forRange: Range<Index>
-  )
+  public func getLineStart(
+    start: UnsafeMutablePointer<Index>,
+    end: UnsafeMutablePointer<Index>,
+    contentsEnd: UnsafeMutablePointer<Index>,
+    for range: Range<Index>
+  )

-  public func getParagraphStart(
-    start: UnsafeMutablePointer<Index>,
-    end: UnsafeMutablePointer<Index>,
-    contentsEnd: UnsafeMutablePointer<Index>,
-    forRange: Range<Index>
-  )
+  public func getParagraphStart(
+    start: UnsafeMutablePointer<Index>,
+    end: UnsafeMutablePointer<Index>,
+    contentsEnd: UnsafeMutablePointer<Index>,
+    for range: Range<Index>
+  )

   public init(
-    contentsOfURL url: NSURL,
+    contentsOf url: NSURL,
     encoding enc: NSStringEncoding
   ) throws

   public init(
-    contentsOfURL url: NSURL,
+    contentsOf url: NSURL,
     usedEncoding enc: UnsafeMutablePointer<NSStringEncoding> = nil
   ) throws

   public init?(
-    CString: UnsafePointer<CChar>,
+    cString: UnsafePointer<CChar>,
     encoding enc: NSStringEncoding
   )

-  public init(format: String, _ arguments: CVarArgType...)
+  public init(format: String, _ arguments: CVarArg...)

-  public init(format: String, arguments: [CVarArgType])
+  public init(format: String, arguments: [CVarArg])

-  public init(format: String, locale: NSLocale?, _ args: CVarArgType...)
+  public init(format: String, locale: NSLocale?, _ args: CVarArg...)

-  public init(format: String, locale: NSLocale?, arguments: [CVarArgType])
+  public init(format: String, locale: NSLocale?, arguments: [CVarArg])

-  public func lengthOfBytesUsingEncoding(encoding: NSStringEncoding) -> Int
+  public func lengthOfBytes(usingEncoding encoding: NSStringEncoding) -> Int

-  public func lineRangeForRange(aRange: Range<Index>) -> Range<Index>
+  public func lineRange(for aRange: Range<Index>) -> Range<Index>

-  public func linguisticTagsInRange(
-    range: Range<Index>,
-    scheme tagScheme: String,
-    options opts: NSLinguisticTaggerOptions = [],
-    orthography: NSOrthography? = nil,
-    tokenRanges: UnsafeMutablePointer<[Range<Index>]> = nil // FIXME:Can this be nil?
-  ) -> [String]
+  public func linguisticTags(
+    in range: Range<Index>,
+    scheme tagScheme: String,
+    options opts: NSLinguisticTaggerOptions = [],
+    orthography: NSOrthography? = nil,
+    tokenRanges: UnsafeMutablePointer<[Range<Index>]> = nil // FIXME:Can this be nil?
+  ) -> [String]

-  public var localizedLowercaseString: String
+  public var localizedLowercase: String

-  public func lowercaseStringWithLocale(locale: NSLocale?) -> String
+  public func lowercaseString(with locale: NSLocale?) -> String

-  func maximumLengthOfBytesUsingEncoding(encoding: NSStringEncoding) -> Int
+  func maximumLengthOfBytes(usingEncoding encoding: NSStringEncoding) -> Int

-  public func paragraphRangeForRange(aRange: Range<Index>) -> Range<Index>
+  public func paragraphRange(for aRange: Range<Index>) -> Range<Index>

-  public func rangeOfCharacterFromSet(
-    aSet: NSCharacterSet,
-    options mask:NSStringCompareOptions = [],
-    range aRange: Range<Index>? = nil
-  ) -> Range<Index>?
+  public func rangeOfCharacter(
+    from aSet: NSCharacterSet,
+    options mask:NSStringCompareOptions = [],
+    range aRange: Range<Index>? = nil
+  ) -> Range<Index>?

-  func rangeOfComposedCharacterSequenceAtIndex(anIndex: Index) -> Range<Index>
+  func rangeOfComposedCharacterSequence(at anIndex: Index) -> Range<Index>

-  public func rangeOfComposedCharacterSequencesForRange(
-    range: Range<Index>
-  ) -> Range<Index>
+  public func rangeOfComposedCharacterSequences(
+    for range: Range<Index>
+  ) -> Range<Index>

-  public func rangeOfString(
-    aString: String,
-    options mask: NSStringCompareOptions = [],
-    range searchRange: Range<Index>? = nil,
-    locale: NSLocale? = nil
-  ) -> Range<Index>?
+  public func range(
+    of aString: String,
+    options mask: NSStringCompareOptions = [],
+    range searchRange: Range<Index>? = nil,
+    locale: NSLocale? = nil
+  ) -> Range<Index>?

-  public func localizedStandardContainsString(string: String) -> Bool
+  public func localizedStandardContains(string: String) -> Bool

-  public func localizedStandardRangeOfString(string: String) -> Range<Index>?
+  public func localizedStandardRange(of string: String) -> Range<Index>?

-  public var stringByAbbreviatingWithTildeInPath: String
+  public var abbreviatingWithTildeInPath: String

-  public func stringByAddingPercentEncodingWithAllowedCharacters(
-    allowedCharacters: NSCharacterSet
-  ) -> String?
+  public func addingPercentEncoding(
+    withAllowedCharaters allowedCharacters: NSCharacterSet
+  ) -> String?

-  public func stringByAddingPercentEscapesUsingEncoding(
-    encoding: NSStringEncoding
-  ) -> String?
+  public func addingPercentEscapes(
+    usingEncoding encoding: NSStringEncoding
+  ) -> String?

-  public func stringByAppendingFormat(
-    format: String, _ arguments: CVarArgType...
-  ) -> String
+  public func appendingFormat(
+    format: String, _ arguments: CVarArg...
+  ) -> String

-  public func stringByAppendingPathComponent(aString: String) -> String
+  public func appendingPathComponent(aString: String) -> String

-  public func stringByAppendingPathExtension(ext: String) -> String?
+  public func appendingPathExtension(ext: String) -> String?

-  public func stringByAppendingString(aString: String) -> String
+  public func appending(aString: String) -> String

-  public var stringByDeletingLastPathComponent: String
+  public var deletingLastPathComponent: String

-  public var stringByDeletingPathExtension: String
+  public var deletingPathExtension: String

-  public var stringByExpandingTildeInPath: String
+  public var expandingTildeInPath: String

-  public func stringByFoldingWithOptions(
-    options: NSStringCompareOptions, locale: NSLocale?
-  ) -> String
+  public func folding(
+    options: NSStringCompareOptions = [], locale: NSLocale?
+  ) -> String

-  public func stringByPaddingToLength(
-    newLength: Int, withString padString: String, startingAtIndex padIndex: Int
-  ) -> String
+  public func padding(
+    toLength newLength: Int,
+    with padString: String,
+    startingAt padIndex: Int
+  ) -> String

-  public var stringByRemovingPercentEncoding: String?
+  public var removingPercentEncoding: String?

-  public func stringByReplacingCharactersInRange(
-    range: Range<Index>, withString replacement: String
-  ) -> String
+  public func replacingCharacters(
+    in range: Range<Index>, with replacement: String
+  ) -> String

-  public func stringByReplacingOccurrencesOfString(
-    target: String,
-    withString replacement: String,
-    options: NSStringCompareOptions = [],
-    range searchRange: Range<Index>? = nil
-  ) -> String
+  public func replacingOccurrences(
+    of target: String,
+    with replacement: String,
+    options: NSStringCompareOptions = [],
+    range searchRange: Range<Index>? = nil
+  ) -> String

-  public func stringByReplacingPercentEscapesUsingEncoding(
-    encoding: NSStringEncoding
-  ) -> String?
+  public func replacingPercentEscapes(
+    usingEncoding encoding: NSStringEncoding
+  ) -> String?

-  public var stringByResolvingSymlinksInPath: String
+  public var resolvingSymlinksInPath: String

-  public var stringByStandardizingPath: String
+  public var standardizingPath: String

-  public func stringByTrimmingCharactersInSet(set: NSCharacterSet) -> String
+  public func trimmingCharacters(in set: NSCharacterSet) -> String

-  public func stringsByAppendingPaths(paths: [String]) -> [String]
+  public func strings(byAppendingPaths paths: [String]) -> [String]

-  public func substringFromIndex(index: Index) -> String
+  public func substring(from index: Index) -> String

-  public func substringToIndex(index: Index) -> String
+  public func substring(to index: Index) -> String

-  public func substringWithRange(aRange: Range<Index>) -> String
+  public func substring(with aRange: Range<Index>) -> String

-  public var localizedUppercaseString: String
+  public var localizedUppercase: String

-  public func uppercaseStringWithLocale(locale: NSLocale?) -> String
+  public func uppercaseString(with locale: NSLocale?) -> String

-  public func writeToFile(
-    path: String, atomically useAuxiliaryFile:Bool,
-    encoding enc: NSStringEncoding
-  ) throws
+  public func write(
+    toFile path: String, atomically useAuxiliaryFile:Bool,
+    encoding enc: NSStringEncoding
+  ) throws

-  public func writeToURL(
-    url: NSURL, atomically useAuxiliaryFile: Bool,
-    encoding enc: NSStringEncoding
-  ) throws
+  public func write(
+    to url: NSURL, atomically useAuxiliaryFile: Bool,
+    encoding enc: NSStringEncoding
+  ) throws

-  public func stringByApplyingTransform(
-    transform: String, reverse: Bool
-  ) -> String?
+  public func applyingTransform(
+    transform: String, reverse: Bool
+  ) -> String?

-  public func containsString(other: String) -> Bool
+  public func contains(other: String) -> Bool

-  public func localizedCaseInsensitiveContainsString(other: String) -> Bool
+  public func localizedCaseInsensitiveContains(other: String) -> Bool
 }
```

* 其它的修改：

```diff
 extension String {
-  public static func localizedNameOfStringEncoding(
-    encoding: NSStringEncoding
-  ) -> String
+  public static func localizedName(
+    ofStringEncoding encoding: NSStringEncoding
+  ) -> String

-  public static func pathWithComponents(components: [String]) -> String
+  public static func path(withComponents components: [String]) -> String

-  public init?(UTF8String bytes: UnsafePointer<CChar>)
+  public init?(utf8String bytes: UnsafePointer<CChar>)

-  public func canBeConvertedToEncoding(encoding: NSStringEncoding) -> Bool
+  public func canBeConverted(toEncoding encoding: NSStringEncoding) -> Bool

-  public var capitalizedString: String
+  public var capitalized: String

-  public var localizedCapitalizedString: String
+  public var localizedCapitalized: String

-  public func capitalizedStringWithLocale(locale: NSLocale?) -> String
+  public func capitalized(with locale: NSLocale?) -> String

-  public func commonPrefixWithString(
-    aString: String, options: NSStringCompareOptions) -> String
+  public func commonPrefix(
+    with aString: String, options: NSStringCompareOptions = []) -> String

-  public func completePathIntoString(
-    outputName: UnsafeMutablePointer<String> = nil,
-    caseSensitive: Bool,
-    matchesIntoArray: UnsafeMutablePointer<[String]> = nil,
-    filterTypes: [String]? = nil
-  ) -> Int
+  public func completePath(
+    into outputName: UnsafeMutablePointer<String> = nil,
+    caseSensitive: Bool,
+    matchesInto matchesIntoArray: UnsafeMutablePointer<[String]> = nil,
+    filterTypes: [String]? = nil
+  ) -> Int

-  public func componentsSeparatedByCharactersInSet(
-    separator: NSCharacterSet
-  ) -> [String]
+  public func componentsSeparatedByCharacters(
+    in separator: NSCharacterSet
+  ) -> [String]

-  public func componentsSeparatedByString(separator: String) -> [String]
+  public func componentsSeparated(by separator: String) -> [String]

-  public func cStringUsingEncoding(encoding: NSStringEncoding) -> [CChar]?
+  public func cString(usingEncoding encoding: NSStringEncoding) -> [CChar]?

-  public func dataUsingEncoding(
-    encoding: NSStringEncoding,
-    allowLossyConversion: Bool = false
-  ) -> NSData?
+  public func data(
+    usingEncoding encoding: NSStringEncoding,
+    allowLossyConversion: Bool = false
+  ) -> NSData?

-  public func enumerateLinguisticTagsInRange(
-    range: Range<Index>,
-    scheme tagScheme: String,
-    options opts: NSLinguisticTaggerOptions,
-    orthography: NSOrthography?,
-    _ body:
-      (String, Range<Index>, Range<Index>, inout Bool) -> ()
-  )
+  public func enumerateLinguisticTags(
+    in range: Range<Index>,
+    scheme tagScheme: String,
+    options opts: NSLinguisticTaggerOptions = [],
+    orthography: NSOrthography? = nil,
+    _ body:
+      (String, Range<Index>, Range<Index>, inout Bool) -> ()
+  )

-  public func enumerateSubstringsInRange(
-    range: Range<Index>,
-    options opts:NSStringEnumerationOptions,
-    _ body: (
-      substring: String?, substringRange: Range<Index>,
-      enclosingRange: Range<Index>, inout Bool
-    ) -> ()
-  )
+  public func enumerateSubstrings(
+    in range: Range<Index>,
+    options opts:NSStringEnumerationOptions = [],
+    _ body: (
+      substring: String?, substringRange: Range<Index>,
+      enclosingRange: Range<Index>, inout Bool
+    ) -> ()
+  )

-  public func fileSystemRepresentation() -> [CChar]
+  public var fileSystemRepresentation: [CChar]

-  public func getBytes(
-    inout buffer: [UInt8],
-    maxLength maxBufferCount: Int,
-    usedLength usedBufferCount: UnsafeMutablePointer<Int>,
-    encoding: NSStringEncoding,
-    options: NSStringEncodingConversionOptions,
-    range: Range<Index>,
-    remainingRange leftover: UnsafeMutablePointer<Range<Index>>
-  ) -> Bool
+  public func getBytes(
+    inout buffer: [UInt8],
+    maxLength maxBufferCount: Int,
+    usedLength usedBufferCount: UnsafeMutablePointer<Int>,
+    encoding: NSStringEncoding,
+    options: NSStringEncodingConversionOptions = [],
+    range: Range<Index>,
+    remaining leftover: UnsafeMutablePointer<Range<Index>>
+  ) -> Bool

-  public func getLineStart(
-    start: UnsafeMutablePointer<Index>,
-    end: UnsafeMutablePointer<Index>,
-    contentsEnd: UnsafeMutablePointer<Index>,
-    forRange: Range<Index>
-  )
+  public func getLineStart(
+    start: UnsafeMutablePointer<Index>,
+    end: UnsafeMutablePointer<Index>,
+    contentsEnd: UnsafeMutablePointer<Index>,
+    for range: Range<Index>
+  )

-  public func getParagraphStart(
-    start: UnsafeMutablePointer<Index>,
-    end: UnsafeMutablePointer<Index>,
-    contentsEnd: UnsafeMutablePointer<Index>,
-    forRange: Range<Index>
-  )
+  public func getParagraphStart(
+    start: UnsafeMutablePointer<Index>,
+    end: UnsafeMutablePointer<Index>,
+    contentsEnd: UnsafeMutablePointer<Index>,
+    for range: Range<Index>
+  )

   public init(
-    contentsOfURL url: NSURL,
+    contentsOf url: NSURL,
     encoding enc: NSStringEncoding
   ) throws

   public init(
-    contentsOfURL url: NSURL,
+    contentsOf url: NSURL,
     usedEncoding enc: UnsafeMutablePointer<NSStringEncoding> = nil
   ) throws

   public init?(
-    CString: UnsafePointer<CChar>,
+    cString: UnsafePointer<CChar>,
     encoding enc: NSStringEncoding
   )

-  public init(format: String, _ arguments: CVarArgType...)
+  public init(format: String, _ arguments: CVarArg...)

-  public init(format: String, arguments: [CVarArgType])
+  public init(format: String, arguments: [CVarArg])

-  public init(format: String, locale: NSLocale?, _ args: CVarArgType...)
+  public init(format: String, locale: NSLocale?, _ args: CVarArg...)

-  public init(format: String, locale: NSLocale?, arguments: [CVarArgType])
+  public init(format: String, locale: NSLocale?, arguments: [CVarArg])

-  public func lengthOfBytesUsingEncoding(encoding: NSStringEncoding) -> Int
+  public func lengthOfBytes(usingEncoding encoding: NSStringEncoding) -> Int

-  public func lineRangeForRange(aRange: Range<Index>) -> Range<Index>
+  public func lineRange(for aRange: Range<Index>) -> Range<Index>

-  public func linguisticTagsInRange(
-    range: Range<Index>,
-    scheme tagScheme: String,
-    options opts: NSLinguisticTaggerOptions = [],
-    orthography: NSOrthography? = nil,
-    tokenRanges: UnsafeMutablePointer<[Range<Index>]> = nil // FIXME:Can this be nil?
-  ) -> [String]
+  public func linguisticTags(
+    in range: Range<Index>,
+    scheme tagScheme: String,
+    options opts: NSLinguisticTaggerOptions = [],
+    orthography: NSOrthography? = nil,
+    tokenRanges: UnsafeMutablePointer<[Range<Index>]> = nil // FIXME:Can this be nil?
+  ) -> [String]

-  public var localizedLowercaseString: String
+  public var localizedLowercase: String

-  public func lowercaseStringWithLocale(locale: NSLocale?) -> String
+  public func lowercaseString(with locale: NSLocale?) -> String

-  func maximumLengthOfBytesUsingEncoding(encoding: NSStringEncoding) -> Int
+  func maximumLengthOfBytes(usingEncoding encoding: NSStringEncoding) -> Int

-  public func paragraphRangeForRange(aRange: Range<Index>) -> Range<Index>
+  public func paragraphRange(for aRange: Range<Index>) -> Range<Index>

-  public func rangeOfCharacterFromSet(
-    aSet: NSCharacterSet,
-    options mask:NSStringCompareOptions = [],
-    range aRange: Range<Index>? = nil
-  ) -> Range<Index>?
+  public func rangeOfCharacter(
+    from aSet: NSCharacterSet,
+    options mask:NSStringCompareOptions = [],
+    range aRange: Range<Index>? = nil
+  ) -> Range<Index>?

-  func rangeOfComposedCharacterSequenceAtIndex(anIndex: Index) -> Range<Index>
+  func rangeOfComposedCharacterSequence(at anIndex: Index) -> Range<Index>

-  public func rangeOfComposedCharacterSequencesForRange(
-    range: Range<Index>
-  ) -> Range<Index>
+  public func rangeOfComposedCharacterSequences(
+    for range: Range<Index>
+  ) -> Range<Index>

-  public func rangeOfString(
-    aString: String,
-    options mask: NSStringCompareOptions = [],
-    range searchRange: Range<Index>? = nil,
-    locale: NSLocale? = nil
-  ) -> Range<Index>?
+  public func range(
+    of aString: String,
+    options mask: NSStringCompareOptions = [],
+    range searchRange: Range<Index>? = nil,
+    locale: NSLocale? = nil
+  ) -> Range<Index>?

-  public func localizedStandardContainsString(string: String) -> Bool
+  public func localizedStandardContains(string: String) -> Bool

-  public func localizedStandardRangeOfString(string: String) -> Range<Index>?
+  public func localizedStandardRange(of string: String) -> Range<Index>?

-  public var stringByAbbreviatingWithTildeInPath: String
+  public var abbreviatingWithTildeInPath: String

-  public func stringByAddingPercentEncodingWithAllowedCharacters(
-    allowedCharacters: NSCharacterSet
-  ) -> String?
+  public func addingPercentEncoding(
+    withAllowedCharaters allowedCharacters: NSCharacterSet
+  ) -> String?

-  public func stringByAddingPercentEscapesUsingEncoding(
-    encoding: NSStringEncoding
-  ) -> String?
+  public func addingPercentEscapes(
+    usingEncoding encoding: NSStringEncoding
+  ) -> String?

-  public func stringByAppendingFormat(
-    format: String, _ arguments: CVarArgType...
-  ) -> String
+  public func appendingFormat(
+    format: String, _ arguments: CVarArg...
+  ) -> String

-  public func stringByAppendingPathComponent(aString: String) -> String
+  public func appendingPathComponent(aString: String) -> String

-  public func stringByAppendingPathExtension(ext: String) -> String?
+  public func appendingPathExtension(ext: String) -> String?

-  public func stringByAppendingString(aString: String) -> String
+  public func appending(aString: String) -> String

-  public var stringByDeletingLastPathComponent: String
+  public var deletingLastPathComponent: String

-  public var stringByDeletingPathExtension: String
+  public var deletingPathExtension: String

-  public var stringByExpandingTildeInPath: String
+  public var expandingTildeInPath: String

-  public func stringByFoldingWithOptions(
-    options: NSStringCompareOptions, locale: NSLocale?
-  ) -> String
+  public func folding(
+    options: NSStringCompareOptions = [], locale: NSLocale?
+  ) -> String

-  public func stringByPaddingToLength(
-    newLength: Int, withString padString: String, startingAtIndex padIndex: Int
-  ) -> String
+  public func padding(
+    toLength newLength: Int,
+    with padString: String,
+    startingAt padIndex: Int
+  ) -> String

-  public var stringByRemovingPercentEncoding: String?
+  public var removingPercentEncoding: String?

-  public func stringByReplacingCharactersInRange(
-    range: Range<Index>, withString replacement: String
-  ) -> String
+  public func replacingCharacters(
+    in range: Range<Index>, with replacement: String
+  ) -> String

-  public func stringByReplacingOccurrencesOfString(
-    target: String,
-    withString replacement: String,
-    options: NSStringCompareOptions = [],
-    range searchRange: Range<Index>? = nil
-  ) -> String
+  public func replacingOccurrences(
+    of target: String,
+    with replacement: String,
+    options: NSStringCompareOptions = [],
+    range searchRange: Range<Index>? = nil
+  ) -> String

-  public func stringByReplacingPercentEscapesUsingEncoding(
-    encoding: NSStringEncoding
-  ) -> String?
+  public func replacingPercentEscapes(
+    usingEncoding encoding: NSStringEncoding
+  ) -> String?

-  public var stringByResolvingSymlinksInPath: String
+  public var resolvingSymlinksInPath: String

-  public var stringByStandardizingPath: String
+  public var standardizingPath: String

-  public func stringByTrimmingCharactersInSet(set: NSCharacterSet) -> String
+  public func trimmingCharacters(in set: NSCharacterSet) -> String

-  public func stringsByAppendingPaths(paths: [String]) -> [String]
+  public func strings(byAppendingPaths paths: [String]) -> [String]

-  public func substringFromIndex(index: Index) -> String
+  public func substring(from index: Index) -> String

-  public func substringToIndex(index: Index) -> String
+  public func substring(to index: Index) -> String

-  public func substringWithRange(aRange: Range<Index>) -> String
+  public func substring(with aRange: Range<Index>) -> String

-  public var localizedUppercaseString: String
+  public var localizedUppercase: String

-  public func uppercaseStringWithLocale(locale: NSLocale?) -> String
+  public func uppercaseString(with locale: NSLocale?) -> String

-  public func writeToFile(
-    path: String, atomically useAuxiliaryFile:Bool,
-    encoding enc: NSStringEncoding
-  ) throws
+  public func write(
+    toFile path: String, atomically useAuxiliaryFile:Bool,
+    encoding enc: NSStringEncoding
+  ) throws

-  public func writeToURL(
-    url: NSURL, atomically useAuxiliaryFile: Bool,
-    encoding enc: NSStringEncoding
-  ) throws
+  public func write(
+    to url: NSURL, atomically useAuxiliaryFile: Bool,
+    encoding enc: NSStringEncoding
+  ) throws

-  public func stringByApplyingTransform(
-    transform: String, reverse: Bool
-  ) -> String?
+  public func applyingTransform(
+    transform: String, reverse: Bool
+  ) -> String?

-  public func containsString(other: String) -> Bool
+  public func contains(other: String) -> Bool

-  public func localizedCaseInsensitiveContainsString(other: String) -> Bool
+  public func localizedCaseInsensitiveContains(other: String) -> Bool
 }
	•	Miscellaneous changes.
 public struct EnumeratedIterator<Base : IteratorProtocol> : ... {
-  public typealias Element = (index: Int, element: Base.Element)
+  public typealias Element = (offset: Int, element: Base.Element)
 }

 public struct Array<Element> : ... {
   // Same changes were also applied to `ArraySlice` and `ContiguousArray`.

-  public init(count: Int, repeatedValue: Element)
+  public init(repeating: Element, count: Int)
 }

 public protocol Sequence : ... {
   public func split(
-    maxSplit: Int = Int.max,
+    maxSplits maxSplits: Int = Int.max,
-    allowEmptySlices: Bool = false,
+    omittingEmptySubsequences: Bool = true,
     @noescape isSeparator: (Iterator.Element) throws -> Bool
   ) rethrows -> [SubSequence]
 }

 extension Sequence where Iterator.Element : Equatable {
   public func split(
-    separator: Iterator.Element,
+    separator separator: Iterator.Element,
-    maxSplit: Int = Int.max,
+    maxSplits maxSplits: Int = Int.max,
-    allowEmptySlices: Bool = false
+    omittingEmptySubsequences: Bool = true
   ) -> [AnySequence<Iterator.Element>] {
 }


 public protocol Sequence : ... {
-  public func lexicographicalCompare<
+  public func lexicographicallyPrecedes<
     OtherSequence : Sequence where OtherSequence.Iterator.Element == Iterator.Element
   >(
     other: OtherSequence,
     @noescape isOrderedBefore: (Iterator.Element, Iterator.Element) throws -> Bool
   ) rethrows -> Bool {
 }

 extension Sequence where Iterator.Element : Equatable {
-  public func lexicographicalCompare<
+  public func lexicographicallyPrecedes<
     OtherSequence : Sequence where OtherSequence.Iterator.Element == Iterator.Element
   >(
     other: OtherSequence
   ) -> Bool {
 }

 public protocol Collection : ... {
-  func prefixUpTo(end: Index) -> SubSequence
+  func prefix(upTo end: Index) -> SubSequence

-  func suffixFrom(start: Index) -> SubSequence
+  func suffix(from start: Index) -> SubSequence

-  func prefixThrough(position: Index) -> SubSequence
+  func prefix(through position: Index) -> SubSequence
 }

 // Changes to this protocol affect `Array`, `ArraySlice`, `ContiguousArray` and
 // other types.
 public protocol RangeReplaceableCollection : ... {
+  public init(repeating repeatedValue: Iterator.Element, count: Int)

-  mutating func replaceRange<
+  mutating func replaceSubrange<
     C : CollectionType where C.Iterator.Element == Iterator.Element
   >(
     subRange: Range<Int>, with newElements: C
   )

-  mutating func insert(newElement: Iterator.Element, atIndex i: Int)
+  mutating func insert(newElement: Iterator.Element, at i: Int)

-  mutating func insertContentsOf<
+  mutating func insert<
     S : Collection where S.Iterator.Element == Iterator.Element
-  >(newElements: S, at i: Index)
+  >(contentsOf newElements: S, at i: Index)

-  mutating func removeAtIndex(index: Int) -> Element
+  mutating func remove(at index: Int) -> Element

-  mutating func removeAll(keepCapacity keepCapacity: Bool = false)
+  mutating func removeAll(keepingCapacity keepingCapacity: Bool = false)

-  mutating func removeRange(subRange: Range<Index>)
+  mutating func removeSubrange(subRange: Range<Index>)

-  mutating func appendContentsOf<S : SequenceType>(newElements: S)
+  mutating func append<S : SequenceType>(contentsOf newElements: S)
 }

+extension Set : SetAlgebra {}

 public struct Dictionary<Key : Hashable, Value> : ... {
-  public typealias Element = (Key, Value)
+  public typealias Element = (key: Key, value: Value)
 }

 public struct DictionaryLiteral<Key, Value> : ... {
-  public typealias Element = (Key, Value)
+  public typealias Element = (key: Key, value: Value)
 }

 extension String {
-  public mutating func appendContentsOf(other: String) {
+  public mutating func append(other: String) {

-  public mutating appendContentsOf<S : SequenceType>(newElements: S)
+  public mutating append<S : SequenceType>(contentsOf newElements: S)

-  public mutating func replaceRange<
+  public mutating func replaceSubrange<
     C: CollectionType where C.Iterator.Element == Character
   >(
     subRange: Range<Index>, with newElements: C
   )

-  public mutating func replaceRange(
+  public mutating func replaceSubrange(
     subRange: Range<Index>, with newElements: String
   )

-  public mutating func insert(newElement: Character, atIndex i: Index)
+  public mutating func insert(newElement: Character, at i: Index)

-  public mutating func insertContentsOf<
+  public mutating func insert<
     S : Collection where S.Iterator.Element == Character
-  >(newElements: S, at i: Index)
+  >(contentsOf newElements: S, at i: Index)

-  public mutating func removeAtIndex(i: Index) -> Character
+  public mutating func remove(at i: Index) -> Character

-  public mutating func removeRange(subRange: Range<Index>)
+  public mutating func removeSubrange(subRange: Range<Index>)

-  mutating func removeAll(keepCapacity keepCapacity: Bool = false)
+  mutating func removeAll(keepingCapacity keepingCapacity: Bool = false)

-  public init(count: Int, repeatedValue c: Character)
+  public init(repeating repeatedValue: Character, count: Int)

-  public init(count: Int, repeatedValue c: UnicodeScalar)
+  public init(repeating repeatedValue: UnicodeScalar, count: Int)

-  public var utf8: UTF8View { get }
+  public var utf8: UTF8View { get set }

-  public var utf16: UTF16View { get }
+  public var utf16: UTF16View { get set }

-  public var characters: CharacterView { get }
+  public var characters: CharacterView { get set }
 }

 public enum UnicodeDecodingResult {
-  case Result(UnicodeScalar)
-  case EmptyInput
-  case Error
+  case scalarValue(UnicodeScalar)
+  case emptyInput
+  case error
 }

 public struct ManagedBufferPointer<Value, Element> : ... {
-  public var allocatedElementCount: Int { get }
+  public var capacity: Int { get }
 }

 public struct RangeIterator<Element : ForwardIndex> : ... {
-  public var startIndex: Element { get set }
-  public var endIndex: Element { get set }
 }

 public struct ObjectIdentifier : ... {
-  public var uintValue: UInt { get }
 }
 extension UInt {
+  /// Create a `UInt` that captures the full value of `objectID`.
+  public init(_ objectID: ObjectIdentifier)
 }
 extension Int {
+  /// Create an `Int` that captures the full value of `objectID`.
+  public init(_ objectID: ObjectIdentifier)
 }

-public struct Repeat<Element> : ... { ... }
+public struct Repeated<Element> : ... { ... }

 public struct StaticString : ... {
-  public var byteSize: Int { get }
+  public var utf8CodeUnitCount: Int { get }

   // Use the 'String(_:)' initializer.
-  public var stringValue: String { get }
 }

 extension Strideable {
-  public func stride(to end: Self, by stride: Stride) -> StrideTo<Self>
 }
+public func stride<T : Strideable>(from start: T, to end: T, by stride: T.Stride) -> StrideTo<T>

 extension Strideable {
-  public func stride(through end: Self, by stride: Stride) -> StrideThrough<Self>
 }
+public func stride<T : Strideable>(from start: T, through end: T, by stride: T.Stride) -> StrideThrough<T>

 public func transcode<
   Input : IteratorProtocol,
   InputEncoding : UnicodeCodec,
   OutputEncoding : UnicodeCodec
   where InputEncoding.CodeUnit == Input.Element>(
   inputEncoding: InputEncoding.Type, _ outputEncoding: OutputEncoding.Type,
   _ input: Input, _ output: (OutputEncoding.CodeUnit) -> Void,
-  stopOnError: Bool
+  stoppingOnError: Bool
 ) -> Bool

 extension UnsafeMutablePointer {
-  public static func alloc(num: Int) -> UnsafeMutablePointer<Pointee>
+  public init(allocatingCapacity count: Int)

-  public func dealloc(num: Int)
+  public func deallocateCapacity(count: Int)

-  public func initialize(newvalue: Memory)
+  public func initializePointee(newValue: Pointee, count: Int = 1)

-  public func move() -> Memory
+  public func take() -> Pointee

-  public func destroy()
-  public func destroy(count: Int)
+  public func deinitializePointee(count count: Int = 1)
 }

-public struct COpaquePointer : ... { ... }
+public struct OpaquePointer : ... { ... }

-public func unsafeAddressOf(object: AnyObject) -> UnsafePointer<Void>
+public func unsafeAddress(of object: AnyObject) -> UnsafePointer<Void>

-public func unsafeBitCast<T, U>(x: T, _: U.Type) -> U
+public func unsafeBitCast<T, U>(x: T, to: U.Type) -> U

-public func unsafeDowncast<T : AnyObject>(x: AnyObject) -> T
+public func unsafeDowncast<T : AnyObject>(x: AnyObject, to: T.Type) -> T

-public func print<Target: OutputStream>(
+public func print<Target : OutputStream>(
   items: Any...,
   separator: String = " ",
   terminator: String = "\n",
-  inout toStream output: Target
+  inout to output: Target
 )

-public func debugPrint<Target: OutputStream>(
+public func debugPrint<Target : OutputStream>(
   items: Any...,
   separator: String = " ",
   terminator: String = "\n",
-  inout toStream output: Target
+  inout to output: Target
 )

 public struct Unmanaged<Instance : AnyObject> {
-  public func toOpaque() -> COpaquePointer
 }
 extension OpaquePointer {
+  public init<T>(bitPattern bits: Unmanaged<T>)
 }

 public enum UnicodeDecodingResult
+  : Equatable {
-  public var isEmptyInput: Bool
}

-public func readLine(stripNewline stripNewline: Bool = true) -> String?
+public func readLine(strippingNewline strippingNewline: Bool = true) -> String?

 struct UnicodeScalar {
   // Use 'UnicodeScalar("\0")' instead.
-  init()

-  public func escape(asASCII forceASCII: Bool) -> String
+  public func escaped(asASCII forceASCII: Bool) -> String
 }

 public func transcode<
   Input : IteratorProtocol,
   InputEncoding : UnicodeCodec,
   OutputEncoding : UnicodeCodec
   where InputEncoding.CodeUnit == Input.Element
 >(
-  inputEncoding: InputEncoding.Type, _ outputEncoding: OutputEncoding.Type,
-  _ input: Input, _ output: (OutputEncoding.CodeUnit) -> Void,
-  stoppingOnError stopOnError: Bool
+  input: Input,
+  from inputEncoding: InputEncoding.Type,
+  to outputEncoding: OutputEncoding.Type,
+  stoppingOnError stopOnError: Bool,
+  sendingOutputTo processCodeUnit: (OutputEncoding.CodeUnit) -> Void
 ) -> Bool

 extension UTF16 {
-  public static func measure<
+  public static func transcodedLength<
     Encoding : UnicodeCodec, Input : IteratorProtocol
     where Encoding.CodeUnit == Input.Element
   >(
-    _: Encoding.Type, input: Input, repairIllFormedSequences: Bool
+    of input: Input,
+    decodedAs sourceEncoding: Encoding.Type,
+    repairingIllFormedSequences: Bool
-  ) -> (Int, Bool)?
+  ) -> (count: Int, isASCII: Bool)? {
 }

-public struct RawByte {}

-final public class VaListBuilder {}

-public func withVaList<R>(
-  builder: VaListBuilder,
-  @noescape _ f: CVaListPointer -> R)
--> R
```

## 第五部分 对已有代码的影响

这份提议为Swift代码带来了大量的破坏性改变，以至于我们需要一个转换工具把代码从Swift 2转换为Swift 3。这份提议中提及的API改动是这个转换工具主要的信息来源。除此之外，为了帮助编译器提供更好的错误信息并且在编辑器中弹出Fix-Its建议，标准库将把旧的名称标记为`unavailable`，并提供一个`rename`说明。
