Core Data 学习笔记（三）被管理对象及访问方法
===

## 五、被管理对象

本章节提供关于“被管理对象（Managed Object）”的基础说明，如如何存储被管理对象的数据、如何实现一个自定义被管理对象类、对象的生命周期和断层（Faulting）等。

### 5.1 基础

被管理对象是[`NSManagedObject`]()类的实例或者`NSManagedObject`子类的实例，其表现为一个数据实体的实例。`NSManagedObject`用于实现所有被管理数据模型所需要的基础行为的通用类。我们可以自定义创建`NSManagedObject`的子类。如果我们不需要给实体添加额外的操作逻辑，我们就不需要给为实体单独定制`NSManagedObject`的子类。

一个被管理对象与一个实体描述类（[`NSEntityDescription`](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSEntityDescription_Class/)的实例）相关联，这个实体描述类提供有关这个实体的元数据的描述（实体名称、实体属性、实体间的关系等）。还与一个被管理对象上下文相关联，这个上下文追踪这个被管理对象的改变路径。

在被管理上下文中，一个被管理对象表示持久化存储当中一条记录。在一个给定的上下文，对于一条给定的持久化存储记录，只能有一个相对应的被管理对象，但是也许有多个上下文包含有表现通一个记录的不同的被管理对象。

### 5.2 属性和数据存储

在某些方面，一个[`NSManagedObject`](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObject_Class/s)泛型容器对象，有效地为[`NSEntityDescription`](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSEntityDescription_Class/)对象定义的属性提供存储字典。`NSManagedObject`提供多种属性值，常见类型包括字符串，日期，和数量（详情请参阅[`NSAttributeDescription`](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSAttributeDescription_Class/)
）。因此通常不需要在子类中定义实例变量。如果你使用二进制大数据对象有一些性能方面的考虑要牢记（[Large Data Objects(BLOBs)](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/CoreData/Performance.html#//apple_ref/doc/uid/TP40001075-CH25-SW11)）。

#### 5.2.1 使用非标准属性

默认情况下，`NSManagedObject`将它的属性作为对象存储在一个内部结构中，通常，CoreData使用在它本身控制下的存储要比使用自定义实例变量更高效。

有时您需要使用不被直接支持的类型，例如颜色和结构。例如，在一个图形应用程序，你可能想定义一个矩形实体，具有颜色属性和范围，分别是`NSColor`实例和`NSRect`结构。这种情况需要您创建一个`NSManagedObject`的子类。

#### 5.2.2 日期、时间和存储时区

`NSManagedObject`使用`NSDate`对象代表日期属性，使用基于GMT的`NSTimeInterval`值存储时间。时区没有明确地被存储-事实上你应该一直使CoreData日期属性基于GMT，以便规范地搜索数据库。如果你需要保存时区信息，你需要存储一个时区属性在你的模型中，这可能需要您创建一个`NSManagedObject`的子类。


## 六、被管理对象访问方法



## 参考

* [Managed Objects](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/CoreData/LifeofaManagedObject.html)
* [Managed Object Accessor Methods]()
