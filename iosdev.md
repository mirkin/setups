## About

Notes on iOS dev. S4 means swift 4

## Table of contents

- [About](#about)
- [Simulator](#simulator)
- [XCode IDE](#xcode-ide)
  - [](#x)
  - [Pods](#pods)
- [Swift Language](#swift-language)
  - [Struct vs Class](#struct-vs-class)
  - [Access Levels](#access-levels)
  - [Extensions](#extensions)
  - [Let and Var](#let-and-var)
  - [Optionals](#optionals)
    - [Implicitly unwrapped optionals](#implicitly-unwrapped-optionals)
    - [Optional chaining](#optional-chaining)
  - [Subscripts](#subscripts)
  - [Functions](#functions)
    - [Parameters](#parameters)
      - [Default Parameter Values](#default-parameter-values)
      - [Variadic Parameters](#variadic-parameters)
      - [In-Out Parameters](#in-out-parameters)
      - [](#)
      - [](#)
  - [Closures](#closures)
  - [Collections](#collections)
    - [Array](#array)
    - [Dictionary](#dictionary)
  - [Generics](#generics)
  - [Auto Layout and StackViews](#auto-layout-and-stackViews)
  - [Saving Data](#saving-data)
    - [File Structure](#file-structure)
    - [UserDefaults](#userdefaults)
    - [Roll Your Own Files](#roll-your-own-files)
    - [Core Data](#core-data)
      - [Core Data Stack](#core-data-stack)
      - [Fetched Results Controller](#fetched-results-controller)
      - [Notification Center](#notification-center)
      - [Caching](#caching)
      - [Concurrency](#concurrency)
      - [Migration](#migration)
  - [Predicates](#predicates)
  - [](#x)
  - [](#x)
  - [](#x)
  - [](#x)
  
## Simulator

Help
```
xcrun simctl help
```

List available sims any running will show as booted
```
xcrun simctl list
```

Create/Boot/Shutdown/Erase simulator 
pass create the new name and then device type and runtime which you can get from xcrun simctl list
```
xcrun simctl create MYDEVICE com.apple.CoreSimulator.SimDeviceType.iPhone-7 com.apple.CoreSimulator.SimRuntime.iOS-11-4
37FB3D7B-1350-42AF-A039-738C1186FB76
xcrun simctl boot 37FB3D7B-1350-42AF-A039-738C1186FB76
xcrun simctl shutdown 37FB3D7B-1350-42AF-A039-738C1186FB76
xcrun simctl erase 37FB3D7B-1350-42AF-A039-738C1186FB76
```

Simulator app must be running to see the device window
```
open /Applications/Xcode.app/Contents/Developer/Applications/Simulator.app/
```

Install/Uninstall/Launch/Terminate App
To uninstall you use the bundle identifier not the app file name
Also Debug-iphoneos directory didn't work Debug-iphonesimulator did work but the icon
was missing.
```
xcrun simctl install booted /Users/alex/Library/Developer/Xcode/DerivedData/APPNAME-ccavkoifbprloefgguklopkvbpzj/Build/Products/Debug-iphoneos/APPNAME.app

xcrun simctl install 37FB3D7B-1350-42AF-A039-738C1186FB76 /Users/alex/Library/Developer/Xcode/DerivedData/APPNAME-ccavkoifbprloefgguklopkvbpzj/Build/Products/Debug-iphoneos/APPNAME.app

xcrun simctl install booted /Users/alex/Library/Developer/Xcode/DerivedData/APPNAME-ccavkoifbprloefgguklopkvbpzj/Build/Products/Debug-iphonesimulator/APPNAME.app

xcrun simctl uninstall booted com.gurgleaps.MPT

xcrun simctl launch booted com.gurgleaps.MPT
xcrun simctl launch --console booted com.gurgleaps.MPT
xcrun simctl terminate booted com.gurgleaps.MPT
```

Screenshot or video
```
xcrun simctl io booted screenshot screen.png
xcrun simctl io booted recordVideo app-preview.mov
xcrun simctl io booted recordVideo --type=mp4 <PATH TO VIDEO FILE>
```

Get info with bundle ID
```
xcrun simctl appinfo booted com.gurgleaps.MPT
```

Logs
```
xcrun simctl spawn booted log stream -level=debug
xcrun simctl spawn booted log collect
```

Open a URL
```
xcrun simctl openurl booted <URL>
```

Add Photo/Video
```
xcrun simctl addmedia booted /path/to/photo.png
```
  
## XCode IDE

Top of the editor window you can pulldown to go string to function definition.
You can also select a function call and jump to definition.

#### Handy Refactor Functionality
Select some code in a function right click refactor>extract method and it will pull it out into it's own new function and call that function from where it used to be. Useful!

Use refactor to rename functions and variables so it will spread through the code and change everything.

#### No Soft Keyboard In Simulator
Deselect 
Hardware>Keyboard>Connect Hardware Keyboard
cmd shift K

#### XCode Disk space waste 
Library>Developer>Xcode>iOS Device Support

#### Format code

Little hack to auto reformat code

Select All, CopyCut, Paste

#### Shortcuts

cmd r - run

Use these toggles like blender to fly around.

Left Pane (Navigator) cmd 0  
Right Pane (Utilities) cmd alt 0  
Console and Debugger cmd shift y  
Assistant Editor cmd alt enter / cmd enter show/hide  

If left pane open cmd 1,2,3,4,5,6 etc switches around tabbing sections  
for right pane its cmd alt 1,2,3,4 etc 

cmd shift o - open (opens a file in navigator just start typing filename)  
cmd / - comment out . 
ctrl i - audo indent code awesome!!!  

Use preferences/navigation/uses focused navigator rather than primary so files open in the window you want.

#### Editor

Use // MARK:

#### Debug

po varaiable - print out variable   
p variable - print variable try both versions  

On a breakpoint you can continue running or step over line by line. Step over if a line is a method call will treat it as one line so you may want to use step into. This will go into that method and you can go over that line by line.

#### Lag

sleep(15) in your code to emulate a delay and see how your app copes.

## Swift Language

### 

You can use AppDelegate to get to the first view, downcast it and set any variables you need.

AppDelegate.swift
```swift
  let myThing=MyThing(arg: "Hi there")

  func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
    //configure the first view, lets get at it
    let navigationController = window?.rootViewController as! UINavigationController
    let firstViewController=navigationController.topViewController as! MyViewController
    firstViewController.myThing=myThing
    return true
  }
```

You can pass and share between views using prepare(for seque:) in your view controller
```swift
  override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
      if let vc = segue.destination as? MyViewController {
          vc.myVariable=self.myVariable
      }
  }
```

**Dependency Injection** or **Singleton** Singleton avoids possibility of creating multiple instances and I've used it non stop in other languages. Unit testing can be easier with Dependency Injection. 

### Struct vs Class

**Structures** are value types, properties are copied when passed around. **Classes** are reference types.

### Access Levels
S4
5 levels 

* open

* public little more restrictive subclass and override than *open* can only subclass in same module

* internal Default access from any source file in the defining module but not outside

* fileprivate Like private, defines an entity (class, extension, property, ...) as private to everybody outside the source file it is declared in, but accessible to all entities in that source file.

* private more restrictive than fileprivate allows use to any extensions in same source file though as of S4

### Extensions
S4
Add new functionality to an existing class, structure, enumeration, or protocol type even if you don't have the source code.
Can add new functionality to a type, but they cannot override existing functionality. You are not creating a new type but modifying the existing type.
```swift
extension SomeType {
    // new functionality to add to SomeType goes here
}
```
Often used to break the implementation of types up into logical components.

### let and var
let immutable like a constant  
var mutable 

### Optionals

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

### Implicitly unwrapped optionals
If you are sure it will never be nil  
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

### Optional chaining

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

### Subscripts
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

### Functions

Give arg names and types along with return value as shown.

```swift
func bingo(bingoText: String, bingoNumber: Int) -> String {
        return bingoText+" \(bingoNumber)"
}

print(bingo(bingoText: "legs eleven", bingoNumber:11))
```

### Parameters

Parameters are constants see [In-Out Parameters](#in-out-parameters)

Warning there are 2 names used in arguments. **Argument label** - used when calling the function and **Parameter Name** used in the func. Normally just use parameter name as above and argument labels are assumed to be the same. See below for Argument Labels.

```swift
func bingoB(sayThis bingoText: String, bingoNumber: Int) -> String {
    return bingoText+" \(bingoNumber)"
}

print(bingoB(sayThis: "legs eleven", bingoNumber:11)) // legs eleven 11
```

No argument label like other languages, no problem use _ before param name
```swift
func bingoC(_ bingoText: String, bingoNumber: Int) -> String {
    return bingoText+" \(bingoNumber)"
}

print(bingoC("legs eleven", bingoNumber:11)) // legs eleven 11
```

#### Default Parameter Values

No problem same as other languages, keep required params first defaults last
```swift
func bingoD(_ bingoText: String, bingoNumber: Int = 11) -> String {
    return bingoText+" \(bingoNumber)"
}

print(bingoD("legs eleven")) // legs eleven 11
```

#### Variadic Parameters

Param with 0 or more values of a type. You get that param as an array of that type. Use Type...

```swift
func arithmeticMean(_ numbers: Double...) -> Double {
    var total: Double = 0
    for number in numbers {
        total += number
    }
    return total / Double(numbers.count)
}
arithmeticMean(1, 2, 3, 4, 5)
```

#### In-Out Parameters

For a parameter that needs to change, it must be a variable can't be literal or constant obviously. No default values allowed.
Pass & before variable name.

```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}
var someInt = 3
var anotherInt = 107
swapTwoInts(&someInt, &anotherInt)
print("someInt is now \(someInt), and anotherInt is now \(anotherInt)")
// Prints "someInt is now 107, and anotherInt is now 3"
```

#### Function Overloading
Function overloading is fine, if you don't specify a return value void is assumed which is an empty tuple ().

Multiple return values use tuple as return. Optional tuple is also allowed -> (thingA: String, thingB: String)?

```swift
func crud() -> (thingA: String, thingB: String)
{
    return ("peter","paul")
}

var x = crud()
print(x.thingB) // paul
```

#### Function Types

Crazy, by now I'm used to having variables that hold functions from other languages. Here the combination of parameters and return type can be a type. So a varaible can be delclared 
```swift
var mathFunction: (Int, Int) -> Int 
```
and can be assigned any function that matches that type. (takes 2 Ints and returns an Int)

You can used these as parameters or return values from functions.
```swift
// as params
func printMathResult(_ mathFunction: (Int, Int) -> Int, _ a: Int, _ b: Int) {
    print("Result: \(mathFunction(a, b))")
}
printMathResult(addTwoInts, 3, 5)
// Prints "Result: 8"


//or as return type
func stepForward(_ input: Int) -> Int {
    return input + 1
}
func stepBackward(_ input: Int) -> Int {
    return input - 1
}
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    return backward ? stepBackward : stepForward
}
var currentValue = 3
let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
// moveNearerToZero now refers to the stepBackward() function

```

#### Nested Functions
No problem you can do this.

#### Closures

Similar in nature to other languages, used for callbacks or repeats and capture the surrounding variables. They are reference types and can go in Arrays, Dictionarys etc. So careful with memory reference cycles.!!!

```swift
// regular swift function
func changeSign(operand: Double) -> Double { return -operand }
// closure syntax move first { to start and replace with in
var closure = { (operand: Double) -> Double in return -operand }
// can infer return type in this case so no need for -> Double
var closure: (Double) -> Double
closure = { (operand: Double) in return -operand }
// actually also knows operand is double so can remove that
closure = { (operand) in return -operand }
// it knows it returns a value so we can skip the return keyword
closure = { (operand) in -operand }
// you don't need argument names just use $0 for first $1,$2,$3 etc. for 2nd etc.
// no argument names so don't need the 'in' any more either
closure = { -$0 }
```

If the last or only argument to a method is a closure, you can put it outside the method's parentheses that contain it's arguments and if the closure was the only argument you can skip () completely. Best way to show this is Array.map example below.

Array has method map which takes a function as an argument, applies the function to each
element of the array to create and return new array.

```swift
let primes = [2.0, 3.0, 5.0, 7.0, 11.0]
// notice difference in trailing closure syntax below we pass in () like normal
let negativePrimes = primes.map({ -$0 }) // [-2.0, -3.0, -5.0, -7.0, -11.0]
// or put outside
let invertedPrimes = primes.map() { 1.0/$0 } // [0.5, 0.333, 0.2, etc.]
// or put outside and skip the () becasue it's the only argument and it's a closure
let primeStrings = primes.map { String($0) } // [-2.0, -3.0, -5.0, -7.0, -11.0]
```

Execute Closure right away with property initialization - notice the () at end of closure. You can do this to a lazy var and it won't be initialized until someone asks for it.

```swift
var myVar: Type = {
  // do stuff
  return <the value>
}()
```

#### Errors

Throw & Catch a little like exceptions. You have to 'try' but don't have to catch (try! try?). Error protocol is actually empty usually it will be an NSError 

```swift
func save() throws // use throws keyword in function definition
//
do {
      try context.save()
} catch let error {
  // error implements Error protocol usually enums
  throw error // rethrow error, this function must have throws keyword in definition
}
//
try! context.save() // use try! no catch only do this if you are certain there can't be an error or your program will crash
// use try? it will not crash if there is an error but will convert return type into optional in this case an Int?
let x = try? funcThatReturnsInt()
```

### Collections

### Array

### Dictionary

Similar to other languages hashtable, dictionary, etc. unordered key value pairs. Key type must conform to Hashtable protocol and the type of Key and Value is fixed for each dictionary.

Dictionary<Key, Value> shorthand [Key: Value]

```swift
//Shorthand
var bingoNumbers = [Int: String]()
bingoNumbers[11] = "Legs Eleven"
//Longhand
var bingoNumbersB: Dictionary<Int, String> = Dictionary<Int, String>()
bingoNumbersB[11] = "Legs Eleven"
// [:] create empyt dictionary if context already provides type
var bingoNumbersC: Dictionary<Int, String> = [:]
bingoNumbersC[11] = "Legs Eleven"
//
var bingoNumbersD = [11:"Legs Eleven", 22: "Two Little Ducks", 8: "Garden Gate"]
print("bingoNumbersD has \(bingoNumbersD.count) items")
// "bingoNumbersD has 2 items\n"
bingoNumbersD[11]=nil
// removed value
bingoNumbersD[11] = "Legs Eleven"
if let removedValue = bingoNumbersD.removeValue(forKey: 11) {
    print("just removed \(removedValue).")
} else {
    print("nothing there for 11.")
}
// alternative to bingoNumbersD[22]="Two and Two"
if let oldValue = bingoNumbersD.updateValue("Two and Two", forKey: 22) {
    print("old was \(oldValue).")
}
//iterate Dictionary
for (k, v) in bingoNumbersD {
    print ("\(k) -> \(v)")
}
//iterate Dictionary keys
for k in bingoNumbersD.keys {
    print ("\(k)")
}
//iterate Dictionary values
for v in bingoNumbersD.values {
    print ("\(v)")
}
// put keys or values into new array
let keys = [Int](bingoNumbersD.keys)
let values = [String](bingoNumbersD.values)
```

.count and .isEmpty remove value py putting in nil (quick) or removedValue (returns the old value as optional of that type) access and set values by subscript [] (quick) or updateValue (returns old value as optional of that type).

Iterate for (key, value) in, .keys .values gives iterable collection which you can build a new array from or do a .sorted() on.


### Generics

**Generic Functions** that take any (generic) type. Why write the same function for different types when you can write just one. 

Also **Generic Types** classes, structures, enums that can work with any type.

Array , Optionals, and Dictonary are generic types.

Convention to use single letter T instead of a type, if more use U, V or an upper camel case meaningful word.


### Auto Layout and StackViews

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

### Saving Data

### File Structure

Each app in it's own sandbox. 3 main containers.

* Bundle Container - thing.app executable code and resources
* Data Container - Sub folders Documents, Library, Temp
  * Documents for user data and inportant stuff Documents/Inbox
  * Library non user data Library/Appications Suppport Library/Caches files you don't want to expose to the user
  * Temp not going to persists betwen launches
* tmp like Library/Caches but deleted more often
* iCloud Container

### UserDefaults

UserDefaults - If your data is < 1 Meg good for little settings and preferences. (used to be called NSUserDefaults)
Library/Preferences/info.myapp.mobile.plist

UserDefaults.standard gets shared defaults singleton object.
UserDefaults.standard.value(forKey: String)
UserDefaults.standard.float(forKey: String)
UserDefaults.standard.bool(forKey: String)
UserDefaults.standard.set(value: Any?, forKey: String)

```swift
func checkIfFirstLaunch() {
    if UserDefaults.standard.bool(forKey: "hasLaunchedBefore") {
        print("App has launched before")
    } else {
        print("This is the first launch ever!")
        UserDefaults.standard.set(true, forKey: "hasLaunchedBefore")
        UserDefaults.standard.set(false, forKey: "mute")
        UserDefaults.standard.synchronize()
    }
}
```

### Roll Your Own Files

FileManager - get path to sandbox
String to read/write text files Data to read/write binary files

```swift
    let fm = FileManager.default
    let urls = fm.urls(for: .documentDirectory, in: .userDomainMask)
    let url = urls.last?.appendingPathComponent("file.txt")
    do {
        try "Hi There!".write(to: url!, atomically: true, encoding: String.Encoding.utf8)
    } catch {
        print("Error while writing")
    }

    do {
        let content = try String(contentsOf: url!, encoding: String.Encoding.utf8)

        if content == "Hi There!" {
            print("yay")
        } else {
            print("oops")
        }
    } catch {
        print("Something went wrong")
    }
```

### Core Data

Realm may be an alternative but your are at their ransom if they stop developing or have bugs. Bad old days using Adobe Air and not able to publish.

Useful links
[https://github.com/objcio/core-data](https://github.com/objcio/core-data)

Framework to manage your data Data Model (Structure and Relationships) and Related Code is the Data Layer

Alternatives Realm Firebase or use SQLite directly

Saves to *persistent store* 
* SQlite (default)
* binary
* in-memory

Create a data model in core data using the editor

New>File>Data Model creates .xcdatamodeld  
Add and rename your entities in the visual editor graph view (schema) and table view  
Entities are like tables attibutes like fields/columns  
You can set default values  

With **Binary Data Type** you can select 'Allows External Storage' so it will store the binary data as a file and keep a reference to it in DB. Becomes NSData you are responsible for turning that back to whatever it was. Lots of foundation types can be stored as binary data. Any type that conforms to **NSCoding protocol** will work.

With **Transformable** it's like Binary Data but will do the work for you and create correctly typed property. Not tick for 'Allows External Storage' though. Just type in the class under Custom Class.

CodeGen -> Class Definition automatically creates the code, the files are deep in derived data and you shouldn't manually edit them but you can take a peek to see how it all works. So what do we do if we can't mess with this file? *Extension*

Create new file MyModel+Extra.swift
```swift
extension MyModel {
  // all your custom code
}
```

CodeGen -> Category Extension Xcode gen properties file but not class definition so you need to do your own class file but properties will be done for you even if they change. Stored properties can'be be done in an extenstion so this can be the way to go for that.

CodeGen -> Manual/none No code gen, most work, most flexibility

Attributes and relationships start with lowercase

Relationships, graph view ctrl drag to create. Click on the relationship to rename.
Delete Rule-> Cascade kind of obvious children are deleted too, nullify you can delete the item but only the relatonship to the other end is deleted.
Relationship-> obvious 1 to many etc.

#### Core Data Stack

* Managed Object Context(s) - ManagedObjectContext instances scratchpad to create and manipulate when we are done we can roll back or save to permanent store. Changes made while in the context are not saved to disk or update UI. You never instantiate a managed object directly (let Thing=Thing()) you associate with a context (Thing(context:))
* Managed Object Model - Describes attributes and repationships usually defined in data model editor in xcode .xcdatamodeld compiled into a .momd
* Persistant Store Coordinator - Connects to 1 or more persistent stores and uses model and context. It talks to SQL etc. so it's abstracted away from the dev.
* Persistent Container - Introduced iOS10 to reduce boildplate to set up stack. Helps set up stack and has methods and vars to work with contexts

Write this little utility to help make things easier and share it to your viewcontroller. It will be expanded on later in my notes.

DataController.swift
```swift
import Foundation
import CoreData

class DataController
{
    let persistentContainer:NSPersistentContainer
    
    /*
     Computed property to access view context
    */
    var viewContext:NSManagedObjectContext {
        return persistentContainer.viewContext
    }
    
    init(modelName:String){
        persistentContainer=NSPersistentContainer(name:modelName)
    }
    
    /*
    Load the persistent store, Takes optional completion closure
    Uses Trailing Closure syntax hence odd looking code
    */
    func load(completion:(()->Void)?=nil){
        persistentContainer.loadPersistentStores { storeDescription, error in
            guard error == nil else {
                fatalError(error!.localizedDescription)
            }
            completion?()
        }
    }
    
}
```

In your first view 
```swift
var dataController:DataController
```

In your AppDelegate you will inject data dependency into your first view controller
```swift
    // MyModel is name of .xcdatamodeld
    let dataContoler=DataController(modelName: "MyModel")

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
        // trailing closure syntax
        dataController.load {
            //called once persistant store loaded
            // show loading interface while loading then switch to main UI
        }
        //configure the first view, lets get at it
        let navigationController = window?.rootViewController as! UINavigationController
        let firstViewController=navigationController.topViewController as! FirstViewController
        firstViewController.dataController=dataController
        return true
    }
```

Fetch Request is like SELECT Predicate like WHERE SortDesciptors like ORDER BY
Fetch data from persistant store into context

```swift
  var myEntitys:[MyEntity]=[]
  let fetchRequest:NSFetchRequest<MyEntity> = MyEntity.fetchRequest()
  let sortDescriptor = NSSortDescriptor(key:"creationDate",ascending:false)
  fetchRequest.sortDescriptors=[sortDescriptor]
  if let result = try? dataController.viewContext.fetch(fetchRequest){
      myEntitys=result
      tableView.reloadData()
  }
```

Make changes in a context and ask to make changes to persistent store

```swift
  let myEntity = MyEntity(context: dataController.viewContext)
  myEntity.name = name
  myEntity.creationDate = Date()
  try? dataController.viewContext.save()
  myEntitys.insert(myEntity, at:0)
```

Delete

```swift
  let entityToDelete = myEntitys[index]
  dataController.viewContext.delete(entityToDelete)
  try? dataController.viewContext.save()
```

Saving can cause a user perceptable delay in the app, if you aren't sure if there are changes to save check dataController.viewContext.hasChanges or write NSManagedObject extension to check .hasChanges and manage save errors.

Save in AppDelegate when it enters background or terminates. 

```swift
  func applicationDidEnterBackground(_ application: UIApplication) {
      saveViewContext()
  }

  func applicationWillTerminate(_ application: UIApplication) {
      saveViewContext()
  }

  func saveViewContext() {
      try? dataController.viewContext.save()
  }
```

How about Autosave if they are typing in text

```swift
extension DataController {
    func autoSaveViewContext(interval:TimeInterval = 30) {
        print("autosaving")
        guard interval > 0 else
        {
            print("negative autosave interval? think again")
            return
        }
        if viewContext.hasChanges {
             try? viewContext.save()
        }
        DispatchQueue.main.asyncAfter(deadline: .now()+interval) {
            self.autoSaveViewContext(interval: interval)
        }
       
    }
}
```

Faulting - Relationships aren't loaded by default just loaded when needed. You can manage faulting and uniquing yourself to fault something you already loaded to save memory or to load more initially so it doesn't need to load on demand later.

Example if we have a parent with one to many relationship to children we could fetch the parent and the choldren would be faulted hence loaded when needed. Or we could use another fetch to get the children and use a predicate to only get the children of a particular parent.

```swift
  var children:[ChildClass]=[]
  let fetchRequest:NSFetchRequest<ChildClass> = ChildClass.fetchRequest()
  let predicate = NSPredicate(format: "parent == %@", parent)
  fetchRequest.predicate = predicate
  let sortDescriptor = NSSortDescriptor(key:"creationDate",ascending:false)
  fetchRequest.sortDescriptors=[sortDescriptor]
  if let result = try? dataController.viewContext.fetch(fetchRequest){
    children=result
    tableView.reloadData()
  }
```

You need a reference to the managedObjectContext to save etc. we are using a this dataController variable we inject all the time but you can also get a context from the managed object. Managed objects hold a ref to their context so myThing.managedObjectContext?.save() you may be working with multiple contexts.

NSManagedObject has lifecycle methods you can override. wilSave() prepareForDeletion() etc.   
awakeFromInsert() you can set your object up when it's created. Remember you don't want to mess with the generated code so write and extension. To be organised make a new group/folder 'Managed Object Extensions' probably stick it in models group/folder then put your MyManagedObject+Extensions.swift files in there for each managed object you want to do it with.

```swift
import CoreData

extension MyManagedObject {
    public override func awakeFromInsert() {
        super.awakeFromInsert()
        creationDate = Date()
        otherStuff = "whatever man"
    }
}
```

### Fetched Results Controller

Saves a lot of repeat code you want to use this if you use core data so the UI updates when the data changes.

Sits between views and model, listens for changes to model and triggers logic to update views.

Recap - Every managed object instance is registered to a (managed object) context.

Whenver a managed object changes or the context is saved Core Data automatically generates notifications. Like NSManagedObject ContextObjectDidChange.

You could use NotificationCenter to subscribe to core data notifications but more convenient to use FetchedResultsController because they work well with table views and collection views.

Note. FetchedResultsController fetchRequest must be sorted so it has a consistent ordering. Set up implicitly unwrapped NSFetchedResultsController with your managed object type. Set this up in viewDidLoad or viewWillAppear with a fetchRequest. Tear it down in viewWillDisappear. Set self as it's delegate.

MyViewController.swift needs to conform to NSFetchedResultsControllerDelegate
```swift
class MyViewController: UIViewController, UITableViewDataSource, NSFetchedResultsControllerDelegate {

  var fetchedResultsController:NSFetchedResultsController<MyManagedObject>!

  override func viewDidDisappear(_ animated: Bool) {
          super.viewDidDisappear(animated)
          // tear it down
          fetchedResultsController = nil
      }
      
  override func viewDidLoad() {
        super.viewDidLoad()
        setUpFetchedResultsController()
    }
    
  fileprivate func setUpFetchedResultsController() {
        let fetchRequest:NSFetchRequest<MyManagedObject> = MyManagedObject.fetchRequest()
        let sortDescriptor = NSSortDescriptor(key:"creationDate",ascending:false)
        fetchRequest.sortDescriptors=[sortDescriptor]
        fetchedResultsController = NSFetchedResultsController(fetchRequest: fetchRequest, managedObjectContext: dataController.viewContext, sectionNameKeyPath: nil, cacheName: "myCache")
        fetchedResultsController.delegate=self
        
        do {
            try fetchedResultsController.performFetch()
        } catch {
            print("fetchedResultsController could not fetch: \(error.localizedDescription)")
        }
    }
}
```

You use an Array as a data source to your TableView often when hacking. Use a fetchedResultsController instead.  

FetchedResultsController can have multliple sections fetchedResultsController.sections?.count for how many. Each section is of type SectionInfo fetchedResultsController.sections?[section].numberOfObjects fetchedResultsController.object(at: indexPath)

So remove your array if you were using it and do it the fetchedResultsController way!

```swift
func numberOfSections(in tableView: UITableView) -> Int {
        return fetchedResultsController.sections?.count ?? 1
    }
    
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return fetchedResultsController.sections?[section].numberOfObjects ?? 0
    }
    
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let myObject = fetchedResultsController.object(at: indexPath)
        let cell = tableView.dequeueReusableCell(withIdentifier: MyCell.defaultReuseIdentifier, for: indexPath) as! MyCell

        // Configure cell
        cell.nameLabel.text = myObject.name
        return cell
    }
    
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
        if let vc = segue.destination as? SubViewController {
            if let indexPath = tableView.indexPathForSelectedRow {
                vc.myManagedObject = fetchedResultsController.object(at: indexPath)
            }
            //
            vc.dataController=self.dataController
        }
    }
    
    func addThing(name: String) {
        let myManagedObject = ManagedObject(context: dataController.viewContext)
        myManagedObject.name = name
        myManagedObject.creationDate = Date()
        try? dataController.viewContext.save()
    }
    
    func deleteThing(at indexPath: IndexPath) {
        let myManagedObjectToDelete = fetchedResultsController.object(at: indexPath)
        dataController.viewContext.delete(myManagedObjectToDelete)
        try? dataController.viewContext.save()
    }
```

Organise into an extension. 

```swift
extension MyViewController:NSFetchedResultsControllerDelegate {
    
    func controllerWillChangeContent(_ controller: NSFetchedResultsController<NSFetchRequestResult>) {
        tableView.beginUpdates()
    }
    
    func controllerDidChangeContent(_ controller: NSFetchedResultsController<NSFetchRequestResult>) {
        tableView.endUpdates()
    }
    
    func controller(_ controller: NSFetchedResultsController<NSFetchRequestResult>, didChange anObject: Any, at indexPath: IndexPath?, for type: NSFetchedResultsChangeType, newIndexPath: IndexPath?) {
        switch type {
        case .insert:
            tableView.insertRows(at: [newIndexPath!], with: .fade)
        case .delete:
            tableView.deleteRows(at: [indexPath!], with: .fade)
        case .update:
            tableView.reloadRows(at: [indexPath!], with: .fade)
        case .move:
            tableView.moveRow(at: indexPath!, to: newIndexPath!)
        }
    }
}
```

NSFetchedResultsChangeType enum insert,delete,move,update

Section changes? controller(_:didChange:atSectionIndex:for:) delegate method
```swift
func controller(_ controller: NSFetchedResultsController<NSFetchRequestResult>, didChange sectionInfo: NSFetchedResultsSectionInfo, atSectionIndex sectionIndex: Int, for type: NSFetchedResultsChangeType) {
    let indexSet = IndexSet(integer: sectionIndex)
    switch type {
    case .insert: tableView.insertSections(indexSet, with: .fade)
    case .delete: tableView.deleteSections(indexSet, with: .fade)
    case .update, .move:
        fatalError("Invalid change type in controller(_:didChange:atSectionIndex:for:). Only .insert or .delete should be possible.")
    }
}
```

#### Notification Center

Use notification center to find out about changes and update UI

```swift
class MyViewController: UIViewController {
  
  var myManagedObject: MyManagedObject!
  var dataController:DataController!
  var saveObserverToken: Any?
  
  override func viewDidLoad() {
        super.viewDidLoad()
        addSaveNotificationObserver()
    }
    
    deinit {
        removeSaveNotificationObserver()
    }

}


extension MyViewController {
    func addSaveNotificationObserver() {
        removeSaveNotificationObserver()
        saveObserverToken = NotificationCenter.default.addObserver(forName: .NSManagedObjectContextObjectsDidChange, object: dataController.viewContext, queue: nil, using: handleSaveNotification(notification:))
    }
    
    func removeSaveNotificationObserver() {
        if let token = saveObserverToken {
            NotificationCenter.default.removeObserver(token)
        }
    }
    
    fileprivate func reloadUI() {
        myUIElement.whatever = myManagedObject.whatever
    }
    
    func handleSaveNotification(notification:Notification) {
        //dump info about what changed use this to only update UI that needs it
        dump(notification.userInfo)
        DispatchQueue.main.async {
            self.reloadUI()
        }
    }
    
}
```

#### Caching

Kind of automatic and free, multiple fetchedResultsControllers will need multiple caches with unique names.
```swift
fetchedResultsController = NSFetchedResultsController(fetchRequest: fetchRequest, managedObjectContext: dataController.viewContext, sectionNameKeyPath: nil, cacheName: "myCache")
```

Delete cache manually - You'll need to do this if you change the fetch request in a FetchedResultsController
```swift
NSFetchedResultsController<MyManagedObject>.deleteCache(withName:"myCacheForMyManagedObject")
```

#### Concurrency

Each ManagedObjectContext is associated with a specific queue, ViewContext is associated with the main queue. 

Users are very sensitive to lag especially when it's something they've not initiated. 

You can have multiple ViewContexts connected to the same PersistentStoreCoordinator. In this setup the ViewContexts have seperate versions of the managedObjects they contain. Changes in one aren't reflected in the other. You can listen for change notifications in one context and merge those changes into the other context if we want full control.

We can set this up to happen automatically viewContext.automaticallyMergeChangesFromParent = true

Parent can be a persistent store coordinator you have as a parent for multiple contexts or you can set up child context from a parent context. 

For concurrency a good choice is to have a main context and a background context connected to same PersistentStoreCoordinator.

NSPersistentContainer.newBackgroundContext() will create a background context that you can use repeatedly.

NSPersistentContainer.performBackgroundTask with a closure {do stuff then context.save()} will create a temporary backgroundContext which can perform better than a long lived newBackgroundContext()

ViewContext.perform {} will do something async on correct queue for that context.
ViewContext.performAnd Wait {} will do something sync on correct queue for that context. 

UI not thread safe so don't do any UI stuff in there, even read only stuff.

Don't do viewContext stuff in there either. Say you have a managedObject instance associated with a viewContext you can't use that in the background. You need another instance of it, every managed object has an ID consistent over contexts. So store myManagedObject.objectID and use that to get another instance in the backgroundContext.object(with:theID)

DataController.swift
```swift
class DataController
{
    let persistentContainer:NSPersistentContainer
  
    var viewContext:NSManagedObjectContext {
        return persistentContainer.viewContext
    }
    
    var backgroundContext:NSManagedObjectContext!
    
    init(modelName:String){
        persistentContainer=NSPersistentContainer(name:modelName)
    }
    
    func configureContexts() {
        backgroundContext = persistentContainer.newBackgroundContext()
        
        viewContext.automaticallyMergesChangesFromParent = true
        backgroundContext.automaticallyMergesChangesFromParent = true
        
        backgroundContext.mergePolicy = NSMergePolicy.mergeByPropertyObjectTrump
        viewContext.mergePolicy = NSMergePolicy.mergeByPropertyStoreTrump
    }
   
    func load(completion:(()->Void)?=nil){
        persistentContainer.loadPersistentStores { storeDescription, error in
            guard error == nil else {
                fatalError(error!.localizedDescription)
            }
            self.autoSaveViewContext()
            self.configureContexts()
            completion?()
        }
    }
}

```

YourViewController.swift
```swift
  let backgroundContext:NSManagedObjectContext! = dataController.backgroundContext

  let backgroundManagedObjectID = managedObject.objectID

  backgroundContext.perform {
      let backgroundManagedObject = backgroundContext.object(with: noteID) as! MyManagedObject
      
      //delay to test concurrency code replace with doing stuff
      sleep(5)

      backgroundManagedObject.attribute = newValue
      try? backgroundContext.save()
  }
```

Always wrap context's tasks in a perform call.

Wrong queue bug is hard to find may or may not crash the app. Add this to crash everytime you use the wrong queue

Product>Scheme>Edit Scheme>Run Debug>Arg passed on launch> add -com.apple.CoreData.ConcurrencyDebug 1

(change 1 to 2 or 3 for more verbose output)

Merge Conflicts can cause issues so we need a merge policy to handle merge conflicts. Context has merge policy property of type NSMergePolicy defaults to errorMergePolicyType which is why it will crash.

PropertyStoreTrump - property changes from peristentStore trump prop changes in the context.
PropertyObjectTrump - state of property in context trumps state in store.

MergeByPropertyStoreTrump - 
MergeByPropertyObjectTrump -

#### Migration

Check up to date documentation but here are some basics.

Change your model and the managed object model will be different from the one stored in persistent store container which is an unholy mess. Any changes to data model will invalidate everything, in development you can just delete the app but in the field with users having your old data format you need to migrate.

**Version your data model** select your data model in XCode in editor 'Add Model Version'. You can see the versions in the project navigator little green tick for the one being used. You can change this in file inspector under Model Version.

Migrations can be Automatic (lightweight) or Manual (Custom). Automatic is default and it will do it's best to infer mapping of entities, attributes, and relationships to the new model. Manual you suppy mapping file explaining how to map from one version to another. You can make and array of mapping files to convert data in batches.

Manual - New file 'Mapping Model' (in core data section) select source and destination data model .xcdatamodel and name the mapping file something descriptive.

You can work with the visual editor and also write policy files. Create a subclass of NSEntityMigrationPolicy override createDestinationInstances 

```swift
import UIKit
import CoreData

class MyMigrationForWhatver: NSEntityMigrationPolicy {

    override func createDestinationInstances(forSource sInstance: NSManagedObject, in mapping: NSEntityMapping, manager: NSMigrationManager) throws {
        // Call super
        try super.createDestinationInstances(forSource: sInstance, in: mapping, manager: manager)

        // Get the (updated) destination entity instance we're modifying
        guard let destination = manager.destinationInstances(forEntityMappingName: mapping.name, sourceInstances: [sInstance]).first else { return }

        // Use the (original) source entity instance, and instantiate a new
        // NSAttributedString using the original string
        if let myField = sInstance.value(forKey: "myOldField") as? String {
            destination.setValue(WhateverNewClass(string: text), forKey: "myNewField")
        }
    }
}
```

Then reference that class with namespace in Custom Policy in Entity Mapping.

### Predicates

Like SQL and Regex to search Arrays, Collections, Core Data
Use = or == they are the same can use BEGINSWITH, ENDSWITH and CONTAINS

```swift
//NSPredicate(format: "attribute = %@", someValue)
let names: [NSString] = [ "Bobby", "Alex", "Jack" ]
let predicate = NSPredicate(format: "length = %d", 4)
let shortNames = (names as NSArray).filtered(using: predicate) // "Alex", "Jack"
```

Easier to explain using code

```swift
// A Person!
class Person: NSObject {
    @objc var name: String
    @objc var age: Int
    
    init(name: String, age: Int) {
        self.name = name
        self.age = age
        super.init()
    }
}

let people = [ Person(name: "Tray", age:12), Person(name:"May", age:15), Person(name:"Teresa", age:23) ]

let predicate1 = NSPredicate(format: "name LIKE %@", "?ay") // wildcard query for a word with “a” and “y” as the 2nd and 3rd letters
let predicate2 = NSPredicate(format: "name LIKE %@", "T*") // wildcard query for names starting with “T”
let predicate3 = NSPredicate(format: "age < 20")
let predicate4 = NSPredicate(format: "name LIKE[c] %@", "t*")  // [c] makes case insensitive [d] ignore diacritics (accents etc)
let predicate5 = NSPredicate(format: "%K = %d", "age", 23) // %K is attribute name or key path age == 23
let predicate6 = NSPredicate(format: "age BETWEEN {15, 55}")
let predicate7 = NSPredicate(format:"(age < 23) AND (name LIKE 'T*')")
let predicate8 = NSPredicate(format:"age < 23")
let compoundPredicate = NSCompoundPredicate(andPredicateWithSubpredicates: [predicate2, predicate8]) // name LIKE "T*" AND age < 23

let result1 = (people as NSArray).filtered(using: predicate1) // May
let result2 = (people as NSArray).filtered(using: predicate2) // Tray, Teresa
let result3 = (people as NSArray).filtered(using: predicate3) // Tray, May
let result4 = (people as NSArray).filtered(using: predicate4) // Tray, Teresa
let result5 = (people as NSArray).filtered(using: predicate5) // Teresa
let result6 = (people as NSArray).filtered(using: predicate6) // May, Teresa
let result7 = (people as NSArray).filtered(using: predicate7) // Tray

let result8 = (people as NSArray).filtered(using: compoundPredicate) // Tray
```

### Debug Autolayout

breakpoint UIViewAlertForUnsatisfiableConstraints
po [[UIWindow keyWindow] _autolayoutTrace] 

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

If it gets messed up 
```
pod deintegrate
pod update
```

