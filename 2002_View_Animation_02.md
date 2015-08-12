UIView Animations 学习笔记（二）定制动画效果及切换效果
===

## 二、视图切换定制

### 2.1 基础说明

要创建定制切换效果，你需要做如下的事情：

1. 创建一个类来实现`UIViewControllerAnimatedTransitioning`协议。在这个类中将编写代码来执行动画，这个类将作为动画控制器被关联。
2. 在呈现一个视图控制器之前，设置一个类作为它的切换效果委托。这个委托将为动画控制器获取一个回调方法，这个回调方法用于视图控制器的呈现。
3. 实现回调方法来返回一个在第一步创建的动画控制器的实例。

#### 2.1.1 [`UIViewControllerAnimatedTransitioning`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerAnimatedTransitioning_Protocol/)协议

`UIViewControllerAnimatedTransitioning`协议用于描述视图控制器切换的动画效果，我们可以通过实现这个协议，并利用该类来实现在视图间切换过程中的动画效果。可以在这个类中定义一个动画对象，这个动画对象创建一个视图控制器在一个固定的时间内出现/消失在屏幕上的动画效果。使用这个协议创建的动画 __必须__ 是无法交互的。如果要创建可以交互的切换，必须混合动画对象和另一个控制动画时间的对象。

在动画对象中，实现`transitionDuration:`方法来指定切换的过程，实现`animateTransition:`方法来创建动画。在切换过程中使用的对象的信息在一个上下文对象中传递给`animateTransition:`方法。使用这个上下文对象(`UIViewControllerContextTransitioning`)提供的信息，来移动目标视图控制器的视图在指定的区间内出现/消失在屏幕上。

从一个实现了[`UIViewControllerTransitioningDelegate`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerTransitioningDelegate_protocol/index.html#//apple_ref/occ/intf/UIViewControllerTransitioningDelegate)协议的切换委托对象中创建一个动画对象。当呈现一个视图控制器时，设置呈现类型为[`UIModalPresentationCustom`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/c/econst/UIModalPresentationCustom)，并指定切换委托到视图控制器的[`transitioningDelegate`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instp/UIViewController/transitioningDelegate)属性。视图控制器从切换委托中接受动画对象，并使用这个切换委托来执行动画。可以为视图控制器的出现、消失分别定制不同的动画对象。

为了添加用户交互到一个视图控制器的切换效果中，必须一起使用一个动画对象和一个交互动画对象（一个实现[`UIViewControllerInteractiveTransitioning`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerInteractiveTransitioning_protocol/index.html#//apple_ref/doc/uid/TP40013059)协议的定制类）。

__执行切换__

* [animateTransition(_:)](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerAnimatedTransitioning_Protocol/#//apple_ref/occ/intfm/UIViewControllerAnimatedTransitioning/animateTransition:) 必须实现
* [animationEnded(_:)](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerAnimatedTransitioning_Protocol/#//apple_ref/occ/intfm/UIViewControllerAnimatedTransitioning/animationEnded:)

__切换过程：__

* [transitionDuration(_:)](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerAnimatedTransitioning_Protocol/#//apple_ref/occ/intfm/UIViewControllerAnimatedTransitioning/transitionDuration:) 必须实现

具体实现如下所示。

```swift
import Foundation
import UIKit

class AnimatedTransitioning: NSObject, UIViewControllerAnimatedTransitioning {

    func transitionDuration(transitionContext: UIViewControllerContextTransitioning) -> NSTimeInterval {
        return 5
    }
    
    func animateTransition(transitionContext: UIViewControllerContextTransitioning) {
        let fromViewController = transitionContext.viewControllerForKey(UITransitionContextFromViewControllerKey)!
        let toViewController = transitionContext.viewControllerForKey(UITransitionContextToViewControllerKey)!
        
        let finalFrameForVC = transitionContext.finalFrameForViewController(toViewController)
        
        let containerView = transitionContext.containerView()
        let bounds = UIScreen.mainScreen().bounds
        
        toViewController.view.frame = CGRectOffset(finalFrameForVC, 0, bounds.size.height)
        containerView.addSubview(toViewController.view)
        
        UIView.animateWithDuration(transitionDuration(transitionContext), delay: 0.0, usingSpringWithDamping: 0.5, initialSpringVelocity: 0.0, options: .CurveLinear, animations: {
            fromViewController.view.alpha = 0.5
            toViewController.view.frame = finalFrameForVC
            }, completion: {
                finished in
                transitionContext.completeTransition(true)
                fromViewController.view.alpha = 1.0
        })
    }
}
```

#### 2.1.2 [`UIViewControllerTransitioningDelegate`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerTransitioningDelegate_protocol/index.html#//apple_ref/occ/intf/UIViewControllerTransitioningDelegate)

```swift
import UIKit

class ViewController: UIViewController, UIViewControllerTransitioningDelegate {

    // ...

    let customPresentAnimationController = CustomPresentAnimationController()
    let customDismissAnimationController = CustomDismissAnimationController()

    override func prepareForSegue(segue: UIStoryboardSegue, sender: AnyObject?) {
        
        if segue.identifier == "showAction" {
            let toViewController = segue.destinationViewController as! UIViewController

            // 用于给目标视图指定切换动画代理
            toViewController.transitioningDelegate = self
        }
    }

    // 用于视图出现
    func animationControllerForPresentedController(presented: UIViewController, presentingController presenting: UIViewController, sourceController source: UIViewController) -> UIViewControllerAnimatedTransitioning? {
        return customPresentAnimationController
    }
    
    // 用于视图消失
    func animationControllerForDismissedController(dismissed: UIViewController) -> UIViewControllerAnimatedTransitioning? {
        return customDismissAnimationController
    }

    // ...
}
```

#### 2.1.3 [`UIViewControllerInteractiveTransitioning`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerInteractiveTransitioning_protocol/index.html#//apple_ref/doc/uid/TP40013059)

> 注意：
> 
> `UIPercentDrivenInteractiveTransition`是`UIViewControllerInteractiveTransitioning`的子类

```swift
import UIKit

class CustomInteractionController: UIPercentDrivenInteractiveTransition {
    
    var navigationController: UINavigationController!
    var shouldCompleteTransition = false
    var transitionInProgress = false
    
    var completionSeed: CGFloat {
        return 1 - percentComplete
    }
    
    func attachToViewController(viewController: UIViewController) {
        navigationController = viewController.navigationController
        setupGestureRecognizer(viewController.view)
    }
    
    private func setupGestureRecognizer(view: UIView) {
        view.addGestureRecognizer(UIPanGestureRecognizer(target: self, action: "handlePanGesture:"))
    }
    
    func handlePanGesture(gestureRecognizer: UIPanGestureRecognizer) {
        let viewTranslation = gestureRecognizer.translationInView(gestureRecognizer.view!.superview!)
        switch gestureRecognizer.state {
        case .Began:
            transitionInProgress = true
            navigationController.popViewControllerAnimated(true)
        case .Changed:
            var const = CGFloat(fminf(fmaxf(Float(viewTranslation.x / 200.0), 0.0), 1.0))
            shouldCompleteTransition = const > 0.5
            updateInteractiveTransition(const)
        case .Cancelled, .Ended:
            transitionInProgress = false
            if !shouldCompleteTransition || gestureRecognizer.state == .Cancelled {
                cancelInteractiveTransition()
            } else {
                finishInteractiveTransition()
            }
        default:
            println("Swift switch must be exhaustive, thus the default")
        }
    }
}
```

#### 2.1.4 [`UIViewControllerContextTransitioning`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerContextTransitioning_protocol/)

`UIViewControllerContextTransitioning`协议的方法为切换的两个视图控制器提供上下文信息。在一次切换当中，切换中使用的动画对象从`UIKit`接受一个完整的配置上下文对象，`UIViewControllerAnimatorTransitioning`或`UIViewControllerInteractiveTransitioning`协议的方法从提供的对象中提取它们需要的信息。

__访问切换对象__

* [`containerView()`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerContextTransitioning_protocol/index.html#//apple_ref/occ/intfm/UIViewControllerContextTransitioning/containerView)：获取在动画执行过程中作为父视图的视图。
* [`viewControllerForKey(_:)`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerContextTransitioning_protocol/index.html#//apple_ref/occ/intfm/UIViewControllerContextTransitioning/viewControllerForKey:)：Key可选值参数见下方的__常量__部分。
* [`viewForKey(_:)`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerContextTransitioning_protocol/index.html#//apple_ref/occ/intfm/UIViewControllerContextTransitioning/viewForKey:)

__获取切换框体矩形__

* [`initialFrameForViewController(_:)`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerContextTransitioning_protocol/index.html#//apple_ref/occ/intfm/UIViewControllerContextTransitioning/initialFrameForViewController:) 必须
* [`finalFrameForViewController(_:)`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerContextTransitioning_protocol/index.html#//apple_ref/occ/intfm/UIViewControllerContextTransitioning/finalFrameForViewController:) 必须

`finalFrameForViewController`用于获取指定的视图控制器视图最终的框体矩形。

__获取切换行为__

* [`isAnimated()`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerContextTransitioning_protocol/index.html#//apple_ref/occ/intfm/UIViewControllerContextTransitioning/isAnimated) 必须
* [`isInteractive()`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerContextTransitioning_protocol/index.html#//apple_ref/occ/intfm/UIViewControllerContextTransitioning/isInteractive) 必须
* [`presentationStyle()`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerContextTransitioning_protocol/index.html#//apple_ref/occ/intfm/UIViewControllerContextTransitioning/presentationStyle) 必须

__反馈切换进度__

* [`completeTransition(_:)`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerContextTransitioning_protocol/index.html#//apple_ref/occ/intfm/UIViewControllerContextTransitioning/completeTransition:) 必须
* [`updateInteractiveTransition(_:)`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerContextTransitioning_protocol/index.html#//apple_ref/occ/intfm/UIViewControllerContextTransitioning/updateInteractiveTransition:) 必须
* [`finishInteractiveTransition()`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerContextTransitioning_protocol/index.html#//apple_ref/occ/intfm/UIViewControllerContextTransitioning/finishInteractiveTransition) 必须
* [`cancelInteractiveTransition()`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerContextTransitioning_protocol/index.html#//apple_ref/occ/intfm/UIViewControllerContextTransitioning/cancelInteractiveTransition) 必须
* [`transitionWasCancelled()`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerContextTransitioning_protocol/index.html#//apple_ref/occ/intfm/UIViewControllerContextTransitioning/transitionWasCancelled) 必须

__获取屏幕方向__

* [`targetTransform`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerContextTransitioning_protocol/index.html#//apple_ref/occ/intfm/UIViewControllerContextTransitioning/targetTransform) 必须

__常量__

* [`View Controller Transition Keys`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerContextTransitioning_protocol/index.html#//apple_ref/doc/constant_group/View_Controller_Transition_Keys)
* [`View Transition Keys`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerContextTransitioning_protocol/index.html#//apple_ref/doc/constant_group/View_Transition_Keys)

`View Controller Transition Keys`用于标识在切换执行过程中所存在的视图控制器，包含两个值，一个是`UITransitionContextFromViewControllerKey`用于标识在动画开始时就已经存在的视图控制器，一个是`UITransitionContextToViewControllerKey`用于标识在动画完成后应该存在的视图控制器。

`View Transition Keys`用于标识在切换执行过程中所存在的视图，包含两个值，一个是`UITransitionContextFromViewKey`用于标识在动画开始时就已经存在的视图，一个是`UITransitionContextToViewKey`用于标识在动画完成后应该存在的视图。
