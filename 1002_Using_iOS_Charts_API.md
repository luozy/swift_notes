如何在Swift种使用iOS图表API创建漂亮的图表
===

[原文](http://www.appcoda.com/ios-charts-api-tutorial/)

Using charts to display data can help users understand the information more easily than they would if it were presented in a table, especially when dealing with a lot of data. With charts, you can easily see patterns in the data at a glance as opposed to reading through a whole table (or several tables) to get this information. The use of charts has been a common feature in business and fitness apps.

In this article, we’ll look at how to add charts to your application using the [ios-charts library](https://github.com/danielgindi/ios-charts) by [Daniel Cohen Gindi](https://github.com/danielgindi). ios-charts is an iOS port of the fairly popular Android library [MPAndroidChart](https://github.com/PhilJay/MPAndroidChart) created by [Philipp Jahoda](https://github.com/PhilJay). With this library, you can include various types of charts in your app fairly quickly and easily. With a few lines of code, you can have a fully working and interactive chart, that is highly customizable.

![](imgs/1002_Demo1.jpg)

Core features of the library include:

* 8 different chart types
* Scaling on both axes (with touch-gesture, axes separately or pinch-zoom)
* Dragging / Panning (with touch-gesture)
* Combined-Charts (line-, bar-, scatter-, candle-stick-, bubble-)
* Dual (separate) Y-Axis
* Finger drawing (draw values into the chart with touch-gesture)
* Highlighting values (with customizeable popup-views)
* Multiple / Separate Axes
* Save chart to camera-roll / export to PNG/JPEG
* Predefined color templates
* Legends (generated automatically, customizable)
* Customizable Axes (both x- and y-axis)
* Animations (build up animations, on both x- and y-axis)
* Limit lines (providing additional information, maximums, e.t.c.)
* Fully customizable (paints, typefaces, legends, colors, background, gestures, dashed lines, e.t.c.)

这个库的核心特性包括：

* 8种不同的图表类型
* 在两个轴上缩放
* 拖拽（利用`touch-gesture`）
* 混合图表
* 双重Y-轴
* 手绘
* 高亮值
* 多重/分隔坐标轴
* 存储图表到相册或导出到PNG/JPEG
* 预定义颜色模板
* 自动生成、定义
* 定制坐标轴
* 动画
* 行数限制
* 完全的自定义支持

#### Getting Started

To get started, first [download the starter project](https://www.dropbox.com/s/9ehz8ca5g4npoc2/iOSChartsDemoStarter.zip?dl=0) we’ll be using in the tutorial. It’s of a simple application called iOSChartsDemo. When you run the app, you’ll get a table with two items: Bar Charts and Other Charts. On tapping on these list items, you’ll get empty views. In the project, I have created two view controllers which we’ll use: BarChartViewController and ChartsViewController.

为了开始学习，首先下载我们将在教程中使用的[起始项目](https://www.dropbox.com/s/9ehz8ca5g4npoc2/iOSChartsDemoStarter.zip?dl=0)。它是一个叫iOSChartsDemo的示例应用。当你运行这个应用，你将看到一个带有两个物件的表格：条型图和其他图表。当点击这些列表条目的时候，你将看到空的视图。在项目中，我已经创建了将使用的两个视图控制器：`BarChartViewController`以及`ChartsViewController`。

Next we’ll include the library in our project. You can use [CocoaPods](https://cocoapods.org/pods/Charts) to install the library, but here, we’ll do the manual installation.

接下来，我们将在项目种包含库。你可以使用CocoaPods来安装这个库，但是这里，我们将进行手动安装。

[Download the ios-charts project](https://github.com/danielgindi/ios-charts/archive/master.zip). The zip file contains the library (in a folder named Charts) and a demo project (named ChartsDemo). The demo project is a great resource to use if you want to learn more about the library.

下载[ios-charts projetc](https://github.com/danielgindi/ios-charts/archive/master.zip)。这个zip文件包含库（在一个命名为Charts的文件夹）以及一个demo项目（名字为ChartsDemo）。如果你想要学习关于这个库更多的内容demo项目是一个很有用的资源。

Unzip the downloaded file and copy the Charts folder and paste it into your project’s (iOSChartsDemo) root directory. Open this Charts folder in Finder and drag Charts.xcodeproj to your project in Xcode. You should have something similar to what’s shown below.

解压下载的文件，然后复制Charts文件夹并粘贴到你项目的根目录。在Finder中打开Charts文件夹，并在Xcode中，拖动Charts.xcodeproj到你的项目中。

![](imgs/1002_Demo2.png)

Next select your project from the Project Navigator and make sure that the iOSChartsDemo target is selected. In the General tab on the right, locate the Embedded Binaries section and hit the + in this section to add the charts framework. Select Charts.framework from the list and click Add.

下一步从项目导航选择你的项目，确保iOSChartsDemo目标被选择。在右侧General选项卡上，定位Embedded Binaries节并点击“+”在这节中以添加图表框架。从列表中选择并单击添加Charts.Framework。

![](imgs/1003_Demo3.png)

If you want to use the library in an Objective-C project you can read how to do this from the [Usage instructions](https://github.com/danielgindi/ios-charts#usage).

如果你想在Objective-C项目中使用库，你可以阅读这个[使用说明](https://github.com/danielgindi/ios-charts#usage)。

Build the project with Command-B or by going to Product > Build. If you don’t build the project first, Xcode will give the error “Cannot load underlying module for ‘Charts'” when you import the Charts framework into your files.

使用`Command-B`编译项目或者通过Product > Build编译项目。如果你不先编译这个项目，当你引入Charts框架到你的文件中时，Xcode将呈现“Cannot load underlying module for 'Chargs'”错误。

Now we are ready to create our first chart.

项目我们已经准备好创建我们第一个图表。

#### Creating a Bar Chart 创建一个条形图

Open BarChartViewController.swift file and add the following import statement.

打开`BarChartViewController.swift`文件，然后添加下面的声明。

```
import Charts
```

Open the storyboard file. We need to add the view that will show the chart. Select Bar Chart View Controller from the Documents Outline and in the Attributes Inspector, uncheck the Under Top Bars in Extend Edges. We don’t want the chart to extend itself underneath the navigation bar.

打开storyboard文件。我们需要添加视图，以用于呈现图表。从Documents Outline选择BarChartViewController，以及在Attributes Inspector中，Extend Edges节，取消选中Under Top Bars。我们不想要图表在导航条下面扩展它本身。

![](imgs/1002_Demo4.png)

Next drag a View onto the Bar Chart View Controller and pin its edges as shown. This View is a child of the main View in the controller.

下一步，拖动一个视图到BarChartviewController，并且就像呈现的那样锁定它的边界。这个视图是控制器中主视图的一个子视图。

![](imgs/1002_Demo5.png)

With this View still selected, go to the Identity Inspector and set its Class to BarChartView. Then use the Assistant Editor to add an outlet of the view to the BarChartViewController class. Name the outlet barChartView. You should have the following in the BarChartViewController class.

当这个视图还被选中的时候，到Identity Inspector中，设置它的类为BarChartView。然后使用Assistant Editor添加一个视图的Outlet到BarChartViewController类。命名Outlet为`barChartView`。在BarChartViewController类中你应该有下面的内容：

```
@IBOutlet weak var barChartView: BarChartView!
```

Run the project and select Bar Chart from the table, you should get a view with the message “No chart data available”.

运行项目，并从表中选择Bar Chart，你将取得一个带有“No chart data available”信息的视图。

![](imgs/1002_Demo6.png)

You can customize this message if you want to display something different for your [Blank Slates](http://blog.teamtreehouse.com/tips-for-creating-a-blank-slate) when no data is available for a chart to be generated. In viewDidLoad() add the following to the bottom of the function.

当没有数据用于生成图表时，如果你想要为你的空状态展示不同的内容，你可以定制这条信息。在`viewDidLoad()`中，添加下面的内容到函数的底部。

```
barChartView.noDataText = "You need to provide data for the chart."
```

Run the project and you’ll be presented with the custom message.

运行项目，然后你将看到定制的信息。

![](imgs/1002_Demo6.png)

You can further add a description to this with the following. This can be used to explain to the user why the chart is empty and what they need to do in order to get data, for example a fitness app can let the user know that they need to record X number of runs before the data can be aggregated.

你还可以使用下面的内容添加一个描述。这个可以被用于向用户解译为什么图表是空的，以及为了得到数据，他们需要做什么，例如一个健身app或可以让用户知道在数据汇总前，他们需要记录跑步数。

```
barChartView.noDataTextDescription = "GIVE REASON"
```

Add the following attribute to the class. We’ll use it to store some mock data for the chart.

添加下面的属性到类。我们需要使用它来为图表存储一些测试数据。

```
var months: [String]!
```

Add the following function to the class. We’ll use it to set up the chart.

添加下面的函数给类。我们将使用它来设置图表。

```
func setChart(dataPoints: [String], values: [Double]) {
    barChartView.noDataText = "You need to provide data for the chart."
        
}
```

Notice I have included the statement we had in viewDidLoad() here. Remove that statement from viewDidLoad(). We’ll use setChart() to customize the chart.

注意，我包含了我们已经在`viewDidLoad()`声明的。移除`viewDidLoad()`中的声明。我们将使用`setChart()`来定制图表。

In viewDidLoad(), add the following to the bottom of the function.

在`viewDidLoad()`，添加下面的内容到方法的下面。

```
months = ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"]
let unitsSold = [20.0, 4.0, 6.0, 3.0, 12.0, 16.0, 4.0, 18.0, 2.0, 4.0, 5.0, 4.0]
        
setChart(months, values: unitsSold)
```

We set some mock data that gives the number of units sold of some product for each month in a year. We then pass this data to setChart().

我们设置一些测试数据，然后给定一年中每个月一些产品的点。然后我们传递这个数据给`setChart()`。

For a chart to display data, we need to create a BarChartData object and set it as the barChartView’s data attribute. Add the following to the bottom of setChart()

为了让一个图表呈现数据，我们需要创建一个`BarChartData`的对象，并且设置它为`barChartView`的数据属性。添加下面的内容到`setchart()`的下面。

```
var dataEntries: [BarChartDataEntry] = []
        
for i in 0..<dataPoints.count {
    let dataEntry = BarChartDataEntry(value: values[i], xIndex: i)
    dataEntries.append(dataEntry)
}
        
let chartDataSet = BarChartDataSet(yVals: dataEntries, label: "Units Sold")
let chartData = BarChartData(xVals: months, dataSet: chartDataSet)
barChartView.data = chartData
```

In the above code, we create an array of BarChartDataEntry objects. The BarChartDataEntry initializer takes the value of each data entry, the index of the entry the value corresponds to and an optional label.

We then use this object to create a BarChartDataSet which is created by passing in the array of BarChartDataEntry objects and a label to describe the data.

然后，我们使用这个对象来创建一个`BarChartDataSet`，这个对象通过传递`BarChartDataEntry`对象数组以及一个标签来描述数据。

Finally we use this to create a BarChartData object which we set as our chart view’s data.

最后，我们使用这个来创建`BarChartData`对你，我们使用这个对象设置我们图表视图的数据。

Run the app and you should have a bar chart of the data.

运行app，你将看到一个条形图。

![](imgs/1002_Demo8.png)

You can set a description of the chart that will appear on the bottom right of the view. By default, the text is set to “Description” as you can see in the image above. A look through the documentation of [MPAndroidChart](https://github.com/PhilJay/MPAndroidChart/wiki/Getting-Started), shows that you should be able to change the location of the description, but looking through the iOS API, this wasn’t included. The library is still being maintained, so this might be added at a later date. If you want to change the location of the description, you can change this in the drawDescription(context) function of the ChartViewBase class which is a superclass of the BarChartView class.

你可以设置一个图表的描述，这将在视图右侧的底部出现。默认的，在上面的图片中，你可以看到文本被设置为“Description“。 浏览MPAndroidChart的文档，发现你应该能够改变描述的位置，但是查看iOS的API，这个特性没有被包含。这个库仍然被维护当中，所以这可能在稍后被添加进来。如果你想要改变描述的位置，你可能在`BarChartView`的父类`ChartViewBase`类的`drawDescription(context)`方法中进行改变。

For our app, we’ll remove the description text. Add the following to the bottom of setChart() to set the description text to an empty string.

对于我们的app，我们将移除描述文本。添加下面的内容到`setChart()`底部，设置描述文本为空字符串。

```
barChartView.descriptionText = ""
```

#### Customizing the Chart 定制图表

You can customize the look of the chart view by changing some of its attributes. We’ll look at some of these; you can have a look at the documentation to find out what else can be customized.

你可以通过修改图表的属性来定制图表视图的外观。我们将会看到这些变化；你可以在文档中查找如何进行定制。

First we’ll change the default color of the bar chart. Add the following to the bottom of setChart()

首先，我们改变条型图的颜色。添加下面的内容到`setChart()`。

```
chartDataSet.colors = [UIColor(red: 230/255, green: 126/255, blue: 34/255, alpha: 1)]
```

The above sets the colors that will be associated to our data set. We set this to an array of UIColor objects. Since we only have one color in our array, this will be used for all the entries.

上面的内容利用我们的数据集来设置颜色。我们通过一个UIColor对象数组来设置。因为在我们的数组中，仅有一种颜色，这个将会被用在所有的点上。

![](imgs/1002_Demo9.png)

If you want a different color for each data entry, then you have to provide as many colors as the total number of data entries you have, in our case 12. If you give a lesser number of colors than the total entries, then the bars, starting from the left will be set to the different colors provided until the colors run out whereby the color scheme will be repeated (like the image below where we use a template, 5 colors are repeated throughout)

如果你想要为没一个数据条不同的颜色，你需要提供与数据条数一样多的颜色。如果你赋予的颜色少于总的数据条，数据条的颜色将会从左到右循环设置。

The API also comes with some predefined color templates you can use to set different colors for the data set. They include:

API还提供一些预定义的颜色模板给你使用，以为数据集设置不同的颜色。他们包括：

* ChartColorTemplates.liberty()
* ChartColorTemplates.joyful()
* ChartColorTemplates.pastel()
* ChartColorTemplates.colorful()
* ChartColorTemplates.vordiplom()

Using the ChartColorTemplates.colorful() template gives the following.

使用下面给出的`ChartColorTemplates.colorful()`模板。

```
chartDataSet.colors = ChartColorTemplates.colorful()
```

![](imgs/1002_Demo10.png)

To change the position of the x-axis labels use the following.

使用下面的内容改变X轴标签的位置。

```
barChartView.xAxis.labelPosition = .Bottom
```

Now the labels are at the bottom of the chart.

现在标签在图表的下面。

![](imgs/1002_Demo11.png)

You can also change the chart’s background color with the following.

你还可以使用下面的内容修改图表的背景颜色。

```
barChartView.backgroundColor = UIColor(red: 189/255, green: 195/255, blue: 199/255, alpha: 1)
```

With the above, you’ll get:

利用上面的内容，你将实现：

![](imgs/1002_Demo12.png)

#### Animation 动画

You can add some animation to the chart to make it more interesting. There are 3 main types of animation methods you can use to animate either both axes or the x and y axis seperately.

你可以添加一些动画到图表，以便让它更有趣。有三种主要的你可以使用的动画方法类型，来驱动两个坐标轴或分别驱动。

* animate(xAxisDuration: NSTimeInterval, yAxisDuration: NSTimeInterval)
* animate(xAxisDuration: NSTimeInterval)
* animate(yAxisDuration: NSTimeInterval)

You can add an optional ChartEasingOption to the above functions. The options available are:

你可以添加一个可选的ChartEasingOption给上面的函数。这些可用的属性是：

* Linear
* EaseInQuad
* EaseOutQuad
* EaseInOutQuad
* EaseInCubic
* EaseOutCubic
* EaseInOutCubic
* EaseInQuart
* EaseOutQuart
* EaseInOutQuart
* EaseInQuint
* EaseOutQuint
* EaseInOutQuint
* EaseInSine
* EaseOutSine
* EaseInOutSine
* EaseInExpo
* EaseOutExpo
* EaseInOutExpo
* EaseInCirc
* EaseOutCirc
* EaseInOutCirc
* EaseInElastic
* EaseOutElastic
* EaseInOutElastic
* EaseInBack
* EaseOutBack
* EaseInOutBack
* EaseInBounce
* EaseOutBounce
* EaseInOutBounce

Add the following to setChart()

添加下面的内容给`setChart()`

```
barChartView.animate(xAxisDuration: 2.0, yAxisDuration: 2.0)
```

Run the app and the bar chart will animate into view. We animate both axes in 2 seconds.

运行app，条型图将会以动画的方式进入视图。我们在两秒内驱动两个坐标轴。

![](imgs/1002_Demo13.gif)

Change the above statement to:

修改上面的声明为：

```
barChartView.animate(xAxisDuration: 2.0, yAxisDuration: 2.0, easingOption: .EaseInBounce)
```

And you’ll see the effect of the above.

然后你将在下面看到上面内容的效果。

![](imgs/1002_Demo14.gif)

#### Limit Lines 限制线

The limit line is an additional feature for all Line, Bar and ScatterCharts. It allows the displaying of an additional line in the chart that marks a certain limit on the specified axis (x- or y-axis). Such a line can be used to set a target/goal value for the data and helps the user easily see where they didn’t meet the target.

限制线是所有的折线图、条状图以及散点图的附加特性。它允许在图表中显示附加的线，来作为对制定坐标轴的确定的限制。这样一条线，能够被用于为数据设置一个目标值，以及帮助用户方便的看到他们什么地方没有实现目标。

To add a limit line to the chart, add the following to setData()

添加下面的内容到`setData()`，以便添加一条限制线给图表。

```
let ll = ChartLimitLine(limit: 10.0, label: "Target")
barChartView.rightAxis.addLimitLine(ll)
```

On running the app, you should see a red line marking the 10 units limit. In the above code we added a label to the limit line, but ChartLimitLine has another initializer that doesn’t take the label, so you can omit it if you prefer.

在运行当中的app，你应该看到一条红色的线，作为10单位的限制。在上面的代码，我们添加一个标签来给限制线，但是ChartLimitLine有另一个初始化器，没有使用这个标签，所有你可以选择是否移除它。

![](imgs/1002_Demo15.png)

#### Touch Events 点击事件

If you run the app, you’ll notice that zooming by pinching and double tapping work by default. Also, a tap on a bar highlights the tapped bar. It’s great that we get this functionality for free without writing any code for it, but you might want to add more functionality, for example take some action when the user taps on a bar.

如果你运行app，你将注意到，默认的是通过“捏”和双击来进行缩放的。同时，单击一个条目会高亮被单击的条目。非常棒的是，我们能够自由的取得这个函数而不用为它写任何的代码，但是你也许想要添加更多的方法，例如当用户单击一个条目时产生一些动作。

To detect selections made inside the chart, we’ll use the ChartViewDelegate protocal.

为了探究图表内部所做的选择，我们将使用ChartViewDelegate协议。

Modify the class’ declaration as shown.

修改类的声明如下。

```
class BarChartViewController: UIViewController, ChartViewDelegate {
```

Add the following to viewDidLoad() after the call to super.viewDidLoad()

在调用`super.viewDidLoad()`后，添加下面的内容到`viewDidLoad()`。

```
barChartView.delegate = self
```

Then add the following function to the class.

然后添加下面的方法到类。

```
func chartValueSelected(chartView: ChartViewBase, entry: ChartDataEntry, dataSetIndex: Int, highlight: ChartHighlight) {
    println("\(entry.value) in \(months[entry.xIndex])")
}
```

The above is called when a value has been selected inside the chart view. Here we print out the value and month selected.

当在图表视图的内部，一个值被选择的时候，调用上面的代码。这里，我们打印出选择的月份和值。

#### Saving the Chart 保存图表

You can save the current state of a chart as an image. You can choose to save it to the camera roll or you can set a path for it to be saved to.

你可以保存图表当前的状态为图片。你可以选择保存它到相册或设置一个路径保存。

First we’ll add a Save button to the chart view. Open the storyboard file and locate the Bar Chart View Controller. Drag a Navigation Item to the view controller’s navigation bar, then drag a Bar Button Item and place it to the right corner of the Navigation Item. Remove the “Title” text of the Navigation Item’s Title attribute in the Attributes Inspector. Select the Bar Button Item and set its identifier to Save in the Attributes Inspector. You should have the following.

首先，我们添加一个保存按钮给图表视图。打开storyboard文件，然后定位BarChartViewController。拖拽一个导航控件给视图控制器的导航条，然后拖拽一个Bar Button Item，并放置它到Navigation Item的右侧。在属性指示器中，移除Navigation Item标题属性的“Title”文本。选择Bar Button Item然后再属性指示器中，设置它的标识为`Save`。你将获得如下的效果：

![](imgs/1002_Demo16.png)

Next create an action for the button. Name it saveChart. You should have the following in the BarChartViewController class.

下一步创建为这个按钮创建一个动作。命名它为`saveChart`。在`BarChartViewController`类中，应该有下面的内容。

```
@IBAction func saveChart(sender: UIBarButtonItem) {
        
}
```

Modify the above method as shown.

修改上面的方法如下所示。

```
@IBAction func saveChart(sender: UIBarButtonItem) {
    barChartView.saveToCameraRoll()
}
```

Run the app and when you tap the Save button, an image of the chart will be saved to the camera roll. You can view it using the Photos app.

运行app，点击Save按钮，图表的一张图片将会保存到相册。你可以使用Photos应用查看它。

You can instead set a path to save to with the following method.

你可以使用下面的方法设置一个路径作为替代来保存到相册。

```
barChartView.saveToPath(path: String, format: ChartViewBase.ImageFormat, compressionQuality: Double)
```

The format can be either .JPEG or .PNG and the compressionQuality is the compression quality for lossless formats (JPEG).

格式可以是.JPEG或.PNG，并且`compressionQuality`作为高保真格式的JPEG。

#### More Charts 更多的图表

Here we’ll look at a couple of other chart examples we can create. I won’t give detailed instructions since we’ve looked at most of what we’ll do.

这里我们大略的浏览一下我们能够创建的其他图表示例。由于将做的很多我们都已经做过了，所以我不会给出详细的指引。

First locate the Charts View Controller in the storyboard file and set the Class of the view labelled Pie Chart View to PieChartView in the Identity Inspector. Do the same for the Line Chart View, setting its class to LineChartView. Then create outlets for these two views named pieChartView and lineChartView respectively.

首先，再storyboard文件中找到ChartsViewController，然后再Identity Inspector中设置并状图视图类到PieChartView。同样的设置折线图的类为LineChartView。然后创建Outlet为这两个视图，并分别命名为`pieChartView`以及`lineChartView`。

Then modify the ChartsViewController class as ahown.

然后像下面呈现的修改`ChartsViewController`类。

```
import UIKit
import Charts
 
class ChartsViewController: UIViewController {
    
    @IBOutlet weak var lineChartView: LineChartView!
    @IBOutlet weak var pieChartView: PieChartView!
 
    override func viewDidLoad() {
        super.viewDidLoad()
 
        // Do any additional setup after loading the view.
        
        let months = ["Jan", "Feb", "Mar", "Apr", "May", "Jun"]
        let unitsSold = [20.0, 4.0, 6.0, 3.0, 12.0, 16.0]
        
        setChart(months, values: unitsSold)
        
    }
    
    func setChart(dataPoints: [String], values: [Double]) {
        
        var dataEntries: [ChartDataEntry] = []
        
        for i in 0..<dataPoints.count {
            let dataEntry = ChartDataEntry(value: values[i], xIndex: i)
            dataEntries.append(dataEntry)
        }
        
        let pieChartDataSet = PieChartDataSet(yVals: dataEntries, label: "Units Sold")
        let pieChartData = PieChartData(xVals: dataPoints, dataSet: pieChartDataSet)
        pieChartView.data = pieChartData
        
        var colors: [UIColor] = []
        
        for i in 0..<dataPoints.count {
            let red = Double(arc4random_uniform(256))
            let green = Double(arc4random_uniform(256))
            let blue = Double(arc4random_uniform(256))
            
            let color = UIColor(red: CGFloat(red/255), green: CGFloat(green/255), blue: CGFloat(blue/255), alpha: 1)
            colors.append(color)
        }
        
        pieChartDataSet.colors = colors
        
        
        let lineChartDataSet = LineChartDataSet(yVals: dataEntries, label: "Units Sold")
        let lineChartData = LineChartData(xVals: dataPoints, dataSet: lineChartDataSet)
        lineChartView.data = lineChartData
        
    }
 
}
```

In the above, we import the Charts framework into the class and create a pie chart and a line chart in the same way as we created a bar chart. Note however, we use the superclass ChartDataEntry to create data entry objects when in the bar chart example we used BarChartDataEntry. Not all charts have subclasses for ChartDataEntry and so here, we use the superclass. For the chart data set and chart data objects, we use the specific subclass for the particular chart.

在上面的代码中，我们引入了Chart框架到类中，然后使用同样的方式（就像在条型图中）创建了一个饼状图和一个折线图。注意，无论如何，当我们在条形图中使用BarChartDataEntry时，我们使用ChartDataEntry的父类来创建输入数据对象。不是所有的图表都有父类。对于图表数据集和图表数据对象，我们使用指定的特殊图表的父类。

If you run the app and select Other Charts from the table view, you should see a line chart and a colored pie chart. Your app will look different from the one shown, since we use random numbers for the pie chart’s colors.

如果你运行这个app并在表视图中选择Other Charts，你将看到一个折线图和一个多彩的饼状图。你的app将看起来与展示的不一样，因为我们使用随机数来处理饼状图的颜色。

![](imgs/1002_Demo17.png)

#### Conclusion 总结

We’ve only looked at a few chart types that can be created using the ios-charts library. We’ve also only touched the surface of the customization you can perform on a chart. If you want to know what else the library is capable of, you can look through the code of the ChartsDemo project that comes with the library download and also have a look at the [project’s Wiki](https://github.com/PhilJay/MPAndroidChart/wiki). The link to the Wiki page leads to the MPAndroidChart project documentation. At the time of writing, there is no documentation for the iOS version of the library, but since the API is about 95% the same as on Android, the Android documentation can still come in handy when searching for help.

我们只是看了少数的可以使用ios-charts库创建的图表类型。我们也只是接触了你可以在一个图表上执行的定制的皮毛。如果你想要知道这个库还有哪些能做的事情，你可以查看随着库文件一起下载的CartsDemo项目，还可以看一下这个[项目百科](https://github.com/PhilJay/MPAndroidChart/wiki)。到百科页面的链接将指赂MPAndroidChart项目文档。在写文章的时候，这个库还没有iOS的文档，但是因为这个库大约90%与Android上的一致，Android文档仍然可以提供帮助。

You can [download the completed project here](https://www.dropbox.com/s/9dq1onn6ndjvf1o/iOSChartsDemo.zip?dl=0).

你可以下载[全部的项目文件](https://www.dropbox.com/s/9dq1onn6ndjvf1o/iOSChartsDemo.zip?dl=0)。

[The original repository](https://github.com/PhilJay/MPAndroidChart)

[原始的库](https://github.com/PhilJay/MPAndroidChart)

