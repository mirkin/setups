## About

Notes on iOS dev

## Table of contents

- [About](#about)
- X Code IDE
  - [](#x)
  - [Pods](#pods)



##### Format code

Little hack to auto reformat code

Select All, CopyCut, Paste

### Pods

#### Install
Setup clones the [CocoaPods Master Specs](https://github.com/CocoaPods/Specs) repository into ~/.cocoapods/
```sh
sudo gem install cocoapods
pod setup --verbose
```


#### Overview
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

Change it to 

```
platform :ios, '11.2'

target 'YourProject' do
  use_frameworks!
end
```
