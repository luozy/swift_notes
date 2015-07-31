CoreData 学习笔记 应用示例
===

## 示例

### 使用Xcode创建Entity及属性

1. 创建一个新的项目，命名为“CoreData”，并__勾选__“Use Core Data”
2. 在创建成功的新项目中，找到“CoreData.xcdatamodeld”文件，并点击打开
3. 在主面板“Entities”中，添加一个名为"User"的实体
4. 点击“User”实体，在右侧为实体添加两个“Attribute”，分别为“name”与“birthday”，类型都是“String”型
5. 在“菜单栏”依次点击“Editor” - “Create NSManagedObject Subclass”
6. 点击“Next”
7. 勾选“User”实体
8. 点击“Next”
9. 选择项目目录“CoreData”，在下面的“Group”中，选择项目目录“CoreData”
10. 保存

> 注意：
> 
> * 实体的属性名称，首字母必须小写。我们可以使用骆驼命名法对实体的属性进行命名。
> * 在生成完成Entity子类后，打开子类文件，查看子类的标题上方是否有`@Obj()`标记，如果没有，就加上。如果不加上，在使用上下文对象时，会提示找不到实体类。

### 初始化环境

因为我们在创建项目的时候勾选了使用`Use Core Data`，所以在项目中的AppDelegate.swift文件中，我们可以看到Xcode已经完成对`NSManagedObjectContext`、`NSManagedObjectModel`、`NSPersistentStoreCoordinator`以及用于存储的`.saveContext()`方法。如下所示：

```swift
// MARK: - Core Data stack

lazy var applicationDocumentsDirectory: NSURL = {
    let urls = NSFileManager.defaultManager().URLsForDirectory(.DocumentDirectory, inDomains: .UserDomainMask)
    return urls[urls.count-1] as! NSURL
}()

lazy var managedObjectModel: NSManagedObjectModel = {
    let modelURL = NSBundle.mainBundle().URLForResource("CoreDataUsing", withExtension: "momd")!
    return NSManagedObjectModel(contentsOfURL: modelURL)!
}()

lazy var persistentStoreCoordinator: NSPersistentStoreCoordinator? = {
    var coordinator: NSPersistentStoreCoordinator? = NSPersistentStoreCoordinator(managedObjectModel: self.managedObjectModel)
    let url = self.applicationDocumentsDirectory.URLByAppendingPathComponent("CoreDataUsing.sqlite")
    var error: NSError? = nil
    var failureReason = "There was an error creating or loading the application's saved data."
    if coordinator!.addPersistentStoreWithType(NSSQLiteStoreType, configuration: nil, URL: url, options: nil, error: &error) == nil {
        coordinator = nil
        var dict = [String: AnyObject]()
        dict[NSLocalizedDescriptionKey] = "Failed to initialize the application's saved data"
        dict[NSLocalizedFailureReasonErrorKey] = failureReason
        dict[NSUnderlyingErrorKey] = error
        error = NSError(domain: "YOUR_ERROR_DOMAIN", code: 9999, userInfo: dict)
        NSLog("Unresolved error \(error), \(error!.userInfo)")
        abort()
    }
    
    return coordinator
}()

lazy var managedObjectContext: NSManagedObjectContext? = {
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
            NSLog("Unresolved error \(error), \(error!.userInfo)")
            abort()
        }
    }
}
```

> 注意：
> 
> * 如果在创建项目的时候没有勾选`Use Core Data`，后续可以使用添加`Data Model`的方式添加。但是，如果使用手工的方式添加，那么就需要手动的实现在`AppDelegate`中的上述代码。

### 实现对数据实体的增删改查操作

#### 自动生成的数据实体对象



#### 插入

要想插入数据到持久话存储当中，根据CoreData的框架特性，我们首先需要取得`NSManagedObjectContext`对象，然后再创建想要插入的`NSManagedObject`对象，并对这个对象的属性进行赋值操作，最后通过保存上下文的方式，完成持久化操作。

打开ViewController.swift文件，然后添加下面的代码到`.viewDidLoad()`方法当中。

```swift
override func viewDidLoad() {
	super.viewDidLoad()

	var delegate = UIApplication.sharedApplication().delegate as? AppDelegate
	var context = delegate?.managedObjectContext

	var user = NSEntityDescription.insertNewObjectForEntityForName("User", inManagedObjectContext: context!) as! User
	user.name = "Aaron"

	delegate!.saveContext()
}
```

下面的代码首先取得当前运行的应用程序的`AppDelegate`，然后取得由Xcode自动生成的上下文对象。

```swift
var delegate = UIApplication.sharedApplication().delegate as? AppDelegate
var context = delegate?.managedObjectContext
```

下面的代码用于新建一个`NSManagedObject`实体实例，并为这个实体的属性赋值。

```swift
var user = NSEntityDescription.insertNewObjectForEntityForName("User", inManagedObjectContext: context!) as! User
user.name = "Aaron"
```

当我们完成实体实例的配置后，需要保存改变，进行持久化操作。我们可以使用下面的代码。

```swift
delegate!.saveContext()
```

#### 查询

所有对实体的操作都需要在当前应用程序的持久化上下文环境当中，这样才能够利用持久化上下文进行数据实体的持久话操作。

下面是从持久化数据存储提取全部“User”实体（表）的数据的方法。

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    // Do any additional setup after loading the view, typically from a nib.
    
    var delegate = UIApplication.sharedApplication().delegate as? AppDelegate
    var context = delegate?.managedObjectContext
    
    var request = NSFetchRequest(entityName: "User")
    var results = context?.executeFetchRequest(request, error: nil) as! [User]
    
    for user in results {
        println("\(user.name)")
    }
}
```

在获取到持久化上下文之后，想要从持久化存储中提取数据，首先需要创建一个提取数据请求。

```swift
var request = NSFetchRequest(entityName: "User")
```

然后在上下文中执行提取数据请求，然后将请求后的结果赋值给结果对象。

```swift
var results = context?.executeFetchRequest(request, error: nil) as! [User]
```

在取得提取结果之后，可以使用遍历等的方式，提取需要的数据结果。

```swift
for user in results {
    println("\(user.name)")
}
```

#### 更新

要想更新数据，首先需要找到我们需要更新的数据项目，我们可以为数据提取请求配置查询条件（谓词，Predicate），然后执行请求取得需要更新的数据。然后直接修改数据对象的属性值。最后，调用应用程序委托对象，进行持久化上下文操作。

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    // Do any additional setup after loading the view, typically from a nib.
    
    var delegate = UIApplication.sharedApplication().delegate as? AppDelegate
    var context = delegate?.managedObjectContext
    var request = NSFetchRequest(entityName: "User")
    
    var predicate = NSPredicate(format: "%K like %@", argumentArray: ["name", "Aaron"])
    request.predicate = predicate
    
    var aarons = context?.executeFetchRequest(request, error: nil) as! [User]

    for user1 in aarons {
        user1.name = "cong"
    }
    
    delegate?.saveContext()
}
```

#### 删除

与更新数据类似，想要删除数据，首先要找到对应的数据，然后使用持久话上下文的`.deleteObject(...)`操作对制定的数据对象执行删除操作，最后调用持久化存储操作。

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    // Do any additional setup after loading the view, typically from a nib.
    
    var delegate = UIApplication.sharedApplication().delegate as? AppDelegate
    var context = delegate?.managedObjectContext
    
    var predicate = NSPredicate(format: "%K like %@", argumentArray: ["name", "cong"])
    request.predicate = predicate
    
    var aarons = context?.executeFetchRequest(request, error: nil) as! [User]
    
    for user1 in aarons {
        context?.deleteObject(user1)
    }
    
    delegate?.saveContext()
}
```

## 参考

* [IOS 数据存储之 Core Data详解](http://www.cnblogs.com/jerehedu/p/4607368.html) by [杰瑞教育](http://www.cnblogs.com/jerehedu/)