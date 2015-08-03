UIView Animations 学习笔记（一）简介及API说明
===

## 一、简介

随着iOS7的发布，动画与运动效果变成了Apple和第三方开发者进行app设计的中心。iOS7介绍了一种app扁平化、最小化设计，这导致在一些app中有相似的UI。为了区分他们的app与其他人的app，开发者引入了一些类似动画和运动效果的特性来使得他们的app脱引而出。

动画不只是用于设置你的app部分，他们可以改善你应用程序的用户体验整体。对于怎样使用动画来改善用户体验（UX）的示例，你应该查看Apple是怎样在他们的app中使用动画的。例如，在Photos应用中，当你从一个集合中选择一个照片，这个照片从被选择的那个扩大出来，当关闭这个照片时，这个照片缩小回被选择时的状态。这个添加给这个应用的导航，它让你明确的知道你在那里，如果在浏览很多的图片的时候。

有很多种方法在你的应用中包含动画，他们中的一些通过使用`UIKit Dynamics`、层动画（`Layer Animation`）、视图控制器切换（`View Controller Transitions`）或通过使用类似__Facebook Pop__或[`JNWSpringAnimation`](https://github.com/jwilling/JNWSpringAnimation)等第三方库来实现。

### 1.1 UIView API说明

在你的视图上创建动画是指改变视图的属性以及让`UIKit`自动的催动他们。我们改变的属性被使用`Animatable`进行标记。

下面的列表展示可动属性。

* center —— 中心
* alpha —— 透明度
* frame —— 边框
* bounds —— 约束
* transform —— 切换
* backgroundColor —— 背景色
* contentStretch —— 内容缩放

随着我们对视图动画的使用，你将发现所有的动画都是引入了上面属性的一种或多种变化。

#### 1.1.1 基础动画 API

对于简单的视图动画，`UIKit`提供了下面可以被用于在屏幕上催动视图的API。

* [`animateWithDuration(_:animations:)`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/occ/clm/UIView/animateWithDuration:animations:)
* [`animateWithDuration(_:animations:completion:)`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/occ/clm/UIView/animateWithDuration:animations:completion:)
* [`animateWithDuration(_:delay:options:animations:completion:)`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/occ/clm/UIView/animateWithDuration:delay:options:animations:completion:)

第一个方法使用两个参数，一个用于描述动画持续时间（秒）的值以及一个用于指定改变属性用的闭包。`UIKit`将获取视图的方向状态以及创建一个你在闭包中指定的从这个状态到终止状态的平滑的过渡。

另外两个API与第一个类似，但是他们使用额外的参数，用于给动画添加更多的配置。第二个使用一个`完成`时的闭包，你可以使用这个闭包来指定当第一个动画完成时要作的第二个动画或者你可以做一些这个UI的清理（从视图结构树中移除已经运动到其他场景的视图）。

第三个API使用两个附加的参数 - `delay`指定动画开始前的延迟时间，`options`（一个`UIViewAnimationOptions`常量）制定你想要怎样执行动画。下面展示了可用的选项。

具体使用方式如下所示：

```swift
UIView.animateWithDuration(1.0, delay: 0.0, options: UIViewAnimationOptions.CurveEaseOut, animations: {
    self.centerXAlign.constant += self.view.bounds.width
    
    self.view.layoutIfNeeded()
}, completion: nil)
```

#### 1.1.2 弹性动画 API

弹性动画（Spring Animations）尝试模型化真实的弹动行为，这方面，当一个视图从一个点移动到另一个，在放置到位置前它将跳动/震荡朝向终点。

下面是我们用于弹性动画的API。

* [`animateWithDuration(_:delay:usingSpringWithDamping:initialSpringVelocity:options:animations:completion:)`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/occ/clm/UIView/animateWithDuration:delay:usingSpringWithDamping:initialSpringVelocity:options:animations:completion:)

上面的API添加了两个新的传入参数`usingSpringWithDamping`和`initialSpringVelocity`。

* `usingSpringWithDamping`：设置在弹性动画执行过程中的阻尼大小，阻尼（Damping）是一个从0到1的值，它决定朝向动画终点的视图弹性有多少。越靠近1，弹性越小。
* `initialSpringVelocity`：决定动画的初始速率。如果你想要在动画开始时动的更加的猛烈一些，设置一个更大的值，如果你想要一个相对柔和的动画，你可以设置值为0.

具体使用方式如下所示：

```swift
UIView.animateWithDuration(2.0, delay: 1.2, usingSpringWithDamping: 0.1, initialSpringVelocity: 100.0, options: nil, animations: {

    self.button.bounds = CGRect(x: bounds.origin.x - 80, y: bounds.origin.y, width: bounds.size.width + 160, height: bounds.height)

    }, completion: nil)
```

#### 1.1.3 关键帧动画 API

关键帧动画（Keyframe Animation）让你可以设置一个动画的不同阶段。你可以组织不同的动画在一起，以便共享一些公用的属性，但是仍然可以分别控制他们。

代替一个仅仅是沿着一条路径移动的动画，`UIKit`将执行不同的动画阶段。

关键帧动画API如下所示。

* [`animateKeyframesWithDuration(_:delay:options:animations:completion:)`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/occ/clm/UIView/animateKeyframesWithDuration:delay:options:animations:completion:)
* [`addKeyframeWithRelativeStartTime(_:relativeDuration:animations:)`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/occ/clm/UIView/addKeyframeWithRelativeStartTime:relativeDuration:animations:)

上面两个方法需要一起使用，第二个方法的调用是嵌套在第一个动画的闭包中的。

第一个函数设置动画的全部配置项目，如需要多少时间，延迟以及它的选项。之后你定义一个或多个第二个方法在动画闭包中来设置动画的不同阶段。每一帧的相对起始时间和相对持续时间为0至1，表示动画总时间内的百分比时间。

#### 1.1.4 视图间切换 API

视图切换（View Transition）被用于添加一个新的视图到你的视图组织结构当中或从属兔组织结构当中移除一个视图时。 

用于创建视图切换的API有：

* [`transitionWithView(_:duration:options:animations:completion:)`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/occ/clm/UIView/transitionWithView:duration:options:animations:completion:)
* [`transitionFromView(_:toView:duration:options:completion:)`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/occ/clm/UIView/transitionFromView:toView:duration:options:completion:)

使用第一个来介绍一个视图到视图组织结构中。这个方法采用我们在其他动画方法中同样的参数。

第二个用于从视图结构中取得一个视图，然后放置一个新的视图在它的位置。

#### 1.1.5 其他 API

除了以上提到的在各个场景中常用的API外，Cocoa框架还提供如下的API。

* [`performSystemAnimation(_:onViews:options:animations:completion:)`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/occ/clm/UIView/performSystemAnimation:onViews:options:animations:completion:)
* [`performWithoutAnimation(_:)`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/occ/clm/UIView/performWithoutAnimation:)

`performSystemAnimation`用于在一个或多个视图上执行一个特定的由系统提供的动画，伴随着可选的并行动画。参数中，`onViews`为需要执行动画的视图的数组，`animations`为想要并行执行的动画。

`performWithoutAnimation`用于禁用一个视图动画。参考[`setAnimationsEnabled:`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/occ/clm/UIView/setAnimationsEnabled:)、[`areAnimationsEnabled`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/occ/clm/UIView/areAnimationsEnabled)

### 1.2 动画数据类型

#### 1.2.1 视图动画选项([`UIViewAnimationOptions`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/c/tdef/UIViewAnimationOptions))

常用数据类型：

* `CurveEaseOut`：动画开始时快速，动画临近结束时变慢
* `CurveEaseIn`：动画开始时慢速，动画临近结束时变快
* `CurveLinear`：匀速

完整数据类型：

```swift
struct UIViewAnimationOptions : OptionSetType {
    init(rawValue rawValue: UInt)
    static var LayoutSubviews: UIViewAnimationOptions { get }
    static var AllowUserInteraction: UIViewAnimationOptions { get }
    static var BeginFromCurrentState: UIViewAnimationOptions { get }
    static var Repeat: UIViewAnimationOptions { get }
    static var Autoreverse: UIViewAnimationOptions { get }
    static var OverrideInheritedDuration: UIViewAnimationOptions { get }
    static var OverrideInheritedCurve: UIViewAnimationOptions { get }
    static var AllowAnimatedContent: UIViewAnimationOptions { get }
    static var ShowHideTransitionViews: UIViewAnimationOptions { get }
    static var OverrideInheritedOptions: UIViewAnimationOptions { get }
    static var CurveEaseInOut: UIViewAnimationOptions { get }
    static var CurveEaseIn: UIViewAnimationOptions { get }
    static var CurveEaseOut: UIViewAnimationOptions { get }
    static var CurveLinear: UIViewAnimationOptions { get }
    static var TransitionNone: UIViewAnimationOptions { get }
    static var TransitionFlipFromLeft: UIViewAnimationOptions { get }
    static var TransitionFlipFromRight: UIViewAnimationOptions { get }
    static var TransitionCurlUp: UIViewAnimationOptions { get }
    static var TransitionCurlDown: UIViewAnimationOptions { get }
    static var TransitionCrossDissolve: UIViewAnimationOptions { get }
    static var TransitionFlipFromTop: UIViewAnimationOptions { get }
    static var TransitionFlipFromBottom: UIViewAnimationOptions { get }
}
```

#### 1.2.2 [UIViewAnimationCurve](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/c/tdef/UIViewAnimationCurve)

用于对运动弧线的支持。

```swfit
enum UIViewAnimationCurve : Int {
    case EaseInOut
    case EaseIn
    case EaseOut
    case Linear
}
```

#### 1.2.3 [UISystemAnimation](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/c/tdef/UISystemAnimation)

当一个动画一定结束时，将其从视图结构中移除。

```swift
enum UISystemAnimation : UInt {
    case Delete
}
```

#### 1.2.4 [UIViewAnimationTransition](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/c/tdef/UIViewAnimationTransition)

用在动画闭包中的动画过渡效果选项。

```swift
enum UIViewAnimationTransition : Int {
    case None
    case FlipFromLeft
    case FlipFromRight
    case CurlUp
    case CurlDown
}
```

## 参考

* [Creating Simple View Animations in Swift](http://www.appcoda.com/view-animation-in-swift/)
* [Animating Views with Block Objects](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/doc/uid/TP40006816-CH3-SW108)