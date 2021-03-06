# JNI Tutorial (2018)
###### A list of steps on how to run a C/C++ function from a Java Program on MacOS Sierra. The example files for ``HelloWorld.java``, ``HelloWorld.h``, and ``HelloWorld.cpp`` are provided.


## Steps
###### There are several steps to this tutorial and are as follows:
1. Creating the Java Class
2. Compiling the Java Class
3. Creating the C++ code
4. Compiling the C++ code
5. Compiling the Java Program with the Native Library 
6. Using the Shell Script

### Java Program
Create a new file HelloWorld.java:
```
class HelloWorld {
    private native void print();
    public static void main(String[] args) {
        new HelloWorld().print();
    }
    static {
        System.loadLibrary("HelloWorld");
    }
}
```

### Compiling the Java Class
To compile the Java Class, run the command:
```
$ javac HelloWorld.java
```
To generate the header file for the C++ code, run the command: 
```
$ javah -jni HelloWorld
```
This will output the following file HelloWorld.h:
```
/* DO NOT EDIT THIS FILE - it is machine generated */
#include <jni.h>
/* Header for class HelloWorld */

#ifndef _Included_HelloWorld
#define _Included_HelloWorld
#ifdef __cplusplus
extern "C" {
#endif
/*
 * Class:     HelloWorld
 * Method:    print
 * Signature: ()V
 */
JNIEXPORT void JNICALL Java_HelloWorld_print
    (JNIEnv *, jobject);

#ifdef __cplusplus
}
#endif
#endif
```

### C++ Program
Create a new file HelloWorld.cpp:
```
#include <jni.h>
#include <iostream>
#include "HelloWorld.h"
using namespace std;

JNIEXPORT void JNICALL 
Java_HelloWorld_print(JNIEnv *, jobject){
    cout << "Hello World!\n";
    return;
}
```

### Compiling the C++ Code
Compiling the C++ Code to include the jni.h library is going to be the most difficult part of this process. 

To start, run the command:
```
$ export JAVA_HOME="$(/usr/libexec/java_home -v 1.8)"
```
and then:

```
$ g++ -I"$JAVA_HOME/include" -I"$JAVA_HOME/include/darwin/" -o HelloWorld.o -shared HelloWorld.cpp
```
This will create the file HelloWorld.o. Finally run:
```
$ g++ -dynamiclib -o libhelloworld.jnilib HelloWorld.o
```

### Compiling the Java Program with the Native Library
Finally, to run the Java Program, run:
```
$ java HelloWorld
```

The ouput should be:
```
Hello World!
```

### Using the Shell Script
The shell script simply runs all of these commands:
```
#!/bin/bash

javac Helloworld.java
javah -jni HelloWorld
export JAVA_HOME="$(/usr/libexec/java_home -v 1.8)"
g++ -I"$JAVA_HOME/include" -I"$JAVA_HOME/include/darwin/" -o HelloWorld.o -shared HelloWorld.cpp
g++ -dynamiclib -o libhelloworld.jnilib HelloWorld.o
java HelloWorld
```
Running the provided shell script with the command:
```
$ ./build.sh
```
will complete all of these steps and run the HelloWorld.java program. If permissions are denied, you may have to run:
```
$ chmod -x build.sh
```
Like running the java executable, running this script will output:
```
Hello World!
```






## Credits
###### I did not come up with this tutorial by myself. I compiled it from several other pages to work on the current version of MacOS:
* http://mrjoelkemp.com/2012/01/getting-started-with-jni-and-c-on-osx-lion/
* https://gist.github.com/DmitrySoshnikov/8b1599a5197b5469c8cc07025f600fdb
