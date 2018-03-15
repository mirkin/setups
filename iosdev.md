## About

Notes on iOS dev. S4 means swift 4

## Table of contents

- [About](#about)
- [XCode IDE](#xcode-ide)
  - [](#x)
  - [Pods](#pods)
- [Swift Language](#swift-language)
  - [Access Levels](#access-levels)
  - [Extensions](#extensions)
  - [Let and Var](#let-and-var)
  - [Optionals](#optionals)
  - [Subscripts](#subscripts)
  - [](#x)
  - [](#x)
  - [](#x)
  - [](#x)
  
## XCode IDE

##### No Soft Keyboard In Simulator
Deselect 
Hardware>Keyboard>Connect Hardware Keyboard
cmd shift K

##### XCode Disk space waste 
Library>Developer>Xcode>iOS Device Support

##### Format code

Little hack to auto reformat code

Select All, CopyCut, Paste

## Swift Language

#### Access Levels
S4
5 levels 

* open

* public little more restrictive subclass and override than *open* can only subclass in same module

* internal Default access from any source file in the defining module but not outside

* fileprivate Like private, defines an entity (class, extension, property, ...) as private to everybody outside the source file it is declared in, but accessible to all entities in that source file.

* private more restrictive than fileprivate allows use to any extensions in same source file though as of S4

#### Extensions
S4
Add new functionality to an existing class, structure, enumeration, or protocol type even if you don't have the source code.
Can add new functionality to a type, but they cannot override existing functionality. You are not creating a new type but modifying the existing type.
```swift
extension SomeType {
    // new functionality to add to SomeType goes here
}
```
Often used to break the implementation of types up into logical components.

#### let and var
let immutable like a constant  
var mutable 

#### Optionals

Optional either contains something or it's empty (nil)  
Send a message to nil and you app will crash so we need to avoid that

Remember your func can return an optional in that case you can return the type or nil

```swift
// returns an String? even though you are returning a String in the code
func bingoNames(number: Int) -> String? {
    switch number {
    case 2: return "One little duck"
    default: return nil
    }
}

// returns a non optional String
func bingoNames(number: Int) -> String {
    switch number {
    case 2: return "One little duck"
    default: return "not sure"
    }
}
```

```swift
// declare optional
let x: String?
// or less convenient
let x: Optional<String> 
```

Forced unwrap use ! BAD if it's nil you get an exception 
```swift
var a = x!
```
Instead do this it's called *optional binding* 
```swift
if let a=x {
    /*
    do something here with a because it does contains something 
    temporary constant a will be a string not an optional
    */
  }
  else
  {
    // you may want to do smething here which is executed if it was nil
  }

// you can use as many optional bindings or booleans as you want in one if 
if let a=x, let something=somethingelse, a>b && b<c {
  /*
  will reach here if all optional bindings are not nil and all booleans are true
  */
}
```

**Implicitly unwrapped optionals** - if you are sure it will never be nil  
WHY? Not many uses could do away with a few 'if let'  use on IBOutlet  
Rather than placing an exclamation mark after the optional’s name each time you use it, you place an exclamation mark after the optional’s type when you declare it.

```swift
let assumedInt: Int! = 123
let implicitInt: Int = assumedInt
```

You can still use optional binding on an implicitly unwrapped optional. It's still an optional behind the scenes you just don't have to unwrap it.

nil coalescing operator ??
```swift
// Can be used to give a default if the optional is nil, otherwise the actual value of the optional is given
print("Hello, \(name ?? "Anonymous")!")
```

**Optional chaining**

It doesn't mean you can put a ? then a long line of deep dots and be golden. You still need a ? for every optional in the chain.

Always returns an optional even if the final value in the chain is for example a String or Int you will get a String? or Int?  Query and call properties, methods, and subscripts on an optional we use ?  
Everything after the question mark will only be run if everything before the question mark has a value

```swift
let translation = bingoNames(number: 11)?.someOptionalValue?.someOtherOptionalValue?.whatever
```


```swift
func bingoNames(number: Int) -> String? {
    switch number {
    case 2: return "One little duck"
    case 11: return "Legs eleven"
    default: return nil
    }
}

//Forced unwrapping
var translation = bingoNames(number:2)!
print("The translation is \(translation)")
//prints "The translation is One little duck\n" as it's a String

//optional chaining translationB is a String?
let translationB = bingoNames(number:11)?.uppercased()
print("The translation is \(translationB)")

//optional binding translationC is a String
if let translationC=bingoNames(number:2)
{
    print("The translation is \(translationC)")
    //prints "The translation is One little duck\n"
}

//Forced unwrapping translationD is a String
var translationD = bingoNames(number:11)!
print("The translation is \(translationD)")
```

#### Subscripts
Classes, structures, and enumerations can define subscripts.   
Lets you get and set using []  
Simple example below but you can include multiple input params mything[1,3] and can overload multiple subscripts are inferred based on the types of values that are declared within the subscript braces.

```swift
class daysofaweek {
   private var days = ["Sunday", "Monday", "Tuesday", "Wednesday",
      "Thursday", "Friday", "saturday"]
   subscript(index: Int) -> String {
      get {
         return days[index]
      }
      set(newValue) {
         self.days[index] = newValue
      }
   }
}
var p = daysofaweek()

print(p[0])
print(p[1])
print(p[2])
print(p[3])
```

#### Auto Layout and StackViews

TIPS.

Use stackviews first only use constraints if you can't do what you want with a stackview

Evolve it from the details don't try to do the whole thing right away work on a small part from the inside.

Xcode may look wrong as it's buggy trust devices and simulator more.

1. Start tweaking properties of stack view. 
2. If you don't get what you want then use another stack view. (Stack view in stack view)
3. Still not right? Tweak compression resistance or hugging priority of views in stack view.
4. Still can't get what you want? Add constraints to the stack view.
5. Last resort add constraints to the views.
6. To connect views within different stackviews see 5.
7. Worst case, use a filler view.

Constraint

You can see the equation in the top bar
FirstItem=SecondItem*multiplier+constant
For edges use the value Standard for their default

Axis - Horizontal vertical  
Spacing - Takes precedence over the view's intrinsic content size. Will shrink view rather than spacing if limited space. Can be negative which will overlap views.
Distribution - 
Modifies size of views FILL(default), FILL EQUALLY, FILL PROPORTIONALLY
Works hard not to modify size of view EQUAL SPACING, EQUAL CENTERING
* Equal Spacing - tried not to change intrinsic size, distribute along available space equal seperation. Not enough space, it will reduce the size ov the views starting with those with lowest compression resistance. If all same comp resist it will shrink first one from the left.
* Equal Centering - Similar instead of measuring seperation from edges it measures from their center.
* Fill equally - divides all space in equal chunks amonh available views. IF intrinsic content size needs changing then it will.
* Fill proportionally - If size of views not matching avaialable space will change size of views keeping compression resistance and hugging priority in mind. Tweakable with comp resist and hug priority.
* Fill - Similar but will try to change the first view.

* Alignment - controls how views expand perpendicular to the axis.
* Horizontal axis stackview you have top middle and bottom
* Vertical axis stackview left center or right

Horizontal text stack views aligns by baseline 

Low content hugging stretch before one with higher value

Editor>Canvas>Show Layout Rectangles
Editor>Canvas>Show Involved View For Selected Constraints



### Pods

#### Install
Setup clones the [CocoaPods Master Specs](https://github.com/CocoaPods/Specs) repository into ~/.cocoapods/
```sh
sudo gem install cocoapods
pod setup --verbose
```


#### Overview
Keep checking Swift Package Manager one day it will be the best route.  
Download a library and drag source files over into your project? There is a better way. 

Go to your project folder, create podfile and edit it 
```sh
pod init
open -a Xcode Podfile
```

You get something like this
```
# Uncomment the next line to define a global platform for your project
# platform :ios, '9.0'

target 'YourProject' do
  # Comment the next line if you're not using Swift and don't want to use dynamic frameworks
  use_frameworks!

  # Pods for YourProject

end
```

Change as below here we are adding Alamofire as a dependencies just add any you need.

```
platform :ios, '11.2'

target 'YourProject' do
  use_frameworks!
  pod 'Alamofire', '4.4.0'
end
```

Then

```sh
pod install
```

Now must always open the project with the .xcworkspace file and not the .xcodeproj.

Now you can import Alamofire in your swift code.
