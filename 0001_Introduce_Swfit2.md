Swift. A modern programming language that is safe, fast, and interactive.
===

> Swift is a powerful and intuitive programming language for iOS, OS X, and watchOS. Writing Swift code is interactive and fun, the syntax is concise yet expressive, and apps run lightning-fast. Swift is ready for your next project — or addition into your current app — because Swift code works side-by-side with Objective-C.

> Swift has been refined from the ground up. It generates faster code across the board, both for release and debug builds. The Swift compiler is also faster, even while adding new Fix-it suggestions such as where you can use let instead of var. Comments can include **Markdown syntax** to add rich text and embedded images that display in Xcode’s Quick Help. A new assistant shows your Swift API in a “header-like” view. And new syntax features combined with improvements to the Cocoa frameworks and Objective-C will make your code more expressive, and even safer.

#### Error handling model

An advanced error handling model provides clear, expressive syntax for catching and throwing errors. It’s also easy to create your own custom error types so you can describe error cases with clear, meaningful names. The new error model was designed to work seamlessly with NSError and the Cocoa frameworks. Error handling code now looks like:

```
func loadData() throws { }

func test() {
    do {
        try loadData()
    } catch {
        print(error)
    }
}
```

#### Syntax improvements

New syntax features let you write more expressive code while improving consistency across the language. The SDKs have employed new Objective-C features such as generics and nullability annotation to make Swift code even cleaner and safer. Here is just a sampling of Swift 2.0 enhancements.

* Powerful control flow with `do`, `guard`, `defer`, and `repeat`
* Keyword naming rules unified for functions and methods
* Protocol extensions and default implementations
* Extended pattern matching to work in if clauses and for loops

Xcode 7 includes a powerful migrator that will help convert your application and playground code to work with the latest syntax improvements in Swift 2.0.

#### Modern

Swift is the result of the latest research on programming languages, combined with decades of experience building Apple platforms. Named parameters brought forward from Objective-C are expressed in a clean syntax that makes APIs in Swift even easier to read and maintain. Inferred types make code cleaner and less prone to mistakes, while modules eliminate headers and provide namespaces. Memory is managed automatically, and you don’t even need to type semi-colons. All this modern thinking results in a language that is easy and fun to use. 

```
extension String {
    var banana : String {
        let shortName = String(dropFirst(characters))
        return "\(self) \(self) Bo B\(shortName) Banana Fana Fo F\(shortName)"
    }
}

let bananaName = "Jimmy".banana
```

Swift has many other features to make your code more expressive:

* [Closures](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Closures.html#//apple_ref/doc/uid/TP40014097-CH11-ID94) unified with function pointers
* [Tuples](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Types.html#//apple_ref/doc/uid/TP40014097-CH31-ID448) and multiple return values
* [Generics](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Generics.html#//apple_ref/doc/uid/TP40014097-CH26-ID179)
* Fast and concise iteration over a range or collection
* Structs that support [methods](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Functions.html#//apple_ref/doc/uid/TP40014097-CH10-ID158), [extensions](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Extensions.html#//apple_ref/doc/uid/TP40014097-CH24-ID151), and [protocols](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Protocols.html#//apple_ref/doc/uid/TP40014097-CH25-ID267)
* Functional programming patterns, e.g., map and filter
* Native [error handling](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ErrorHandling.html#//apple_ref/doc/uid/TP40014097-CH42-ID508) using try / catch / throw

#### Interactive Playgrounds

Playgrounds make writing Swift code incredibly simple and fun. Type a line of code and the result appears immediately. You can then Quick Look the result from the side of your code, or pin that result directly below. The result view can display graphics, lists of results, or graphs of a value over time. You can open the Timeline Assistant to watch a complex view evolve and animate, great for experimenting with new UI code, or to play an animated SpriteKit scene as you code it. When you’ve perfected your code in the playground, simply move that code into your project.

And new in Xcode 7, playgrounds can contain comments that use rich text with bold, italic, and bullet lists in addition to embedded images and links. You can even embed resources and supporting Swift source code in the playground to make the experience incredibly powerful and engaging, while the visible code remains simple. Playgrounds let you:

* Share curriculum to teach programming with beautiful text and interactive code.
* Design a new algorithm and watch its results every step of the way.
* Create new tests and verify they work before promoting into your test suite.
* Experiment with new APIs to hone your Swift coding skills.
* Turn your experiments into documentation with example code that runs within the playground.

Read-Eval-Print-Loop (REPL). The LLDB debugging console in Xcode includes an interactive version of the Swift language built right in. Use Swift syntax to evaluate and interact with your running app, or write new code to see how it works in a script-like environment. Available from within the Xcode console or in Terminal.

#### Designed for Safety

Swift eliminates entire classes of unsafe code. Variables are always initialized before use, arrays and integers are checked for overflow, and memory is managed automatically. Syntax is tuned to make it easy to define your intent — for example, simple three-character keywords define a variable (var) or constant (let).

Another safety feature is that by default Swift objects can never be nil. In fact, the Swift compiler will stop you from trying to make or use a nil object with a compile-time error. This makes writing code much cleaner and safer, and prevents a huge category of runtime crashes in your apps. However, there are cases where nil is valid and appropriate. For these situations Swift has an innovative feature known as optionals. An optional may contain nil, but Swift syntax forces you to safely deal with it using the ? syntax to indicate to the compiler you understand the behavior and will handle it safely.

#### Fast and Powerful

From its earliest conception, Swift was built to be fast. Using the incredibly high-performance LLVM compiler, Swift code is transformed into optimized native code that gets the most out of modern hardware. The syntax and standard library have also been tuned to make the most obvious way to write your code also perform the best.

Swift is a successor to both the C and Objective-C languages. It includes low-level primitives such as types, flow control, and operators. It also provides object-oriented features such as classes, protocols, and generics giving Cocoa and Cocoa Touch developers the performance and power they demand.

#### Objective-C Interoperability

You can create an entirely new application with Swift today, or begin using Swift code to implement new features in your app, or enhance existing ones. Swift code co-exists along side your existing Objective-C files in the same project, with full access to your Objective-C API, making it easy to adopt.

To get started with Swift, download Xcode and follow the tutorials available on the [Resources](https://developer.apple.com/swift/resources/) tab.

