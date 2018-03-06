## About

Notes on iOS dev. S4 means swift 4

## Table of contents

- [About](#about)
- X Code IDE
  - [](#x)
  - [Pods](#pods)



##### Format code

Little hack to auto reformat code

Select All, CopyCut, Paste

##### Access Levels
S4
5 levels 

* open

* public little more restrictive subclass and override than *open* can only subclass in same module

* internal Default access from any source file in the defining module but not outside

* fileprivate Like private, defines an entity (class, extension, property, ...) as private to everybody outside the source file it is declared in, but accessible to all entities in that source file.

* private more restrictive than fileprivate allows use to any extensions in same source file though as of S4

##### Extensions
S4
Add new functionality to an existing class, structure, enumeration, or protocol type even if you don't have the source code.
Can add new functionality to a type, but they cannot override existing functionality. You are not creating a new type but modifying the existing type.
```swift
extension SomeType {
    // new functionality to add to SomeType goes here
}
```
Often used to break the implementation of types up into logical components.

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
