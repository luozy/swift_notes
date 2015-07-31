Core Data 学习笔记（二）被管理对象模型
===

## 三、被管理对象模型

CoreData的大多数功能依赖于你创建的用于描述实体、实体属性以及实体间关系的Schema。Schema被表述为一个[被管理对象模型（NSManagedObjectModel）](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObjectModel_Class/)实例对象。通常，这个实例对象越丰富，CoreData对你应用程序的支持越好。

### 3.1 被管理对象模型特点

被管理对象模型有如下特点：

* 被管理对象模型是一个`NSManagedObjectModel`类的实例。
* 它描述你在应用中使用的数据实体的Schema。

详情描述如下。

#### 3.1.1 实体（Entities）

一个模型包含[`NSEntityDescription`](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSEntityDescription_Class/)对象，该对象用于描述一个实体的名称，以及在运行时被使用来表达这个类的实体的名称。一个`NSEntityDescription`对象还可能包含[`NSAttributeDescription`](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSAttributeDescription_Class/)以及[`NSRelationshipDescription`](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSRelationshipDescription_Class/)两个对象。数据实体可能还包含由[`NSFetchedPropertyDescription`](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSFetchedPropertyDescription_Class/)类的实例来表达的可提取属性。模型还可能有提取请求模板，由[`NSFetchRequest`](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSFetchRequest_Class/)的实例来表达。

> 注意：
>
> * 在一个模型当中，实体可能被排列在一个继承结构当中，实体也可能是抽象的。

__实体继承__

对象实体间的继承与类对象的继承类似，都是用于将多个实体相同的属性抽象出来放到父实体当中，从而可以减少重复创建、维护相同属性的工作。

> 注意：
>
> * 如果你在代码中创建实体继承关系，你必须使用自顶向下的方向进行创建。也就是说，你不可以先创建一个子实体，然后给这个子实体指定父实体。但是你可以创建一个父实体，然后为这个父实体指定子实体（使用`.setSubentities`方法）。

__抽象实体__

可以指定一个实体为抽象的，也就是说在应用程序中不会创建这个实体的实例。当在应用中，有大量的类有相同的一些属性，我们可以将这些属性抽象出来，组成一个抽象实体，然后让使用这些通用属性的实体去继承这个抽象实体。这样我们就完成了对于代码的复用。

#### 3.1.2 实体性质（Properties）

一个实体的性质一般是指其属性（Attribute）以及与其他实体间的关系，包含提取属性（Fetched Property）。实体的属性不能以任何没有参数的`NSObject`或`NSManagedObject`类成员函数名来命名。以避免混淆。

临时属性是作为模型的一部分来定义的，但是临时属性不会作为实体数据的一部分来持久化到数据存储当中。CoreData追踪你对临时属性进行的更改，所以它们被记录以便回滚操作。

__实体属性__

CoreData原生支持多种属性类型，如字符串、日期、整型。如果你想要使用原生没有的类型作为实体属性的类型，可以使用在[Non-Standard Persistent Attributes](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/)中描述的几种方法。

可以设置属性为`optional`的，这样就可以不为实体的这个属性赋值，也就是说，这个属性是非必须的。

> 注意：
>
> * SQL中的`NULL`与iOS中的`nil`不同，当一个实体的属性值为`NULL`时，其并代表着iOS中的`nil`。如，对于数值型来属性来说，`NULL`就是`NULL`，而不是`0`。

__实体关系__

CoreData支持“1对N”、“1对1”关系。可以指定实体间关系是否为“必填”和“基数”，以及关联实体间的删除方式。关于实体间关系的更多细节，可以参考[Relationships and Fetched Properties](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CoreData/)。

#### 3.1.3 提取模板

使用[`NSFetchRequest`](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSFetchRequest_Class/)类来描述从持久化数据存储中提取数据的请求。可以在一个被管理对象模型中预定义数据提取请求，这样你可以预定义按需对模型进行检索的查询。关于提取模板的更多的细节可以参见[Accessing and Using a Managed Object Model at Runtime](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/Articles/cdUsingMOM.html#//apple_ref/doc/uid/TP40005190-SW7)。

#### 3.1.4 用户信息模板

被管理对象模型实体、属性、实体关系中的许多元素都有一个关联的用户信息字典。你可以把任何你想要的信息以键值对的开工加入到用户信息字典当中。被置于用户信息字典中的常用的信息包括一个数据实体的版本细节和用于被提取属性的查询条件的值。

#### 3.1.5 配置

配置有一个名称以及一个关联的实体集。设置项目可能是部分重叠的，这是因为一个实体可能在一个或多个配置中出现。可以使用`.setEntities:forConfiguration`来编程实现创建配置。或者使用Xcode的数据模型工具来进行设置。

> 注意：
>
> * 通常，当我们需要将不同的实体存到不同的数据中时，我们需要使用配置。
> * 一个持久化存储协调器只能与一个被管理对象模型进行关联，所以默认与一个给定的协调器进行关联的所有存储都得有同样的实体。
> * 出于以上限制，可以创建一个包含有所有想要使用的实体的被管理对象模型，然后就可以被这个模型所包含的实体所组成的各个子集来创建各种配置。
> * 然后就可以创建一个协调器来关联这个模型。这样，当想要存储数据的时候，在协调器中的各个配置将会各个不同的实体的值保存到不同的持久化存储当中。
> * 一定不可以创建跨多个持久化存储的实体关系。

## 四、被管理对象模型的使用

### 4.1 创建和加载一个被管理对象模型

我们可以使用Xcode自带的CoreData模型工具来创建被管理对象，也可以完全是使用编程的方式来实现模型的定制。

#### 4.1.1 编译一个数据模型

使用Xcode工具创建的被管理对象模型有很多在运行时不需要的信息。我们可以使用`momc [source] [destination]`来编译被管理对象模型文件（.xcdatamodel），编译成用于部署的`mom`文件。

#### 4.1.2 加载一个数据模型

如果使用Xcode创建一个使用CoreData的非文档型应用程序，应用程序委托包含了检索数据模型的代码。模型的名称（模型文件的名称）在运行时是不被强制关联的，所以，一旦数据模型被CoreData加载后，文件名就无关仅要了且也不会被使用了。所以，可以使用任意的名称来命名模型文件。

在创建项目的时候，如果勾选了`Use Core Data`，则在`AppDelegate`文件中，将会有如下代码自动生成：

```swift
// MARK: - Core Data stack

lazy var applicationDocumentsDirectory: NSURL = {
    // The directory the application uses to store the Core Data store file. This code uses a directory named "com.46day.CoreDataUsing" in the application's documents Application Support directory.
    let urls = NSFileManager.defaultManager().URLsForDirectory(.DocumentDirectory, inDomains: .UserDomainMask)
    return urls[urls.count-1] as! NSURL
}()

lazy var managedObjectModel: NSManagedObjectModel = {
    // The managed object model for the application. This property is not optional. It is a fatal error for the application not to be able to find and load its model.
    let modelURL = NSBundle.mainBundle().URLForResource("CoreDataUsing", withExtension: "momd")!
    return NSManagedObjectModel(contentsOfURL: modelURL)!
}()

lazy var persistentStoreCoordinator: NSPersistentStoreCoordinator? = {
    // The persistent store coordinator for the application. This implementation creates and return a coordinator, having added the store for the application to it. This property is optional since there are legitimate error conditions that could cause the creation of the store to fail.
    // Create the coordinator and store
    var coordinator: NSPersistentStoreCoordinator? = NSPersistentStoreCoordinator(managedObjectModel: self.managedObjectModel)
    let url = self.applicationDocumentsDirectory.URLByAppendingPathComponent("CoreDataUsing.sqlite")
    var error: NSError? = nil
    var failureReason = "There was an error creating or loading the application's saved data."
    if coordinator!.addPersistentStoreWithType(NSSQLiteStoreType, configuration: nil, URL: url, options: nil, error: &error) == nil {
        coordinator = nil
        // Report any error we got.
        var dict = [String: AnyObject]()
        dict[NSLocalizedDescriptionKey] = "Failed to initialize the application's saved data"
        dict[NSLocalizedFailureReasonErrorKey] = failureReason
        dict[NSUnderlyingErrorKey] = error
        error = NSError(domain: "YOUR_ERROR_DOMAIN", code: 9999, userInfo: dict)
        // Replace this with code to handle the error appropriately.
        // abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
        NSLog("Unresolved error \(error), \(error!.userInfo)")
        abort()
    }
    
    return coordinator
}()

lazy var managedObjectContext: NSManagedObjectContext? = {
    // Returns the managed object context for the application (which is already bound to the persistent store coordinator for the application.) This property is optional since there are legitimate error conditions that could cause the creation of the context to fail.
    let coordinator = self.persistentStoreCoordinator
    if coordinator == nil {
        return nil
    }
    var managedObjectContext = NSManagedObjectContext()
    managedObjectContext.persistentStoreCoordinator = coordinator
    return managedObjectContext
}()

// MARK: - Core Data Saving support

func saveContext () {
    if let moc = self.managedObjectContext {
        var error: NSError? = nil
        if moc.hasChanges && !moc.save(&error) {
            // Replace this implementation with code to handle the error appropriately.
            // abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
            NSLog("Unresolved error \(error), \(error!.userInfo)")
            abort()
        }
    }
}
```

通常情况下，我们不需要去修改这些自动生成的，用于处理被管理数据模型的操作。简要说明如下。

* `applicationDocumentsDirectory` - 标识应用程序存储数据文件的路径
* `managedObjectModel` - 在应用程序中使用的被管理对象模型
* `persistentStoreCoordinator` - 在应用程序中使用的持久化存储协调器
* `managedObjectContext` - 在应用程序中使用的被管理对象上下文
* `saveContext` - 用于CoreData保存持久化数据，我们可以在此方法中添加一些错误处理的操作

如果我们想要在其他类当中使用系统自动生成在`AppDelegate`当中的被管理对象上下文、被管理对象模型等内容，可以使用如下方法来获取一个当前应用的`AppDelegate`的单例对象。

```swift
var delegate = UIApplication.sharedApplication().delegate as? AppDelegate
```

其中`.sharedApplication()`方法就是用来获取当前正在运行的应用程序对象（单例）的。

如果想要自己__手动加载__数据模型文件，可以使用下面两种机制：

* 使用`init?(contentsOfURL url: NSURL)`实例方法从一个指定的URL中加载。
* 使用`mergedModelFromBundles`类方法来从一个制定的Bundle集合中创建一个数据模型。

> 注意：
>
> * 一般来说，在一个应用程序中，使用一个模型文件就足够了。但是，仍然可以通过使用多个URL来加载多个模型文件，然后在使用这些模型实例化协调器前，使用`modelByMergingModels`来合并它们。

#### 4.1.3 如果你的项目包含一个以上的模型，将可能出现的问题

当我们使用`mergedModelFromBundles`来手动合并多个数据模型文件时，有是会因为多个文件的版本等问题而产生一些异常错误。

* 如果仅仅是数据模型文件被重命名，CoreData将会尝试合并现在的以及旧版本的文件，这可能会引起如下错误。

`reason = "'Can't merge models with two different entities named 'EntityName''";`

* 如果创建一个包含来自原始模型中不同实体的模型，CoreData将合并新的和旧的到一起。而如果你已经存储过一些数据，可能会引起如下错误。

`reason = "The model used to open the store is incompatible with the one used to create the store";`

针对以上异常，提出如下的解决的方法：

* 在运行程序之前，清空之前编译的旧的产品文件。
* 使用`init?(contentsOfURL url: NSURL)`替代使用`mergedModelFromBundles`

### 4.2 修改模式（Schema）使得一个模型与旧的存储冲突

因为模型用于描述数据存储的数据结构，所以修改模型的任意一个部分都将修改模式（Schema），从而引起与之前创建的模型的冲突。如果修改了模式，那么就需要将旧的数据迁移到新的数据存储当中，参考（[Core Data Model Versioning and Data Migration Programming Guide](http://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreDataVersioning/Articles/Introduction.html)）。

> 注意：
> 
> 如果想要修改模型，但是还想要保留打开之前创建的旧版本模型的能力，则_必须保留旧版本模型_。CoreData无法打开不兼容的数据模型，所以，如果想要修改数据模型，但是还要保证能够打开已经存在的数据存储，需要做到：
> 
> * 设置模型版本
> * 在编辑模式之前，给当前模型创建一个新的版本
> * 编辑模型的新版本，保持模型的就版本不变。

### 4.3 在运行时访问和使用一个被管理对象模型

可以使用如下方式在运行时得到被管理对象模型，以便使用这个模型。

* 从被管理对象上下文取得被管理对象模型

`let model = self.managedObjectContext?.persistentStoreCoordinator?.managedObjectModel`

* 从被管理对象实体取得被管理对象模型

`let model = managedObject.entity.managedObjectModel`

#### 4.3.1 编程实现提取请求模板

首先，我们需要取得当前使用的数据模型。然后，创建请求以及请求所使用的查询字符串。最后，使用`setFetchRequestTemplate`方法将请求加入到模型型当中。

```swift
let model = self.managedObjectContext?.persistentStoreCoordinator?.managedObjectModel

var requestTemplate = NSFetchRequest(entityName: "User")
var predicateTemplate = NSPredicate(format: "name like aaron", argumentArray: nil)
requestTemplate.predicate = predicateTemplate

model?.setFetchRequestTemplate(requestTemplate, forName: "UserNameLikeAaron")
```

#### 4.3.2 访问提取请求模板

在访问提取请求模板时，传入的参数必须包含在请求模板当中所使用到的所有实体的键值。如果想要测试`NULL`，则需要传入`nil`类型对象。具体参考[Predicate Format String Syntax](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Predicates/)。

```swift
let model = self.managedObjectContext?.persistentStoreCoordinator?.managedObjectModel

var request = model?.fetchRequestTemplateForName("UserNameLikeAaron")
var results = self.managedObjectContext?.executeFetchRequest(request!, error: nil)
```

> 注意：
>
> 如果请求模板的查询条件当中，没有参数的需要，则在访问请求模板时，必须做到：
> 
> * 使用`fetchRequestFromTemplateWithName:substitutionVariables`，然后传入`nil`作为参数。
> * 或者，使用`fetchRequestTemplateForName`，然后`copy`结果。

## 参考

### 官方教程

* [Managed Object Models](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Devpedia-CoreData/)
* [Using a Managed Object Model](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/)
* [Core Data Model Editor Help](https://developer.apple.com/library/etc/redirect/xcode/ios/1048/recipes/xcode_help-core_data_modeling_tool/_index.html#//apple_ref/doc/uid/TP40010379)
* [Core Data Model Versioning and Data Migration Programming Guide](http://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreDataVersioning/Articles/Introduction.html)
* [create a new version of the current model](https://developer.apple.com/library/prerelease/ios/recipes/xcode_help-core_data_modeling_tool/Articles/creating_new_version.html#//apple_ref/doc/uid/TP40010379-CH8)
* [Predicate Format String Syntax](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Predicates/)

### 网摘文章

* [IOS 数据存储之 Core Data详解](http://www.cnblogs.com/jerehedu/p/4607368.html) by [杰瑞教育](http://www.cnblogs.com/jerehedu/)
