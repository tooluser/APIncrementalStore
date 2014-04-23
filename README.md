APIncrementalStore
==================

Cutting the long story short, after StackMob [went to hell](https://blog.stackmob.com/2014/02/stackmob-announcement/) and all my development based on that SDK dragged along with it, I decided to implement my own `NSIncrementalStore` subclass.
Yes I have looked around for alternatives and even taking into account what is available, none of them are at the moment capable of supporting how I had designed my app.
I need an implementation that works most of time offline and sync with the backend BaaS when internet is available, which is quite the opposite of the APIs that I had found.
Special thanks to the great Mattt Thompson for the inspiring [AFIncrementalStore] (https://github.com/AFNetworking/AFIncrementalStore).
The code is based on Apple guidelines for [IncrementalStore Programming Guide] (https://developer.apple.com/library/mac/documentation/DataManagement/Conceptual/IncrementalStorePG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40010706)

There are basically three main classes:

1) `APIncrementalStore` - this is the `NSIncrementalStore` subclass that implements what is required to handle the Core Data context.

2) `APDiskCache` - the APIncrementalStore uses this class as a local cache to respond to all the requests. This class exchanges NSDictionaries representations of the managed objects and uses a objectID to uniquely identify them across the managed contexts (`NSIncrementalStore` and Disk Cache). This class uses a complete core data stack to store the cached objects. There will be one sqlite store for each logged user, so that on distinct cache per user.

3) `APParseConnector` - Responsible for merging the local cache context in background as requested by the cache. Please be mindful that relationships are being represented as Parse Pointers for Core Data To-One and Parse Relationships for Core Data To-Many relationships. All relationships have its inverse reflected at Parse as well for consistency sake. At the moment this class does not support Parse Array Relationships. 

I will include descent documentation in the next weeks, for the time being take a look at the folder Example in the repository, you are going to find a very basic usage of this library.

Well, that's it, sorry about the short README and the English as it's not my first idiom as you have probably noticed at this point.

###Installation
Easy, you may clone it locally or use CocoaPods:

```
platform :ios, '7.0'
pod 'APIncrementalStore' , :git => 'https://github.com/flavionegrao/APIncrementalStore.git'
```

### Run the example app

1) Download the project

2) Create a Parse account and a test App you may name it whatever you want.

3) On your new test Parse App create a class User and add a new object to it with username:test_user and password:1234

4) Navigate to the *Example* project directory in the Terminal

5) Run the following command: `pod update`  - [Install cocoapods](http://guides.cocoapods.org/using/getting-started.html#getting-started) if you don't have it.

6) Open `APIncrementalStore.xcworkspace` in Xcode

7) Navigate to the *Example* project's `AppDelegate.m` file and set the `APParsepApplicationId` and `APParseClientKey`

8) You may run it on multiple devices (iOS Simulator + real iOS devices) to check to syncronization process

###Few tips when using the library:
- Don't forget to set Parse keys
- Login with an user (`PFUser`) and pass it as parameter to `APIncrementalStore`.
- `APParseConnector` will sync all entities found on your model and use the exactly same entity names to find the classes from Parse.
- I have done nothing in regards to Parse ACL yet, so that it will sync everything that the logged user has access to.

###Unit Testing
On `UnitTestingCommon.m` config a valid Parse User/Password.
Use a test Parse App as it will include few additional classes needed for testing. 


Cheers. Flavio


