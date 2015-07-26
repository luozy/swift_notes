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

#### 4.1.1 编译一个数据模型

#### 4.1.2 加载一个数据模型

#### 4.1.3 如果你的项目包含一个以上的模型，将可能出现的问题

### 4.2 发型模式（Schema）使得一个模型与旧的存储冲突

### 4.3 在运行时访问和使用一个被管理对象模型

#### 4.3.1 编程实现提取请求模板

#### 4.3.2 访问提取请求模板

### 4.4 本地化一个被管理对象模型

#### 4.4.1 字符串文件

#### 4.4.2 编程实现设置一个本地化字典

## 参考

* [Managed Object Models](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Devpedia-CoreData/)
* [Using a Managed Object Model](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/)
