Core Data 学习笔记（三）被管理对象
===

## 五、被管理对象

本章节提供关于“被管理对象（Managed Object）”的基础说明，如如何存储被管理对象的数据、如何实现一个自定义被管理对象类、对象的生命周期和断层（Faulting）等。

### 5.1 基础

被管理对象是[`NSManagedObject`]()类的实例或者`NSManagedObject`子类的实例，其表现为一个数据实体的实例。`NSManagedObject`用于实现所有被管理数据模型所需要的基础行为的通用类。我们可以自定义创建`NSManagedObject`的子类。~~如果我们不需要给实体添加额外的操作逻辑，我们就不需要给为实体单独定制`NSManagedObject`的子类。~~ 在使用Swift语言的过程中，为每个实体创建一个子类，然后进行相应的扩展，是有必要的。

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

## 六、被管理对象的创建与删除

当你已经定义了你的被管理对象模型，并且初始化好CoreData栈之后，你已经做好准备为持久化创建对象了。

### 6.1 创建被管理对象

`NSManagedObject`实例需要两个元素，一个是实体对象描述（`NSEntityDescription`），一个是持久化上下文（`NSManagedObjectContext`）。我们可以利用实体对象描述来创建一个新的被管理对象。如下所示：

```swift
var user = NSEntityDescription.insertNewObjectForEntityForName("User", inManagedObjectContext: context!) as! User
```

> 注意：
>
> * 上面实例所使用的`User`为已经创建好的`NSManagedOobject`类的子类。

### 6.2 保存被管理对象实例 

因为所有对被管理对象的改变，都需要使用其所在的上下文进行持久化操作，所以保存被管理对象实例，实际上就是调用持久话上下文对象的持久化操作。如下所示。

```swift
func saveContext () {
    if let moc = self.managedObjectContext {
        var error: NSError? = nil
        if moc.hasChanges && !moc.save(&error) {
            NSLog("Unresolved error \(error), \(error!.userInfo)")
            abort()
        }
    }
}
```

上面的代码判断在上下文中的被管理对象是否有所变化（通过判断`.hasChanges`），如果有变化，则调用`.save()`方法，来执行持久话操作。

### 6.3 提取持久化数据

数据被存储在CoreData持久话存储当中，可以使用一个[`NSFetchRequest`](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSFetchRequest_Class/)来访问已经存在的数据。从CoreData中提取数据是这个框架最有力的特性。

#### 6.3.1 提取被管理对象实例

我们可以使用如下步骤来提取持久化上下文当中的被管理对象数据：

1. 取得当前运行应用的持久化上下文
2. 根据提取数据的需要，创建提取请求（[`NSFetchRequest`](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSFetchRequest_Class/)）
3. 结合提取数据请求，在持久化上下文中执行数据提取操作
4. 将提取出的数据赋值给一个合适的数组
5. 使用这个数组

具体实现如下所示：

```swift
// 1.
var moc = UIApplication.sharedApplication().delegate?.managedObjectContext

// 2.
var request = NSFetchRequest(entityName: "User")

// 3. & 4.
var results = moc?.executeFetchRequest(request, error: nil) as! [User]

//
if results != nil {
    NSLog("Error fetching Employee objects: \(error), \(error!.userInfo)")
    // 提取空值&提取出错处理
}

// 5.
// ... 结果操作省略

```

`.executeFetchRequest`有两种可能的执行结果，一种是返回一个数组对象（存储提取结果），一种是返回`nil`。如果返回`nil`，就说明本次提取从CoreData取得了一个异常，此时就需要对此异常进行处理，以避免Bug扩大化。

#### 6.3.2 过滤提取结果

在实际生产环境中，提取结果往往是很复杂的，为了实现对复杂结果的提取，我们可以使用`NSPredicate`来作为数据提取请求的一个__过滤条件__。比如我们要从持久化上下文中提取出所有`name`值为`Aaron`的数据对象，可以使用如下的谓词（`NSPredicate`）。

```swift
var predicate = NSPredicate(format: "%K like %@", argumentArray: ["name", "Aaron"])
```

> 注意：
> 
> * 有关详细的谓词格式、字符，请[点击查看](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Predicates/)。

在定制好用于过滤提取结果的谓词后，我们需要将这个谓词赋予提取请求。

```swift
request.predicate = predicate
```

之后，我们就可以像上一节中所提到的方式进行数据的提取了。

对于提取结果的过滤，除了使用谓词外，我们还可以对提取请求本身进行定制，以实现：

* 提取结果数量限制
* 提取结果偏移量
* 提取结果的排序
* 提取结果的类型（默认为数组）

关于更多的关于提取请求本身的配置，可以[点击这里查看](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSFetchRequest_Class/index.html#//apple_ref/occ/instp/NSFetchRequest/resultType)。

## 参考

* [Managed Objects](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/CoreData/LifeofaManagedObject.html)
* [Fetching Objects](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/CoreData/FetchingObjects.html#//apple_ref/doc/uid/TP40001075-CH6-SW1)
