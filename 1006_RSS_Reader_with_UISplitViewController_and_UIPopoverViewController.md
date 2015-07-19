使用UISplitViewController与UIPopoverViewController构建一个RSS阅读器
===

[原文](http://www.appcoda.com/building-rss-reader-using-uisplitviewcontroller-uipopoverviewcontroller/)

Just a few weeks ago Apple introduced the new iPhone devices, the iPhone 6 and iPhone 6 Plus. As we all saw, the screen sizes are bigger than all the older iPhone screens, meaning that any new applications must be build in that way so they work on all devices. Even more, if we consider the iPad screens, then the number of screen sizes that any application should adapt to becomes even greater. From a first point of view, that may seem a big hassle for developers, however that’s not true at all.

In iOS 8, Apple introduces the so-called adaptive user interface or adaptive layout. With that new philosophy, application interfaces can be adjusted and configured pretty fast for any kind of display. With adaptive UI, some existing concepts have become deprecated, such as various delegate methods regarding the device orientation. On the other hand, new concepts are provided for easier and more general handling of the interface, and developers just need to get used to working with them. Anyway, in this tutorial I’m not going to discuss in depth about the adaptive UI, but as we are going to be in align with it, there are a few things that we will see in a bit more detail later on.

![](imgs/1006_Demo1.jpg)

Further than all the above, it’s well known that iOS 8 not only contains new features, but great improvements to existing ones as well. Of course, view controllers could not just stay out of those improvements, and that’s more or less what we are going to talk about. Specifically, we are going to focus on two special view controllers, the `UISplitViewController` and the `UIPopoverViewController`.

直到iOS的第七个版本（iOS7），这两个视图控制器仅可以用在iPad引用当中。现在，新的iOS SDK允许我们在iPhone引用中使用他们，这就是我们在这里将要做的事情。我的目标是呈现给你怎样才能添加一个split视图控制器和一个popover视图控制器到iPhone应用，以及他们是怎样能够现实在所有可用的屏幕尺寸当中。它也许在不同的屏幕尺寸中看起来有一点不同或怪异，但是一旦你知道方法，你就可以自己去在不同的应用中按照你想要的效果去使用它们。

除此之外，我们将要写Swift语言的代码。非常明确的是，就像在我上一篇教程中做的，我们还将看到多种技术是如何使用Swift来实现的，并且我相信，通过我们将实验的视图控制器，你也将从我们作品的其余部分获得额外的收益。

最后，给出一个建议，建议你查看[WWDC 2014 Session视频](https://developer.apple.com/videos/wwdc/2014/)#214以及#216关于新的自适应布局概念、split视图控制器以及popover视图控制器，如果你还没有看过的话。请花些时间看一下他们，然后你将更容易理解下面我们将讨论的内容。不再罗嗦，下面就让我们开始吧！

#### 示例应用概览

在我的一些旧的教程中，我使用iOS SDK的NSXMLParser类来下载以及解析来自网络的数据。这果，我们将再次使用这个类，但是这次我们将看到相关的SML解析代码使用Swift是如何写的（我认为这对于那些想要使用Swift处理XML的人是相当有用的）。我们将下载并解析的数据源将来自我们自己的RSS源，Appcoda的RSS订阅源。

对于这个教程的示例应用，我们将仅仅为iPhone设备创建一个简单的项目。这个应用仅仅包含一个split视图控制器，我们将编码配置这个视图控制器，以便它适于工作在iPhone上。就像你知道的，一个split视图控制器由两个其他的视图控制器组成（子视图控制器），一个主要的和一个将要的视图控制器。我们的主要的视图控制器将有一个表视图（实际上将是一个表视图控制器），在这个表视图控制器中我们将罗列出所有的教程标题。

第二个视图控制器将包含一个`Web View`以及一个工具条。当在主视图上一个条目被点击时，教程的内容将被加载并显示在`web view`当中（我们将提供它合适的链接）。上工具条上，将存在两个`bar button item`（一个额外flexible space bar button item）：第一个将触发split视图的显示访求，意味着它将显示或隐藏主视图控制器。在iOS8中，`UISplitViewController`类提供一个方法，这个方法将返回一个可以用于隐藏主视图控制器的`bar button item`。为了让主视图控制器再次可见，我们将使用一个我们自己定义的按键来替换默认的`button item`。除此之外，我们将这样实现这个应用，让这两个按钮仅仅显示在适用的设备方向上。当然，自定义的按钮将使教程清单可见，不能以及将不与我们为了隐藏主视图控制器的SDK预定义的按钮同时显示。最后，工具栏上的第二个按钮将在一个popover视图控制器中显示要显示的教程的发布日期。

如果所有这些看起来比较混乱，接下来的屏幕截图将让其看来来清晰：

![](imgs/1006_Demo2.png)

![](imgs/1006_Demo3.png)

![](imgs/1006_Demo4.png)

#### 创建应用并添加一些文件

让我们通过创建一个新的Xcode项目并且选择`Single View Application`模板。你可以设置`SplitAndPopover`值作为这个应用的名称（在这里我使用这个名称），然后确认选择的编程语言是Swift，选择的设备是iPhone。

![](imgs/1006_Demo5.png)

为创建的项目选择一个目录来存储它。

现在，在开始之前，让我们创建下面的类：

1. 一个`UITableViewController`，我们将使用它作为主视图控制器。
2. 一个`UIViewController`，我们将使用它作为split视图控制器的将要视图控制器。
3. 一个`UIViewController`类，我们将使用它作为定制窗口视图控制器。下面有一个小节专门用来说明它，所以如果你不清楚这个是用来做什么的也没有问题。
4. 一个`NSObject`类，我们将在这个类里面实现解析XML数据的函数。

在教程的最后一节，我们将创建另一个有关popover控制器的类，但是出于时间关系，我们聚焦在之前提到的类上面。

为了创建上面说的文件，下面是你需要做的事情的简要清单。注意，每创建每一个文件时，都应该做下面的这些步骤：

1. 在Xcode中，依次点击：File > New > File...
2. 创建一个Cocoa Touch类
3. 设置每一个类的Subclass属性
4. 设置Class Name属性
5. 创建每一个类　

接下来说明每一个用例：

用于主视图控制口器的表视图控制器，命名为`TopicsTableViewController`。

![](imgs/1006_Demo6.png)

为次要视图控制器命名为`TutorialViewController`。

![](imgs/1006_Demo7.png)

容器视图控制器类命名为`ContainerViewController`。

![](imgs/1006_Demo8.png)

`NSObject`类命名为`XMLParser`。

![](imgs/1006_Demo9.png)

#### 设计UI

现在，所有我们需要的的类文件已经准备好了，让我们设置app的界面。打开`Main.storyboard`文件，然后等待`Interface Builder`出现。第一步，从`Object Library`添加一个`Split View Controller`到画板，要做的是找到它并将它拖到画板里面。

![](imgs/1006_Demo10.png)

当你拖动完成后，将箭头从默认的视图控制器移动到split视图控制器最左面的视图：

![](imgs/1006_Demo11.png)

然后，你可以删除默认的视图近面器，然后从`Project Navigator`中删除`ViewController.swift`。

让我们按照需要设置每一个视图控制器。选择`Table View Controller`（导航视图控制器指向的），然后通过打开`Utilities`然后打开`Identity Inspector`设置一个自定义类。在`Custom Class`节，在`Class`字段，设置`TopicsTableViewController`值。同时，替换导航项目的标题为"Appcoda Tutorials"。

![](imgs/1006_Demo12.png)

然后，选择列表视图单元原型，然后在`Attributes Inspector`中选择`Basic`样式。同时，设置`idCell`作为它的标识值。下一步，打开`Size Inspector`，并设置`Row Height`为80.

通过设置表视图单元为`Basic`样式，一个标签显示在表视图中。选择这个标签，设置字号为14pt，以及`Lines`为3.

下面是应该看到的：

![](imgs/1006_Demo13.png)

让我们继续配置split视图控制器的次要视图。在开始之前，设置它的自定义类为`TutorialViewController`值。暂时不要急于推进，在这个用例中，我们必须指定一个`Storyboard ID`值，在代码中，稍后我们将需要它。对于此处，指定为`idTutorialViewController`。

![](imgs/1006_Demo14.png)

我们现在将添加一些子视图。首先，从`Object Library`中取得一个`UILabel`，然后添加它到场景中。设置`No Tutorial`为它的文本。确认移动这个标签到场景的中间。然后点击在Interface Builder右下方的Align按钮，设置`Horizontal center in container`以及`Vertical center in container`约束。

![](imgs/1006_Demo15.png)

接下来，从`Object Library`拖拽一个`UIToolbar`对象进来。放置它在场景的底部，并重命名已经存在的按钮内容为“Publish Date”。同时，取得一个`Flexible Space Bar Button`并添加它到工具栏中已经存在的按钮的左侧。我们将为工具栏设置约束。

现在，从控件库中拿出一个`UIWebView`，并且添加它到场景当中。确认覆盖全部的空间，除了工具栏。这个`Web View`将覆盖之前的标签。

现在让我们为工具栏以及web视图设置约束。选择工具栏，然后点击在IB下面的`Pin`工具栏。启用上、下、左、右以及高度约束。

然后选择`Web View`，并再次点击`Pin`按钮。启用上、下、左、右约束，工添加它们。下面展示`Tutorial View Controller`应该的样子：

![](imgs/1006_Demo16.png)

最后，我们需要为Web View创建三个`IBOutlet`属性，工具栏和工具栏按钮项目。同时，我们将需要给工具栏按钮项目提供一个`IBAction`方法。打开`TutorialViewController.swift`文件，以及添加下面的`IBoutlet`属性：

```swift
class TutorialViewController: UIViewController {
 
    @IBOutlet weak var webview: UIWebView!
 
    @IBOutlet weak var toolbar: UIToolbar!
 
    @IBOutlet weak var pubDateButtonItem: UIBarButtonItem!
    ...
}
```

接下来，添加`IBAction`方法：

```swift
@IBAction func showPublishDate(sender: AnyObject) {
}
```

我们将在教程最后一部分使用这个方法，当我们处理popover控制器时。

现在，返回Interface Builder然后进行属性连接。选择`Tutorial View Controlelr`场景，然后再`Utilities`面板找到`Connections Inspector`。选择每一个`IBOutlet`属性，然后连接它到合适的子视图。不要忘记连接`IBAction`方法到工具栏按钮。

![](imgs/1006_Demo17.png)

我们需要在Interface Builder要做的大部分工作现在已经做完了。稍后我们将会返回来，为popover控制器添加一个新的场景，但是，之前，让我们写一些代码！

#### 获取并解析XML数据

在这个教程中，我们将要处理的低一个事情，是下载并解析我们开发的示例应用的样本数据。就像我在应用概览一节中所说的，我们的数据源将是Appcoda站点的RSS订阅源，我们的职责是解析订阅源然后获取我们需要的数据部分。

就像你知道的当使用`NSXMLParser`类解析XML数据时，需要进行三个步骤：

1. 初始化一个NSXMLParser对象
2. 指定URL源并开始解析
3. 使用NSXMLParser委托方法以处理解析数据

让我们一步一步来。初始化，打开XMLParser.swift文件，然后引入NSXMLParserDelegate协议。这样做是为了稍后处理数据。如下面所示：

```
class XMLParser: NSObject, NSXMLParserDelegate
```

> 类将实现的任何协议都应该在父类后面进行声明。

现在，让我们实现一个新的函数，用于执行三件事情：我们将初始化一个解析对象，我们将设置XMLParser类作为它的代理，最后我们将开始解析。将这些转化为代码，将类似如下所示：

```swift
func startParsingWithContentsOfURL(rssURL: NSURL) {
        let parser = NSXMLParser(contentsOfURL: rssURL)
        parser.delegate = self
        parser.parse()
}
```

注意，数据源URL将作为函数的参数。

现在，我之前列出的三个步骤中的两个已经实现了。然而，这是容易的部分。下面是重要且难一些的部分，这里我们必须实现所有的NSXMLParserDelegate协议委托方法的需求，然后应用合适的逻辑以便我们可以从解析结果中仅抽取我们需要的数据。

在我们查看返回XML数据的示例之前，让我们声明一些属性。在类的顶部，添加下面的属性：

```
class XMLParser: NSObject, NSXMLParserDelegate {
 
    var arrParsedData = [Dictionary<String, String>]()
 
    var currentDataDictionary = Dictionary<String, String>()
 
    var currentElement = ""
 
    var foundCharacters = ""
 
    ...
 
}
```

下面是每一个属性的目的：

* arrParsedData: This is an array that will contain just the parsed data we care about. Note that each object of this array is going to be a Dictionary of which both the key and the value will be String objects. We’ll see an example of the dictionary contents in a while.
* currentDataDictionary: This dictionary object will be used to temporarily store the parsed data of interest. After we have all the needed data of a parsed tutorial, we’ll add it to the previously described array.
currentElement: This is a String object in which the name of the currently parsed element will be stored. We’ll need it to determine if every found element is of interest or not.
* foundCharacters: This String object will store the value of a node (an element) that we care about. When the end of the element is found, then its contents will be added to the currentDataDictionary dictionary and it will become empty again.

Now that you are aware of the purpose of the above properties, let’s see a sample of the XML data. The following extract displays all the returned data for one tutorial only:

```swift
<item>
        <title>Understanding Self Sizing Cells and Dynamic Type in iOS 8</title>
        <link>http://feedproxy.google.com/~r/appcoda/~3/NBLtJtdCP7Q/</link>
        <comments>http://www.appcoda.com/self-sizing-cells/#comments</comments>
        <pubDate>Thu, 04 Sep 2014 17:09:31 +0000</pubDate>
        <dc:creator><![CDATA[Simon Ng]]></dc:creator>
                <category><![CDATA[Beginner]]></category>
        <category><![CDATA[Course]]></category>
        <category><![CDATA[Swift]]></category>
        <category><![CDATA[Tutorials]]></category>
        <category><![CDATA[dynamic type]]></category>
        <category><![CDATA[self sizing cell]]></category>
        <category><![CDATA[swift]]></category>
        <category><![CDATA[UITableView]]></category>
 
        <guid isPermaLink="false">http://www.appcoda.com/?p=4013</guid>
 
        <wfw:commentRss>http://www.appcoda.com/self-sizing-cells/feed/</wfw:commentRss>
        <slash:comments>9</slash:comments>
        <description>In iOS 8, Apple introduces a new feature for UITableView known as Self Sizing Cells. To me, this is seriously one of the most exciting features for the new SDK. Prior to iOS 8, if you want to display dynamic content in table view with variable height, you would need to calculate the row height [&amp;amp;#8230;]This article, Understanding Self Sizing Cells and Dynamic Type in iOS 8, was originally published at AppCoda.&amp;lt;br/&amp;gt;&amp;lt;br/&amp;gt;
[[ This is a content summary only. Visit my website for full links, other content, and more! ]]&amp;lt;img src="http://feeds.feedburner.com/~r/appcoda/~4/NBLtJtdCP7Q" height="1" width="1"/&amp;gt;
        </description>
    <feedburner:origLink>http://www.appcoda.com/self-sizing-cells/</feedburner:origLink>
</item>
```

The nodes we are interested in, are:

1. title
2. link
3. pubDate

Using the values of the above XML sample, here’s what the currentDataDictionary would contain after having parsed the data of the specific tutorial:

```
[link: http://feedproxy.google.com/~r/appcoda/~3/NBLtJtdCP7Q/, pubDate: Thu, 04 Sep 2014 17:09:31 +0000, title: Understanding Self Sizing Cells and Dynamic Type in iOS 8]
```

有了上述的一切，我们现在能够处理所有需要的呃委托方法的实现。让我们从一个新元素被发现的情况开始。我们唯一应该做的，是给currentElement属性指定元素名称：

```swift
func parser(parser: NSXMLParser, didStartElement elementName: String!, namespaceURI: String!, qualifiedName qName: String!, attributes attributeDict: [NSObject : AnyObject]) {
 
        currentElement = elementName
}
```

现在，我们必须存储每个节点的实时内容（实际值）到foundCharacters属性：

```swift
func parser(parser: NSXMLParser, foundCharacters string: String!) {
        if (currentElement == "title" && currentElement != "Appcoda") || currentElement == "link" || currentElement == "pubDate"{
            foundCharacters += string
        }        
}
```

如果你仔细观察，你会发现在第一个条件中我们不只是检查当前元素是否是标题，还检查它的值是否是“Appcoda”字符串。我这样做的目的，返回的XML数据在XML开始部分包含名称为“title”的元素，与其他“教程”不同，它只有“appcoda”值。不管怎样，这是一个与特定的数据相关的行为。

总结到此，我们已经设法暂时存储每个解析的元素名，并基于此，以保持或不保持节点的值。接下来，我们必须添加foundCharacters值给currentDataDictionary。此外，当所有需要的数据已被添加到词典，我们需要把这个词典给arrParsedData数组。让我们看看所有的代码：

```swift
func parser(parser: NSXMLParser, didEndElement elementName: String!, namespaceURI: String!, qualifiedName qName: String!) {
        if !foundCharacters.isEmpty {
 
            if elementName == "link"{
                foundCharacters = (foundCharacters as NSString).substringFromIndex(3)
            }
 
            currentDataDictionary[currentElement] = foundCharacters
 
            foundCharacters = ""
 
            if currentElement == "pubDate" {
                arrParsedData.append(currentDataDictionary)
            }
        }
}
```

We know if there is data to be stored to the dictionary simply by checking if the foundCharacters string is empty or not. Notice that there’s one more “tweak” here:

```swift
foundCharacters = (foundCharacters as NSString).substringFromIndex(3)
```

I do this also on purpose, because the characters “\n\t\t” are added to the beginning of the string when parsing the link element. The above command actually gets a substring from the foundCharacters string, starting at index 3.

You see that after having added the found value to the dictionary, we empty the foundCharacters string again. The pubDate element is the last one of interest for each tutorial topic (in the order the data is parsed), so when that happens we are able to add the dictionary to the array.

Our next move regards the action that we should take when the parsing is finished. In that case, we only need to inform the caller class that this has happened, and an easy way to do it is to use the delegation pattern (for more information about it, you could also check my previous tutorial, where I explain the steps involved in the delegation pattern in Swift in a bit more detail). So, let’s work on that.

First of all, we need to create a new protocol. Go to the top of the file, before the XMLParser class implementation. In there, add the following code segment:

```swift
@objc protocol XMLParserDelegate{
    func parsingWasFinished()
}
```

We declared just one delegate method, named parsingWasFinished. No parameters are needed, and it’s a void function.

Next, let’s declare a delegate property. Go to the body of the XMLParser class where you declared and initialized all the previous properties, and add the next one:

```swift
class XMLParser: NSObject, NSXMLParserDelegate {
 
    ...
 
    var delegate : XMLParserDelegate?
 
    ...
 
}
```

Finally, we only have to call our delegate method that will inform the caller class about the end of the parsing. Here it is:

```swift
func parserDidEndDocument(parser: NSXMLParser) {
        delegate?.parsingWasFinished()
}
```

Our class is almost ready now. What we haven’t done yet, is to implement the delegate methods that will handle any errors that may occur. To keep things simple, we’ll just log these error messages:

```swift
func parser(parser: NSXMLParser, parseErrorOccurred parseError: NSError!) {
    println(parseError.description)
}
 
 
func parser(parser: NSXMLParser, validationErrorOccurred validationError: NSError!) {
    println(validationError.description)
}
```

Notice that we didn’t implement the parserDidStartDocument method, simply because we didn’t need to do something in it.

#### 使用已解析的数据

The mechanism to download and parse data for our demo application is ready, and it would be a good idea to use that data before we move to any other important aspect of the tutorial. As you saw previously, all the data of interest is stored to the arrParsedData array, and that data we are going to populate in the `TopicsTableViewController` class for starters. So, open the respective file in Xcode (the * TopicsTableViewController.swift* file), and as a first step adopt the XMLParserDelegate protocol (the one we implemented) by declaring it to the class header:

```swift
class TopicsTableViewController: UITableViewController, XMLParserDelegate {
```

By doing the above, we’ll be able to implement the parsingWasFinished delegate method, and knowing that way when the parsing is over so we can reload the table view. Actually, as we are speaking about that, why don’t we implement it now?

```swift
func parsingWasFinished() {
        self.tableView.reloadData()
}
```

Now, declare at some point (preferably at the beginning of the class) the next property:

```swift
class TopicsTableViewController: UITableViewController, XMLParserDelegate {
 
    var xmlParser : XMLParser!
 
}
```

Using the above, we’ll manage to start the parsing process in the XMLParser class, and to access the arrParsedData array later to get all the data we want. Now, we must initialize it, specify the feed URL and parse. All that will take place in the viewDidLoad method as follows:

```swift
override func viewDidLoad() {
        super.viewDidLoad()
 
        let url = NSURL(string: "http://feeds.feedburner.com/appcoda")
        xmlParser = XMLParser()
        xmlParser.delegate = self
        xmlParser.startParsingWithContentsOfURL(url)
 
}
```

Here, you can see how easy it is to put in action our custom – made class. Now, we need to populate the downloaded data, and that’s something that is triggered by the parsingWasFinished delegate method that we have already implemented. However, there is still some little work ahead of us, as we need to implement some tableview methods. As you may have noticed, several such methods already exist to the class, they are just commented out. So, we will uncomment the proper ones and we’ll add any necessary code. Let’s get started by defining the number of the tableview’s sections:

```swift
override func numberOfSectionsInTableView(tableView: UITableView) -> Int {
        return 1
}
```

Next, let’s specify the number of the rows in this section, which will be no other than the total elements in the arrParsedData array:

```swift
override func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
 
        return xmlParser.arrParsedData.count
 
}
```

One important task we also need to do, is to specify the contents of each displayed cell. If you remember, in the cell prototype existing in the Interface Builder, we set its identifier value to idCell. Now we are about to use this identifier for dequeueing each cell, and then use the arrParsedData array to get the data. I say no more, as everything is too easy:

```swift
override func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCellWithIdentifier("idCell", forIndexPath: indexPath) as UITableViewCell
 
        let currentDictionary = xmlParser.arrParsedData[indexPath.row] as Dictionary<String, String>
 
        cell.textLabel?.text = currentDictionary["title"]
 
        return cell
}
```

And one last easy thing:

```swift
override func tableView(tableView: UITableView, heightForRowAtIndexPath indexPath: NSIndexPath) -> CGFloat {
        return 80
}
```

All the above is the minimum required implementation we needed to do for showing the tutorial topics in the primary view controller of the split VC.

Now, in order to display the proper tutorial in the integrated web view of the second view controller, we must pass the link of the selected tutorial to the TutorialViewController instance. To do so, we must begin by declaring a property to which we’ll assign that link. Open the TutorialViewController.swift file, and at the beginning of the class write:

```swift
class TutorialViewController: UIViewController {
 
    var tutorialURL : NSURL!
 
    ...
 
}
```

Next, return to the TopicsTableViewController.swift file, locate this commented method: override func tableView(tableView: UITableView, didSelectRowAtIndexPath indexPath: NSIndexPath) and uncomment it. In it, we’ll get the link matching to the selected row, then we’ll instantiate an object of the TutorialViewController class and we will pass the link value. Finally, we’ll display the second view controller not by performing a segue, but by using a new method in iOS 8 that displays contents to a detail view controller, just like here. Let’s see the implementation:

```swift
override func tableView(tableView: UITableView, didSelectRowAtIndexPath indexPath: NSIndexPath) {
        let dictionary = xmlParser.arrParsedData[indexPath.row] as Dictionary<String, String>
        let tutorialLink = dictionary["link"]
 
        let tutorialViewController = UIStoryboard(name: "Main", bundle: nil).instantiateViewControllerWithIdentifier("idTutorialViewController") as TutorialViewController
 
        tutorialViewController.tutorialURL = NSURL(string: tutorialLink!)
 
        showDetailViewController(tutorialViewController, sender: self)
 
}
```

In the above code segment you can see what the view controller identifier we set in the Interface Builder is useful for.

Now, open the TutorialViewController.swift file. Upon the application launch we want the web view and the toolbar to be hidden, so go to the viewDidLoad method and add the next two commands:

```swift
override func viewDidLoad() {
        super.viewDidLoad()
 
        // Do any additional setup after loading the view.
 
        webview.hidden = true
        toolbar.hidden = true
 
}
```

After that, we’ll override the viewDidLoad method, where we’ll load and display the web view contents, if a tutorial URL value has been set:

```swift
override func viewDidAppear(animated: Bool) {
        super.viewDidAppear(animated)
 
        if tutorialURL != nil {
            let request : NSURLRequest = NSURLRequest(URL: tutorialURL)
            webview.loadRequest(request)
 
            if webview.hidden {
                webview.hidden = false
                toolbar.hidden = false
            }
 
    }
}
```

As you see, once we start loading the URL contents we make the web view and the toolbar visible again (if they’re hidden).

All of the above consist of the code we need in both the TopicsTableViewController and the TutorialViewController classes in order to list all the parsed tutorial data and to display the proper one, after having selected a topic. With that mechanism ready and working, let’s focus on the split view controller and other new technologies in iOS 8.

#### Size类与Trait收集器

Before we begin the actual work with the split view controller and the rest of the stuff we are about to see, it’s necessary to talk about two new important concepts regarding the adaptive layout. These are the Size Classes and the Trait Collections, and without having the minimum required knowledge about them at least, it’s not possible to carry on. Of course, I recommend you to find more information in the Apple documentation or the respective WWDC videos in addition to what you’ll read here. However, if you are aware of these meanings and you feel comfortable with them, then you may skip this section, otherwise please be patient enough and keep reading.

那么，让我们开始使用`size`类型。`Size`类是在iOS8中新出现的，它实际上描述了设备的屏幕大小。每坐标轴（水平、垂直），有两个size类：`Regular`（常规）和`Compact`（紧凑）。`Regular`尺寸类一般用于iPad设备，`Compact`尺寸类一般用于iPhone设备。完全非官方和非技术的说，一种说法是，`Regular`尺寸类指示出对于特定的内容在一个给定的放向上有“足够的空间”，`Compact`标识完全相反的情况。当然，“足够的空间”一词是相对的，总是取决与与你想要展示的内容，在此教程的最后，你将会明确的找到这些。因此，考虑到在每一个坐标轴上都有两个尺寸类，一共有四个尺寸类：

1. Horizontal regular —— 水平常规
2. Horizontal compact —— 水平紧凑
3. Vertical regular —— 垂直常规
4. Vertical compact —— 垂直紧凑

上面尺寸类型的混合告诉我们在不同方向上的屏幕尺寸：

1. Regular (H) x Regular (V) = iPad在所有方向上的尺寸
2. Compact (H) x Regular (V) = iPhone在竖向上的尺寸
3. Regular (H) x Compact (V) = iPhone 6 Plus在横向上的尺寸
4. Compact (H) x Compact (V) = iPhone在横向上的尺寸

You can see the size classes in action, if you simply go to Xcode, and open the Interface Builder by clicking on the Main.storyboard file. At the bottom of the main window area, there’s a button writing wAny hAny. If you click on it, a new small window will appear, where you can specify any desired size class combination. At the same time, Xcode informs you about the target device of the currently set combination. The Any value means that the interface configuration will work to any device, no matter of the screen size. An extra benefit from this is that no different .storyboard files are needed for each device type when creating universal applications.

![](imgs/1006_Demo18.png)

So, as you can understand, we are no longer talking about 3.5 or 4 inch screens or anything else like that when designing the interface, we’re just talking about size classes. If, for example, you want to describe an iPhone screen, then you simply use the proper size class combination, depending on the orientation.

Let’s say a few things about the trait collections now, but first let’s define what a trait is. Well, a trait is actually a property that describes a certain aspect of a screen. That means that a trait collection is a set of properties describing a specific device, and it contains the exact following traits:

* Horizontal size class
* Vertical size class
* Device idiom
* Display scale

As you notice, the size classes are considered to be traits. Looking at a simple example, the next trait collection:

* Compact horizontal size class
* Regular vertical size class
* Device idiom: iPhone
* Display scale: 2.0
* … describes an iPhone in portrait orientation.

The discussion about traits doesn’t stop here, on the contrary there is a lot more study one should do about it. However, the traits and trait collections are important for us in this tutorial just for one reason: Every view controller inherits the trait collection from its parent, and that inheritance is moving towards up in the hierarchy until the window object of the app. Our goal in the sample app we’re creating is to manage to override the inherited trait collection and to modify the size classes in order to properly display the split view controller when the device orientation gets changed. The new SDK provides some really useful methods do to so, and once you get the meaning of the trait collections you’ll see that overriding them is a super-easy task and in many cases the appropriate way to implement your app.

#### 自定义容器视图控制器

Designing a user interface it’s not a simple thing, as that’s the main way for users to communicate with your application. If the user interface is poorly designed the users will probably never use your app more than a few times, no matter if the features it incorporates are the best ones existing out there. And when saying about UI design, I don’t only mean the graphics or the colors of it, but the structural elements that compose the app. 所以，做正确的决定并选择合适的视图控制器与子视图在开发阶段是对于应用程序的生命周期必不可少的。现在更具体地说，我们大家都很经常使用专用的视图控制器，它可以作为其他的、正常的视图控制器的容器来工作。导航或标签栏视图控制器是由iOS SDK提供的典型的例子。

There is a whole theory behind container view controllers that you should really read at some point here. Besides those pre-made and provided by the SDK container view controllers, developers can also create their own custom container VCs, for which you can also read in the previous given link. There are specific steps that should be followed every time that you implement one, and we’ll see them here pretty soon, but first, allow me to tell you why we need to create a custom container view controller in this application.

As you’ll see yourself while we’re moving forward in this sample project, we won’t be able to properly display the split view controller unless we override the trait collection of its parent view controller. However, the parent of the split view controller is the application window itself (remember the design in the Interface Builder), and we can’t override its trait collections. 因此，我们需要一个自定义容器视图控制器，该控制器将在窗口和拆分视图控制器中间进行工作。将拆分视图控制器嵌入到容器中，容器将成为窗口的根视图控制器。At the end, the trait collection of that container VC is the one that we’ll override.

Our work with the custom container view controller won’t be limited in this part of the tutorial only. Our initial implementation will take place here, however we’ll do new additions and modifications later on, when we’ll dive in the details of the split view controller.

Open the ContainerViewController.swift file, and right at the beginning of the class write this:

```swift
class ContainerViewController: UIViewController {
 
    var viewController : UISplitViewController!
 
}
```

In this property we will assign later the split view controller that we have already added in the Interface Builder.

Next, we will implement a function which we will use to assign the split view controller to this viewController object. 我们不能直接这样做，因为在与容器视图控制器工作时，有应该遵循的具体步骤。 These steps are described in detail in the official documentation by Apple (the link I gave you above), but they’re simple enough to be understood. Let’s see that function and then we’ll discuss a bit about it.

```swift
func setEmbeddedViewController(splitViewController: UISplitViewController!){
        if splitViewController != nil{
            viewController = splitViewController
 
            self.addChildViewController(viewController)
            self.view.addSubview(viewController.view)
            viewController.didMoveToParentViewController(self)
        }
}
```

As you see, the split view controller is passed as a parameter object to the above method. If it’s other than nil, then we assign it to the viewController object, and we perform the standard steps I talked about:

```swift
self.addChildViewController(viewController)
```

At first, the new view controller (the split VC) is added as a child view controller to the container. Next, with this command:

```swift
self.view.addSubview(viewController.view)
```

the split view controller’s view is set as the container’s view. That’s important to do, otherwise nothing is going to appear. Finally, this one:

```swift
viewController.didMoveToParentViewController(self)
```

indicates that the whole process is complete and the new view controller has been embedded to the container view controller.

For the time being our work in this class is over. You might wonder why we declared and used the viewController object, as we didn’t really need it anywhere. I should say in advance that we’ll use it later, and that’s the reason of its existence. Right next, we’ll see how this class can become handy through code, and how it will become the root view controller of the window of our application.

#### Split View Controller

The split view controller has already been added to the app through the Interface Builder, but until now we haven’t been able to use it, because we were performing all the preliminary steps for getting the data, configuring the custom container view and implementing the display of the parsed data. Now that all these are ready, we can start “playing around” with the split view controller, and see step by step various ways to set its appearance.

我们要做的第一件事是把它嵌入到自定义容器视图中。由于__在视图控制器层次中没有其他的视图控制器比容器的更高__，我们将在应用程序代理类完成这个工作。所以，打开`appdelegate.swift`文件，并首先在类的顶部为Split视图控制器声明一个属性，并采用`UISplitViewControllerDelegate`协议：

```swift
class AppDelegate: UIResponder, UIApplicationDelegate, UISplitViewControllerDelegate {
 
    var window: UIWindow?
 
    var splitViewController : UISplitViewController?
 
    ...
 
}
```

我们的下一步是从故事板取得一个拆分视图控制器实例并将它分配给上述属性，并把它作为子视图控制器添加到自定义容器视图控制器。这是已经做过的有关Split视图控制器的工作中最重要的任务之一，但是我们也会做另外两件事：我们会设置`AppDelegate`的实例作为Split视图控制器的委托，我们将设置`优先显示模式`属性。让我们看看所有的代码：

```swift
func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        // Override point for customization after application launch.
 
        splitViewController = UIStoryboard(name: "Main", bundle: nil).instantiateViewControllerWithIdentifier("idSplitViewController") as? UISplitViewController
        splitViewController?.delegate = self
 
        splitViewController?.preferredDisplayMode = UISplitViewControllerDisplayMode.AllVisible
 
        let containerViewController : ContainerViewController = ContainerViewController()
        containerViewController.setEmbeddedViewController(splitViewController)
 
        window?.rootViewController = containerViewController        
 
 
        return true
}
```

Notice that at the end we set the container view controller as the root view controller of the window. In the above implementation you can also see that the preferredDisplayMode property of the view controller was set to AllVisible value, which means that both the primary and the secondary view controllers will be visible at the same time. Below are shown all the possible values for that property:

1. AllVisible
2. Automatic: If no other specific value is set, this option is used by default. With it, iOS decides the proper way to display the primary and hidden VCs.
3. PrimaryHidden: The primary view controller is hidden.
4. PrimaryOverlay: The primary view controller is placed above the secondary view controller and hides a part of it.

In your applications, choose any option that best suits to your design or logic. For now, and for demo reasons mostly, we set this value to AllVisible.

If your try to run the app for first time, then here’s what you’ll get in both portrait and landscape orientation:

![](imgs/1006_Demo19.png)

![](imgs/1006_Demo20.png)

Playing a bit with the app, you notice that our XML data parsing works great! The data was downloaded and parsed perfectly, and it’s being properly displayed. Moreover, you see that the split view controller works as a navigation controller, where you can tap on the Back button to go back to the primary view controller, and to tap on any downloaded tutorial topic to display it to the web view of the secondary view controller once again. However, there’s a problem here. In the running app we are not seeing what we were expecting to see, and of course, if we wanted to have a navigation controller we would be using one. Actually, the navigation controller is displayed automatically by the iOS when the two children view controllers of the split VC are not appeared simultaneously (when the primary view controller is collapsed), and it gets disappeared when they are.

So, why doesn’t the split view controller display both of its view controllers, even though we set the preferred display mode to AllVisible? Well, the answer exists to the size classes and the trait collections story. To be specific, the split view controller doesn’t work as we expect, simply because the system doesn’t allow it to do so when it is displayed in compact horizontal size class, and that’s the value of the specific trait in iPhone devices. To overcome this, we must override the trait collection of the parent view controller of the split VC, which is our custom container.

In action now, open the ContainerViewController.swift file, and go to the setEmbeddedViewController method. In there, add one more line as shown right next:

```swift
func setEmbeddedViewController(splitViewController: UISplitViewController!){
        if splitViewController != nil{
            ...
 
            self.setOverrideTraitCollection(UITraitCollection(horizontalSizeClass: UIUserInterfaceSizeClass.Regular), forChildViewController: viewController)
        }
}
```

The setOverrideTraitCollection method is a quite important one, and if you’re going to work with trait collections, then make it a friend of yours. As it’s shown in the above snippet, we override the horizontal size class trait specifically, and we set the Regular as its new value. This addition, in combination with the AllVisible display mode, will result to the desired outcome. If we run the app again, here’s what we get:

![](imgs/1006_Demo21.png)

![](imgs/1006_Demo22.png)

I believe that is now clear why we used a custom container view controller, and how handy it can be in cases such like this, where we needed to override its trait collection.

管理拆分视图显示正如你所见，嵌入拆分视图控制器到容器视图控制器，and then overriding the trait collection of the latter wasn’t too hard eventually, 结果是split视图控制器将会像上面的截图那样显示。然而，在纵向上的子视图控制器可能不那么有用，并且对于一个iPhone 6 Plus设备也不会是一个问题，但是对于更小的屏幕尺寸发生了什么？

So, it’s obvious that we should manage the split view in a more refined way, by making it show both of the children view controllers when being in landscape mode. In portrait, we’ll let it use the default behavior, the navigation controller. All that will be achieved by performing a few additions and modifications to the current code.

If you remember, we managed to display the split view when we overrode the trait collection of the container view controller. This fact leads us to think that, if we were able to override the trait collection conditionally only when the device is in landscape orientation, then we could achieve our goal. However, that also means that we would need to control in some way the transition between orientations, and then act appropriately.

Fortunately, iOS 8 provides a brand new method through the UIKit framework, named viewWillTransitionToSize. This method is called when the size of the currently presented view controller is about to change, and it is the best shot we have in order to control an upcoming orientation change.

Now, let’s give a thought to the logic that we’ll apply to that method for a minute. We want to display the split view only when the device is in landscape mode, in other words when the width of the presented view controller is greater than the height of it. If we just had one screen size to all iPhone devices, this would be even easier to check, as we would only need to determine if the screen width is greater than 320.0 points or not. However, Apple now supports multiple screen sizes in iPhone, so we can’t use that approach. Anyway, in the case of the landscape orientation, we will override the trait collection of the container view controller as before, so as we get the desired splitting. In portrait mode though, we’ll use the setOverrideTraitCollection method once again, but this time we won’t override any trait at all; we will just set the nil value to the trait collection parameter, and force that way the container view controller to use the trait collection inherited by the app window, something that will lead to the initial split view behavior (no splitting at all).

Make sure that you have opened the ContainerViewController.swift file. In it, add the following method:

```swift
override func viewWillTransitionToSize(size: CGSize, withTransitionCoordinator coordinator: UIViewControllerTransitionCoordinator) {
 
        if size.width > size.height{
            self.setOverrideTraitCollection(UITraitCollection(horizontalSizeClass: UIUserInterfaceSizeClass.Regular), forChildViewController: viewController)
        }
        else{
            self.setOverrideTraitCollection(nil, forChildViewController: viewController)
        }
 
        super.viewWillTransitionToSize(size, withTransitionCoordinator: coordinator)
}
```

Notice that at the end we call the viewWillTransitionToSize method of the super class. For informative reasons only, instead of checking whether the width is greater than the height, we could have also written this:

```swift
if self.traitCollection.verticalSizeClass == UIUserInterfaceSizeClass.Regular {...}
```

With the above line, we check if the current vertical size class has the regular value. If that’s true, then the iPhone is currently is in portrait orientation, and is going to transit to landscape mode, so eventually we need to override the trait collection. However, since the size is given as an argument to the method, why not to use it in first place?

Besides that new addition, a small modification is also required in order to fully control the split view display. Go to the setEmbeddedViewController method and locate the following line:


```swift
self.setOverrideTraitCollection(UITraitCollection(horizontalSizeClass: UIUserInterfaceSizeClass.Regular), forChildViewController: viewController)
```

Either delete it, or comment it out, and you’re ready.

Now, if you run the app once again, you’ll notice that the split view is appeared only when being in landscape orientation, while in portrait the default navigation controller is appeared.

Before we move to the next part of the tutorial, there’s one last thing I would like to talk about. You notice when running the app that the secondary view controller (the TutorialViewController) is the one appeared by default when launching. You can change that if you want, if you return true in the following delegate method of the split view controller:

```swift
func splitViewController(splitViewController: UISplitViewController, collapseSecondaryViewController secondaryViewController: UIViewController!, ontoPrimaryViewController primaryViewController: UIViewController!) -> Bool {
 
        return true
}
```

By default, it returns false, but if you change that you can make the primary view controller the one that will be displayed first after having launched the app. So, if you want add the above method in the AppDelegate.swift file, and test the application once again.

#### Changing the Display Mode

Now that we have made our split view controller to properly be shown in each orientation, let’s see how we can hide and show on demand the primary view controller. In iOS 8, the UIKit framework provides a brand new convenient method named displayModeButtonItem. This method returns a bar button item which can be added as an item to the toolbar, and when it is used, it changes the display mode of the split view. This button item is not always visible to the toolbar. The truth is that the system decides whether it should be shown or not, depending on the device type, orientation and size class values, as well as the current display mode value. In our case, we want to use it in order to hide the primary view controller when we tap on it. However, even though it sounds quite simple, it’s a bit more complex in real. That’s because once we manage to change the display mode of the split view, that button item will disappear, so we must create a new, custom bar button item for making the primary view controller visible again.

The implementation we’ll see right next will make these two bar button items fully working in all orientations. In case you’ve read the split VC documentation or you have watched the WWDC session videos and you’re aware of the collapsed property, note that what we’ll do here doesn’t change that property, but the display mode of the split view controller.

Let’s get started by opening the TutorialViewController.swift file, where we’ll declare and initialize a new bar button property. This is going to be the custom button item that will appear when the system’s display mode button item is not visible. Its job will be to turn the primary view controller on, so we’ll title it “Tutorials”. At first, declare the next property right after any other existing properties:

```swift
class TutorialViewController: UIViewController {
 
    ...
 
    var tutorialsButtonItem : UIBarButtonItem!
 
 
}
```

Then, let’s initialize it in the viewDidLoad method:

```swift
override func viewDidLoad() {
        ...
 
        tutorialsButtonItem = UIBarButtonItem(title: "Tutorials", style: UIBarButtonItemStyle.Plain, target: self, action: "showTutorialsViewController")
 
    }
```

In the above snippet you can see how to create a UIBarButtonItem programmatically in Swift. Notice that in the action parameter, the showTutorialsViewController is the method that will be called when this button item gets tapped, and contrary to the Objective C language, we can omit the Selector keyword here. What this custom method will do is fairly easy (it will just set the AllVisible value to the preferred display mode of the split view controller), so let’s implement it now.

```swift
func showTutorialsViewController(){
        splitViewController?.preferredDisplayMode = UISplitViewControllerDisplayMode.AllVisible
}
```

Now, our next task is to go the viewDidAppear method, and specify if the display mode button item should be appeared or not when the view is shown. As you’ll see right next, we just insert that button item in the toolbar’s items:

```swift
override func viewDidAppear(animated: Bool) {
        super.viewDidAppear(animated)
 
        if tutorialURL != nil {
            ...
 
            if self.traitCollection.verticalSizeClass == UIUserInterfaceSizeClass.Compact{
                toolbar.items?.insert(self.splitViewController!.displayModeButtonItem(), atIndex: 0)
            }
        }        
    }
```

In simple words, the above segment orders the display mode button item to be displayed in landscape orientation. When this button item is tapped, the system calls another new delegate method of the split view controller to specify what the new display mode of the split view should be. This method is named targetDisplayModeForActionInSplitViewController, and by implementing it we can control the new value of the new display mode property. To do that, open the AppDelegate.swift file, and add the following method:

```swift
func targetDisplayModeForActionInSplitViewController(svc: UISplitViewController) -> UISplitViewControllerDisplayMode {
        return UISplitViewControllerDisplayMode.PrimaryHidden
    }
```

All the above are going to perfectly work, but we have a couple of things yet to consider about. First of all, what should happen after the display mode has changed? It’s our job to take care of this and display the custom bar button item that will replace the system’s one. The good news here is that the UISplitViewControllerDelegate protocol gives us one more nice delegate method, which informs us about the new display mode that is about to be applied. Using it, we can send a notification to the TutorialsViewController class, let it know that the display mode is about to change, and ultimately to show the “Tutorials” button item (or the system’s button item if needed again).

So, while being in the AppDelegate.swift file, add the next method:

```swift
func splitViewController(svc: UISplitViewController, willChangeToDisplayMode displayMode: UISplitViewControllerDisplayMode) {
        NSNotificationCenter.defaultCenter().postNotificationName("PrimaryVCDisplayModeChangeNotification", object: NSNumber.numberWithInteger(displayMode.toRaw()))
}
```

In the one line existing in the body of the method, you can see how we can post a notification in Swift. Notice that we set the display mode that’s about to be applied as the object of the notification, converted to a NSNumber object. The display mode value is an enum value, so by using the toRaw method we access the actual integer value of it.

Go back to the TutorialViewController.swift file now, and then straight ahead to the viewDidLoad method. We must make the class observing for the notification we just posted. Here it is:

```swift
override func viewDidLoad() {
        ...
 
        NSNotificationCenter.defaultCenter().addObserver(self, selector: Selector("handleFirstViewControllerDisplayModeChangeWithNotification:"), name: "PrimaryVCDisplayModeChangeNotification", object: nil)
 
    }
```

In the above line, I intentionally used this time the Selector keyword to specify the method that should be called after a notification has received, just to show this option too. As you can see, observing for notifications is easy in Swift too.

Before we implement the action method of the above observer, it would be a good idea to remove the observer as well when it’s no more needed. As you know, the viewDidUnload is deprecated, and the dealloc method doesn’t exist in Swift. So, where should we remove the observer? Well, Apple introduces a new method called deinit, and as its name suggests, it’s used as a deinitializer (the opposite of the initializer). So, somewhere inside the class, write the following:

```swift
deinit{
        NSNotificationCenter.defaultCenter().removeObserver(self)
}
```

Now that we’ve done this, let’s implement the handleFirstViewControllerDisplayModeChangeWithNotification:

```swift
func handleFirstViewControllerDisplayModeChangeWithNotification(notification: NSNotification){
    let displayModeObject = notification.object as? NSNumber
    let nextDisplayMode = displayModeObject?.integerValue
 
    if toolbar.items?.count == 3{
        toolbar.items?.removeAtIndex(0)
    }
 
    if nextDisplayMode == UISplitViewControllerDisplayMode.PrimaryHidden.toRaw() {
        toolbar.items?.insert(tutorialsButtonItem, atIndex: 0)
    }
    else{
        toolbar.items?.insert(splitViewController!.displayModeButtonItem(), atIndex: 0)
    }
}
```

Our first move in the above method is to get the next display mode value from the notification object. After that, we check how many items currently exist to the toolbar. If you recall, in the Interface Builder we had added two items, the Publish Date button item and the flexible space item. The third item (if exists) is either the system’s display mode button item, or our custom button item which we (will) add programmatically. When any of these two exists to the toolbar, then we remove it so we can insert the new one. Finally, we check what is going to be the next value of the display mode property: If the primary view controller is going to be hidden, then we insert the tutorialsButtonItems at the left side of the toolbar (that’s why it is inserted at index 0). Otherwise, we insert the default display mode button item, and that’s all!

Note this: In Swift we can insert or remove items from a toolbar (UIToolbar) in the way shown above. That’s allowed because the items of the toolbar are stored in a Swift array (Array). In Objective C however, we can’t do that because the items of the toolbar are stored to a NSArray array, which is immutable and therefore cannot be modified directly.

The above can’t be tested yet, because we haven’t considered the orientation changes. So, we must find a way to control these changes, and then add or remove the proper button item to the toolbar appropriately.

Up to this point we met a lot of new methods (delegate and not) existing in iOS 8. Now, we’re about to see a one more that will help us do what I just described. This method is called traitCollectionDidChange and it must be overridden. This method contains just one parameter, the previous trait collection of the view controller in which is implemented, after a change has occurred. As we have already seen, any trait collection changes happen to the container view controller, but it’s not necessary in this case to implement that method there. We’ll do it in the view controller we are currently in, the TutorialViewController, because it inherits the trait collection of the container view controller.

The logic we’ll use is simple: We will check the vertical size class, and if it previously had the compact value, meaning that it was in landscape orientation and now in portrait, any extra button item must be removed (either the display mode button item, or the custom one, the tutorialsButtonItem). Otherwise, the device orientation changed from portrait to landscape, and just like we previously did, we should add the proper button item depending on the current display mode value. Here it is:

```swift
override func traitCollectionDidChange(previousTraitCollection: UITraitCollection) {
        if previousTraitCollection.verticalSizeClass == UIUserInterfaceSizeClass.Compact{
            let firstItem = toolbar.items?[0] as? UIBarButtonItem
            if firstItem?.title == "Tutorials"{
                toolbar.items?.removeAtIndex(0)
            }
        }
        else if previousTraitCollection.verticalSizeClass == UIUserInterfaceSizeClass.Regular{
            if toolbar.items?.count == 3{
                toolbar.items?.removeAtIndex(0)
            }
 
            if splitViewController?.displayMode == UISplitViewControllerDisplayMode.PrimaryHidden {
                toolbar.items?.insert(tutorialsButtonItem, atIndex: 0)
            }
            else{
                toolbar.items?.insert(self.splitViewController!.displayModeButtonItem(), atIndex: 0)
            }
        }
    }
```

In the if case, the device is in portrait orientation, so we must remove the extra button item. If you wonder about this:


```swift
if firstItem?.title == "Tutorials"
```

then I should tell that we can only remove from the toolbar only our custom button item. The system’s display mode button item is automatically removed, and if you try to do so the app will crash. Regarding the else case, I don’t think that there’s anything further to be discussed, as we said everything a bit earlier.

Now feel free to run and test the app. Play around with the orientation changes and toggle the display mode of the split view controller as many times as you want. Everything regarding its appearance and its behavior is now ready!

![](imgs/1006_Demo25.png)

![](imgs/1006_Demo26.png)

#### Popover控制器

Up to iOS 7, an UIPopoverController could be used only in iPad devices. However, that’s a fact that belongs to the past since iOS 8, as thanks to the adaptive layout and the new features the UIKit framework supports, popover controllers can now be displayed in iPhone devices too. We have already talked about the adaptive layout, so I would like to mention a couple of words about a new great advancement regarding the UIKit framework, which has to do with the way modal (presented) view controllers are managed. iOS 8 introduces a new class named UIPresentationController, and an instance of this class handles a presented view controller throughout its lifetime. The interesting thing about a presentation controller is that it’s created on the fly by UIKit, once the view controller has been presented. As you understand, a whole new programming stuff such as new methods, properties and protocols have been integrated and provided to developers for usage. Presentation controllers can be used to control and specify various aspects of a view controller, like its style, transition, special properties and a lot more. Keep talking about this new concept would be beyond the scope of this tutorial as there’s a huge discussion regarding it, but I prompt you to read at least this documentation for getting a better taste of it. Also, I recommend again to go through the WWDC videos and watch any related material you find about that topic.

So, focusing on our mission again, a presentation controller is the tool we’ll use to “transform” a simple view controller into a popover controller. Doing so it’ s quite simple as you’ll see right next, but we’ll see everything in details after we have done some preliminary work. Before we begin, I remind you that in the popover controller we’re going to display the publish date of the selected tutorial topic. The next screenshot illustrates that:

![](imgs/1006_Demo27.png)

As you assume, we’re going to need a new view controller that we’ll use as a popover. So, let’s create one by adding a new class to the project. Go to the File > New > File… menu on Xcode, and create a new Cocoa Touch Class. Make sure that the new class is a subclass of the UIViewController, and name it PopoverViewController.

Once you have the new class ready, open the Main.storyboard file to show the Interface Builder. There, drag and drop to the canvas a new view controller object, and set the PopoverViewController as its class name. Also, set the idPopoverViewController value to the Storyboard ID field:

![](imgs/1006_Demo28.png)

Then, add a UILabel object as a subview to it, and set the following attributes:

* Frame: X=16, Y=20, Width=568, Height=80
* Alignment: Center
* Font Size: 14pt
* Lines: 3

Also, click on the Pin button at the bottom-right side of the Interface Builder to set some constraints to the label. Enable the top, leading and trailing space constraints, and check the Height constraint checkbox as well.

Lastly, go to the last step required for this view controller. We need an IBOutlet property connected to the label, so we can access it and set the publish date that’s about to be displayed. In the PopoverViewController.swift file, add the next line somewhere at the top of the class:

```swift
@IBOutlet weak var lblMessage: UILabel!
```

Then, return to Interface Builder, and connect this property to the label.
Note that we don’t make any connection between the PopoverViewController view controller and any other VC using a segue, as we’ll present it programmatically.

Now that the new view controller is ready, we must take care of the data that we’ll provide it with. This data is the publish date of the selected tutorial, but even though we have parsed it, we still haven’t passed it to the TutorialViewController class so it can be used. So, for starters open the TutorialViewController.swift file, and declare the following property:

```
var publishDate : String!
```

Next, open the TopicsTableViewController.swift file, and update the tableView(tableView:, didSelectRowAtIndexPath:) method so it passes the publish date to the previous class along with the tutorial URL. Right next I give you the whole implementation of this method, where you can find the required extra code:

```
override func tableView(tableView: UITableView, didSelectRowAtIndexPath indexPath: NSIndexPath) {
        let dictionary = xmlParser.arrParsedData[indexPath.row] as Dictionary<String, String>
        let tutorialLink = dictionary["link"]
        let publishDate = dictionary["pubDate"]
 
        let tutorialViewController = UIStoryboard(name: "Main", bundle: nil).instantiateViewControllerWithIdentifier("idTutorialViewController") as TutorialViewController
 
        tutorialViewController.tutorialURL = NSURL(string: tutorialLink!)
        tutorialViewController.publishDate = publishDate
 
        showDetailViewController(tutorialViewController, sender: self)
 
    }
```

Entering into the most important part of the work now, open the TutorialViewController.swift file, and in the class header adopt the UIPopoverPresentationControllerDelegate protocol, as we’re going to implement a delegate method later:

```
class TutorialViewController: UIViewController, UIPopoverPresentationControllerDelegate { ... }
```

The popover controller will appear when the bar button item at the right side of the toolbar gets tapped. That means that we’ll work in the showPublishDate method, where initially we’ll perform four steps:

1. We’ll get an instance of the PopoverViewController from the storyboard.
2. We’ll set its presentation style to Popover.
3. Each view controller contains a property regarding the presentation controller. Using it, we’ll make the TutorialViewController class the delegate of this presentation controller.
4. We’ll present the view controller.

Here we go:

```
@IBAction func showPublishDate(sender: AnyObject) {
        var popoverViewController = UIStoryboard(name: "Main", bundle: nil).instantiateViewControllerWithIdentifier("idPopoverViewController") as? PopoverViewController
 
        popoverViewController?.modalPresentationStyle = UIModalPresentationStyle.Popover
 
        popoverViewController?.popoverPresentationController?.delegate = self
 
        self.presentViewController(popoverViewController!, animated: true, completion: nil)
 
    }
```
	
As you see, we set no text to the popover label yet, and also we didn’t set any other properties regarding the popover appearance (such as the arrow direction). All that is our next step, but I separated the whole implementation in such way, because I want to highlight an important fact: The view controller should be presented first in order for the UIKit to create the presentation controller. All the properties we need to access belong to the presentation controller, which doesn’t exist until the last line of the above snippet to be executed.

Now that we know how the presentation controller works, and we also know that it will be created as we ask from the UIKit to present the popoverViewController view controller, we can specify any other properties we desire. So, here what’s next:

We’ll “tell” the presentation controller which is the bar button item that the popover should point to (in other words, which is the anchor of the popover).
We’ll specify the arrow direction of the popover.
That’s important: We’ll specify the content size of the popover. If we skip doing that, then the popover will have an unexpectedly big size.
Finally, we’ll set the proper text to the label of the popover.
All the above we’ll take place in the above IBAction method as complementary steps.

```
@IBAction func showPublishDate(sender: AnyObject) {
        ...
 
        popoverViewController?.popoverPresentationController?.barButtonItem = pubDateButtonItem
        popoverViewController?.popoverPresentationController?.permittedArrowDirections = .Any
        popoverViewController?.preferredContentSize = CGSizeMake(200.0, 80.0)
 
        popoverViewController?.lblMessage.text = "Publish Date:\n\(publishDate)"
    }
```

In the above code I access the presentation controller of the popoverViewController object directly, but alternatively we could have it assigned to a local variable. Note that in this case we’re accessing the popover presentation controller property of the view controller, which is a subclass of the presentation controller class.

If you go through the code we just wrote, you’ll see that everything seems quite straightforward, however that’s not all we need in order to display the popover controller. At some point above, we made our class the delegate of the presentation controller with this command:

```
popoverViewController?.popoverPresentationController?.delegate = self
```

We did that on purpose, as we’re about to implement the following delegate method:

```
func adaptivePresentationStyleForPresentationController(controller: UIPresentationController) -> UIModalPresentationStyle {
        return UIModalPresentationStyle.None
    }
```

By returning None in this method, we allow the presentation controller to adapt in the layout we ask and display the view controller as a popover. There are other values that can be returned too (there are various enumerations), however we can’t use any of them if we want to keep the normal popover controller appearance.

We are ready now! Run the app, and after having selected a tutorial topic, tap on the Publish Date button item to let the popover controller get presented. Test it in both orientations and watch it appearing now in iPhone too!

![](imgs/1006_Demo29.png)

![](imgs/1006_Demo30.png)

As I said before, note that there are other display styles beyond the popover as well, however none of them makes the view controller appear as a normal popover controller. Instead, they make it appear in fullscreen mode, or over any other existing content on the screen. I’m not going into details about all that, I leave you to do your research and play around with all that stuff if you want.

#### 总结

The greatest conclusion that comes out of this tutorial, is that with the adaptive layout the user interface design can now be done on a device-independent basis. Trait collections and size classes provide a brand new way to work with any kind of device, and things seem to be easier than they used to be in the past. Moreover, through this tutorial we saw how view controllers that were initially meant to be used only in iPad can now be integrated in iPhone applications too. New technologies and features, such as the presentation controllers, allow all that to become true, and surely there are still more to explore. Anyway, beyond the implementation of the split view controller and the popover controller, the two concepts that were our main goal here, we also met and learnt how other interesting stuff work in Swift, such as the XML data parsing, posting and receiving notifications, etc. I’m closing by simply saying that I hope you found this post useful and interesting, and of course, use the comments area for sharing your thoughts. Your feedback is always welcome and helpful!

For your reference, you can download the complete Xcode project from [here](https://www.dropbox.com/s/vjee4586nf7znnx/SplitAndPopover.zip?dl=0).