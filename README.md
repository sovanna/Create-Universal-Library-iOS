# Universal Library iOS

This project is about to create your own **framework** as Apple does with UIKit, Foundation and so on.

You can check also this tutorial <http://spin.atomicobject.com/2011/12/13/building-a-universal-framework-for-ios/> which was helpful to me.
In this tutorial, I used some of his explanation that I can't explain because he's made already an awesome work.

Here I cover some other stuff like **how to use it**, **add others Classes** (we're going to make an **'hello world'** with NSLog). So, I removed what I didn't need and did some clean up of the tutorial above.

## Requirements

This project was build with xCode 4.3.1 and runs on iOS 5.0 and above.
The framework is universal, it means it works on device (armv6, armv7) and simulator (i386)

## Tutorial

### Start
1. create a new project in xCode (Cmd + Maj + N)
2. choose iOS/Framework & Library/ and select **"Cocoa Touch Static Library"**
3. click Next
4. give it a name (I chose for this tuto : **SHelloWorldLibraryIOS**)
5. choose whatever you want in Company Identifier (com.sovanna for me)
6. don't check "Include Unit Tests" (we don't need it here)
7. check "Use Automatic Reference Counting"
8. click next

### Configurations of the architectures

As mention above, the framework has to work on simulator and old devices. So,

1. click on the target 'SHelloWorldLibraryIOS'
2. click on architectures, select 'other'
![01](http://www.sovannart.com/github/universal-library-iOS/img/01.png)
3. add armv6, i386
![02](http://www.sovannart.com/github/universal-library-iOS/img/02.png)

### Define the main target

Here, we're going to prepare the **'Aggregate target'** 

The aggregate target helps to combine all script, other targets together that we're going to add.

1. click on the button above 'add target'
2. select 'Aggregate' and click next
3. choose a name (I chose 'libHelloWorld')
![03](http://www.sovannart.com/github/universal-library-iOS/img/03.png)

### Build static library

'Under the “Build Phases” section of the aggregate target we are going to add a build phase that compiles a static library for i386 and ARM. In the next step we’ll merge the binaries into a fat binary.'

1. Select "libHelloWorld" target
2. Select “Build Phases”
3. Click “Add Build Phase”
4. Select “Add Run Script”
5. Name it “Build Static Library”
![04](http://www.sovannart.com/github/universal-library-iOS/img/04.png)

6. Then add the follow script (Thx to dewind)

<script src="https://gist.github.com/1472207.js"> </script>

### Build universal library

We're going to add another script that builds the framework.

1. add another "Build Phases"
2. rename it to "Build Universal Binary"
3. add finally add the follow code

<script src="https://gist.github.com/1472295.js"> </script>

### Add Header files in the framework to make it visible

If you look in the foundation.framework when you create an xCode project, you can see the headers files. It can be great if we can do that too.
So,

1. click on "Add build phase"
2. select "add copy files"
3. set "destination" to "absolute path"
4. rename it to **"Headers"**
5. set the subpath to

````

${BUILD_DIR}/${CONFIGURATION}-iphoneuniversal/${PRODUCT_NAME}.framework/Versions/A/Headers/
````

To add headers file, just clic on "+" and add files you want (we're going to make an example in few moments^^)

### To add resources, just do the same things as Headers files.

1. click on "Add build phase"
2. select "add copy files"
3. set "destination" to "absolute path"
4. rename it to **"Resources"**
5. set the subpath to

````

${BUILD_DIR}/${CONFIGURATION}-iphoneuniversal/${PRODUCT_NAME}.framework/Versions/A/Resources/
````

### Test build the library

We have to make a little configuration in the aggregate target.

1. click on the menu Product / Edit Scheme or click on the scheme tools beside de stop button.
2. select the scheme that correspond to the lib, here 'libHelloWorld'
3. choose "run"
4. set the "build configuration" to "Release"
5. click "ok"

### BUILD
do cmd + B to build the project.

in the project tree, select the folder named "Products", the lib file which was "red" have to turn now in "black" color => just saying the build was successful! nice!

right click on it, select "show in Finder"

to me, it shows the **libSHelloWorldLibraryIOS.a** in the folder **Products/Release-iphoneos**.
Don't panic, the true framework is on the folder **Products/Release-iphoneuniversal**.

## Add HelloWord to the framework

Now we can add one method to test it in our framework

1. select SHelloWorldLibraryIOS.h (or any other header file)
2. add a method description `- (void)testFromMyFramework;`
3. add the correct implementation in (the .m) file


````
- (void)testFromMyFramework
{
    NSLog(@"Hello World from my own Framework! cool!");
}
````

Don't forget to add the header file in the target build phases

![05](http://www.sovannart.com/github/universal-library-iOS/img/05.png)


=> and build again


## TEST your framework with an other project

1. create a new xcode project
2. select an iOS Application / Single View Application
3. name it "TestHelloWorldLib"
4. click on next
5. go back to the xcode project framework
6. select the lib .a file in the Products folder
7. right click on, and select "Show in Finder"
8. select the folder **libHelloWorld.framework** in "Products/Release-iphoneuniversal"
9. drag this folder in the xcode project just create before (TestHelloWorldLib),
10. put it in the "Frameworks" folder as others.

you have to see this

![06](http://www.sovannart.com/github/universal-library-iOS/img/06.png)

Now in the viewController.m, add the import line to call the framework

`#import <libHelloWorld/SHelloWorldLibraryIOS.h>`

in `-(void) viewDidLoad`, create an instance of the object and call the test method.

```
- (void)viewDidLoad
{
    [super viewDidLoad];
    
    SHelloWorldLibraryIOS *test = [[SHelloWorldLibraryIOS alloc] init];
    [test testFromMyFramework];
    
	// Do any additional setup after loading the view, typically from a nib.
    
}
```

Run (cmd + R)

and in the console, you have to see something like this

![07](http://www.sovannart.com/github/universal-library-iOS/img/07.png)

## Conclusion

That's it, in practice, it can be done more quickly that it seems.
But now, you know how to create and use your own framework. It means, you can do some Classes that you can re-use it and build project more easily I hope.
