#### The Main Function

所有的类 C 应用程序，都会以`main`方法作为整个程序的入口。在 iOS 当中，我们不需要编写`main`方法，XCode 将会创建`main`程序作为我们项目的一部分。

```
#import <UIKit/UIKit.h>
#import "AppDelegate.h"

int main(int argc, char * argv[])
{
    @autoreleasepool {
        return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
    } 
}
```

#### The Structure of an App

> `UIApplication`对象是所有 iOS APP 的核心，它负责系统和app中其他对象的交互。



/Users/congjf/Documents/swiftprojects/Presentation-Controllers-Demo-master/PresentationsDemo/ExampleAnimatedTransitioning.swift:42:36: '(UIViewControllerContextTransitioning) -> $T3' is not identical to 'UIViewControllerContextTransitioning'k