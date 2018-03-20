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
    - [Implicitly unwrapped optionals](#implicitly-unwrapped-optionals)
    - [Optional chaining](#optional-chaining)
  - [Subscripts](#subscripts)
  - [Auto Layout and StackViews](#auto-layout-and-stackViews)
  - [Saving Data](#saving-data)
    - [File Structure](#file-structure)
    - [UserDefaults](#userdefaults)
    - [Roll Your Own Files](#roll-your-own-files)
    - [Core Data](#core-data)
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

Useful links
[https://github.com/objcio/core-data](https://github.com/objcio/core-data)

Framework to manage your data Data Model (Structure and Relationships) and Related Code is the Data Layer

Alternatives Realm Firebase or use SQLite directly

Saves to *persistent store* 
* SQlite (default)
* binary
* in-memory

New>File>Data Model creates .xcdatamodeld
Add and rename your entities in the visual editor graph view (schema) and table view
Entities are like tables attibutes like fields/columns

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

Core Data Stack

* Managed Object Context(s) - ManagedObjectContext instances scratchpad to create and manipulate when we are done we can roll back or save to permanent store. Changes made while in the context are not saved to disk or update UI. You never instantiate a managed object directly (let Thing=Thing()) you associate with a context (Thing(context:))
* Managed Object Model - Describes attributes and repationships usually defined in data model editor in xcode .xcdatamodeld compiled into a .momd
* Persistant Store Coordinator - Connects to 1 or more persistent stores and used model and context. It talks to SQL etc. so it's abstracted away from the dev.
* Persistent Container - Introduced iOS10 to reduce boildplate to set up stack. Helps set up stack and has methods and vars to work with contexts

Write this little utility to help make things easier and share it to your viewcontroller.

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

Saving can cause a pause in the app, if you aren't sure if there are changes to save check dataController.viewContext.hasChanges or write NSManagedObject extension to check .hasChanges and mande save errors.

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
