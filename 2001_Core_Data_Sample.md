CoreData 学习笔记（十四）应用示例

## 示例

### 使用Xcode创建Entity及属性

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

> 注意：
> 
> * 实体的属性名称，首字母必须小写。我们可以使用骆驼命名法对实体的属性进行命名。

### 实现对数据实体的增删改查操作
