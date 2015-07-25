Core Data 学习笔记
===

### 简介

### API 说明

#### [NSManagedObject](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObject_Class/)

> NSManagedObject is a generic class that implements all the basic behavior required of a Core Data model object. —— 官方文档

当我们使用Xcode生成实体后，打开实体代码将会发现，实体是一个继承自`NSManagedObject`的类。通过查看iOS官方文档，我们可以了解到，`NSManagedObject`类对象包含了所有“Core Data”模型对象的基础行为操作。

##### [对象标识](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObject_Class/#//apple_ref/doc/uid/TP30001171-SW8)

* `.objectID` —— 获取对象标识

##### [对象状态](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObject_Class/#//apple_ref/doc/uid/TP30001171-SW10)

* `.managedObjectContext` —— 获取对象上下文
* `.hasChanges` —— 获取对象是否已经被插入、被删除、被存储等状态信息
* `.inserted` —— 如果对象已经被插入到一个被管理对象上下文中，则返回`true`
* `.updated` —— 如果有对象有没有存储到上下文中的改变则返回`true`
* `.deleted` —— 如果Core Data在下一次存储操作的时候将会删除对象，则返回`true`
* `.fault` —— 如果对象存在错误，则返回`true`
* `.faultingState` —— 如果对象已经被作为“被管理对象”初始化，并且没有其他的切换等状态时，返回`0`
* `.hasFaultForRelationshipNamed` —— 返回一个布尔值指示给定名称的对象关系是否存在错误

##### [生命周期与改变事件](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObject_Class/#//apple_ref/doc/uid/TP30001171-SW12)

* `contextShouldIgnoreUnmodeledPropertyChanges` —— 如果一个类实例的未被模型化的属性的改变将被标志为类实例的改变，则返回`false`
* `awakeFromFetch` —— Core Data框架自动调用，当对象被Fetch时
* `awakeFromInsert` —— Core Data框架自动调用，当接收器是第一次插入到一个托管对象上下文
* `awakeFromSnapshotEvents` ——  当接收器由于撤销、重做或其他多属性状态改变被重置时，Core Data框架自动调用
* `changedValues` —— 返回存储最后一次被提取或存储的对象的键值对字典
* `changedValuesForCurrentEvent` —— 返回一个字典，包含“键”与持久化“值”，该值是`NSManagedObjectContextObjectsDidChangeNotification`最后一次提交前被改变的值
* `committedValuesForKeys` —— 返回给定“键”所指定的属性的最后获取或保存的值的字典。
* `prepareForDeletion` —— 当对象被删除时，由Core Data自动调用
* `willSave` —— 当被保存的对象上下文被保存时，将自动调用Core Data框架。
* `didSave` —— 当受管理对象上下文完成一个保存操作后，将自动调用Core Data框架。
* `willTurnIntoFault` —— 在对象被转换为故障前，在Core Data架内自动调用。
* `didTurnIntoFault` —— 当对象被转为故障时，自动调用Core Data框架。

其他API：

* [键值对编程支持](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObject_Class/#//apple_ref/doc/uid/TP30001171-SW13)
* [数据验证](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObject_Class/#//apple_ref/doc/uid/TP30001171-SW15)
* [键值对观察支持](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObject_Class/#//apple_ref/doc/uid/TP30001171-SW16)
* [常量](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObject_Class/#//apple_ref/c/tdef/NSSnapshotEventType)

#### NSManagedObjectContext

> An instance of NSManagedObjectContext represents a single “object space” or scratch pad in an application.

`NSManagedObjectContext`是Core Data数据实体的操作上下文，用于管理“被管理对象”的集合。一个被管理对象存在且仅存在于一个管理上下文当中，一个被管理对象的多个拷贝，可以存在于多个管理上下文当中。



* [注册与提取数据](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObjectContext_Class/#//apple_ref/doc/uid/TP30001182-SW4)
* [被管理对象管理](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObjectContext_Class/#//apple_ref/doc/uid/TP30001182-SW6)
* [管理并发](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObjectContext_Class/#//apple_ref/doc/uid/TP30001182-SW8)
* [回滚管理](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObjectContext_Class/#//apple_ref/doc/uid/TP30001182-SW10)
* [父对象存储管理](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObjectContext_Class/#//apple_ref/doc/uid/TP30001182-SW14)
* 锁 —— iOS8之后，已经被删除
* [级联删除](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObjectContext_Class/#//apple_ref/doc/uid/TP30001182-SW12)
* [已注册对象](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObjectContext_Class/#//apple_ref/doc/uid/TP30001182-SW13)
* [管理过期间隔](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObjectContext_Class/#//apple_ref/doc/uid/TP30001182-SW15)
* [管理合并策略](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObjectContext_Class/#//apple_ref/doc/uid/TP30001182-SW16)
* [执行阻塞操作](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObjectContext_Class/#//apple_ref/doc/uid/TP30001182-SW44)
* [用户信息](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObjectContext_Class/#//apple_ref/doc/uid/TP30001182-SW47)
* [数据类型](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObjectContext_Class/#//apple_ref/c/tdef/NSManagedObjectContextConcurrencyType)
* [常量](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObjectContext_Class/#//apple_ref/doc/constant_group/NSManagedObjectContext_Change_Notification_User_Info_Keys)
* [通知](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObjectContext_Class/#//apple_ref/c/data/NSManagedObjectContextObjectsDidChangeNotification)

#### 查询字符串

[Predicate Format String Syntax](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Predicates/)

### 示例

#### 使用Xcode创建Entity及属性

1. 创建一个新的项目，命名为“CoreData”，并__勾选__“Use Core Data”
2. 在创建成功的新项目中，找到“CoreData.xcdatamodeld”文件，并点击打开
3. 在主面板“Entities”中，添加一个名为"Album"的实体
4. 点击“Album”实体，在右侧为实体添加两个“Attribute”，分别为“name”与“createDate”，类型都是“String”型
5. 在“菜单栏”依次点击“Editor” - “Create NSManagedObject Subclass”
6. 点击“Next”
7. 勾选“Album”实体
8. 点击“Next”
9. 选择项目目录“CoreData”，在下面的“Group”中，选择项目目录“CoreData”
10. 保存

> Notes：实体的属性名称，首字母必须小写。我们可以使用骆驼命名法对实体的属性进行命名。

#### 使用生成的NSManagedObject子类

##### 获取全部实体对象

```swift
func getEntities() -> Array<Album>? {
    
    let context = NSManagedObjectContext()
    let request = NSFetchRequest(entityName: "Album")
    
    let entities = context.executeFetchRequest(request, error: nil) as? Array<Album>
    
    return entities
}
```

##### 根据条件获取实体对象集合

条件设置

```swift
/**
用于生成给定检索条件的数据请求。

:param: entityName          数据实体名称
:param: predicateFormat     检索条件格式，检索条件格式参见[Predicate Format String Syntax](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Predicates/)
:param: arrayOfKeyWithValue 检索条件填充键值对数组

:returns: 返回生成成功的数据请求
*/
func getRequestWithPredicate(entityName: String, predicateFormat: String, arrayOfKeyWithValue: Array<String>) -> NSFetchRequest? {
    
    let predicate = NSPredicate(format: predicateFormat, argumentArray: arrayOfKeyWithValue)
    
    var request = NSFetchRequest(entityName: entityName)
    request.predicate = predicate
    
    return request
}
```

获取数据的方法

```swift
func getEntitiesByNSFetchRequest(request: NSFetchRequest) -> Array<Album>? {

    let context = NSManagedObjectContext()
    let entities = context.executeFetchRequest(request, error: nil) as? Array<Album>
    
    return entities
}
```

### 参考

#### [官方文档](https://developer.apple.com/library/ios/navigation/)

* [Core Data Programming Guide](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html) 
* [NSManagedObject](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObject_Class/)
* [NSManagedObjectContext](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObjectContext_Class/)
* [NSFetchRequest](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSFetchRequest_Class/)
* [NSPredicate](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSPredicate_Class/)
* [Predicate Format String Syntax](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Predicates/)

#### 网摘文章

* [Core Data入门](http://www.cnblogs.com/mjios/archive/2013/02/26/2932999.html) by [@M了个J](https://github.com/CoderMJLee)
* [IOS 数据存储之 Core Data详解](http://www.cnblogs.com/jerehedu/p/4607368.html) by [@杰瑞教育](http://www.cnblogs.com/jerehedu/)
* [Core Data Stack学习笔记](http://www.cnblogs.com/ios888/p/4375487.html) by [@一路奔跑](http://www.cnblogs.com/ios888/)