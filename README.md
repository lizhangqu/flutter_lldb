# flutter_lldb
flutter engine remote debug with lldb

See [Flutter Engine C++ 源码调试初探](https://fucknmb.com/2019/12/06/Flutter-Engine-C-%E6%BA%90%E7%A0%81%E8%B0%83%E8%AF%95%E5%88%9D%E6%8E%A2/)

# how to use

1、 clone this repository

```
git clone https://github.com/lizhangqu/flutter_lldb.git
```

2、 build your local engine 

 - [Flutter Engine 编译指北](https://fucknmb.com/2019/02/26/Flutter-Engine-%E7%BC%96%E8%AF%91%E6%8C%87%E5%8C%97/)
 - [Compiling-the-engine](https://github.com/flutter/flutter/wiki/Compiling-the-engine)

3、 build your debuggable apk with the built local engine

```
/path/to/flutter build apk \
--debug \
--target-platform=android-arm \
--local-engine-src-path=/Users/lizhangqu/software/flutter_dev/engine/src \
--local-engine=android_debug_unopt
```

4、 install the built apk

```
adb install /path/to/sample.apk
```

5、 launch the built apk

You can use `monkey` command to run apk directly.

```
adb shell monkey -p com.example.package_name -v 1
```

6、run lldb-server in remote device with flutter_lldb

You must set environment named `ANDROID_HOME` or provide the android sdk home with --android-sdk

`[]` in the command is optional and the value is default. android-sdk default use the environment named `ANDROID_HOME`

The package name parameter is required.

Now run `flutter_lldb`

```
./flutter_lldb \
[--android-sdk /path/to/androidSdk] \
[--abi armeabi] \
[--local-engine-src-path=/path/to/engine/src] \
[--local-engine=android_debug_unopt] \
[--build-dir=/path/to/your/engine/build] \
[--symbol-dir=/path/to/your/engine/symbol] \
com.example.package_name
```

There is a sample command

```
./flutter_lldb \
--local-engine-src-path=/Users/lizhangqu/software/flutter_dev/engine/src \
--local-engine=android_debug_unopt \
com.vdian.flutter.wdb_buyer.example
```

Now you can see the output. There is a visual studio code config. Copy it to launch.json.

```
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "remote_lldb",
            "type": "lldb",
            "request": "attach",
            "pid": "17331",
            "initCommands": [
                "platform select remote-android",
                "platform connect unix-abstract-connect:///data/data/com.vdian.flutter.wdb_buyer.example/debug.socket"
            ],
            "postRunCommands": [
                "add-dsym /Users/lizhangqu/software/flutter_dev/engine/src/out/android_debug_unopt/libflutter.so",
                "settings set target.source-map /Users/lizhangqu/software/flutter_dev/engine/src /Users/lizhangqu/software/flutter_dev/engine/src"
            ],
        }
    ]
}
    
```

or 

```
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "remote_lldb",
            "type": "lldb",
            "request": "attach",
            "pid": "get pid by execute `adb shell pidof com.vdian.flutter.wdb_buyer.example` yourself",
            "initCommands": [
                "platform select remote-android",
                "platform connect unix-abstract-connect:///data/data/com.vdian.flutter.wdb_buyer.example/debug.socket"
            ],
            "preRunCommands": [
                "settings append target.exec-search-paths /Users/lizhangqu/software/flutter_dev/engine/src/out/android_debug_unopt"
            ],
            "postRunCommands": [
                "settings set target.source-map /Users/lizhangqu/software/flutter_dev/engine/src /Users/lizhangqu/software/flutter_dev/engine/src"
            ],
        }
    ]
}
```

The first config only can be used for that the libfluttser.so has already been loaded into memory.


7、use visual studio code to remote debug

Install Extensions in Visual Studio Code
 - [C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)
 - [CodeLLDB](https://marketplace.visualstudio.com/items?itemName=vadimcn.vscode-lldb)


Copy the file `/path/to/engine/src/out/compile_commands.json` to `/path/to/engine/src/flutter/compile_commands.json`

Use Visual Studio Code to open project `/path/to/engine/src/flutter`

Config the .vscode/launch.json by flutter_lldb generated

```
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "remote_lldb",
            "type": "lldb",
            "request": "attach",
            "pid": "17331",
            "initCommands": [
                "platform select remote-android",
                "platform connect unix-abstract-connect:///data/data/com.vdian.flutter.wdb_buyer.example/debug.socket"
            ],
            "postRunCommands": [
                "add-dsym /Users/lizhangqu/software/flutter_dev/engine/src/out/android_debug_unopt/libflutter.so",
                "settings set target.source-map /Users/lizhangqu/software/flutter_dev/engine/src /Users/lizhangqu/software/flutter_dev/engine/src"
            ],
        }
    ]
} 
```

or 

```
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "remote_lldb",
            "type": "lldb",
            "request": "attach",
            "pid": "get pid by execute `adb shell pidof com.vdian.flutter.wdb_buyer.example` yourself",
            "initCommands": [
                "platform select remote-android",
                "platform connect unix-abstract-connect:///data/data/com.vdian.flutter.wdb_buyer.example/debug.socket"
            ],
            "preRunCommands": [
                "settings append target.exec-search-paths /Users/lizhangqu/software/flutter_dev/engine/src/out/android_debug_unopt"
            ],
            "postRunCommands": [
                "settings set target.source-map /Users/lizhangqu/software/flutter_dev/engine/src /Users/lizhangqu/software/flutter_dev/engine/src"
            ],
        }
    ]
}
```

The first config only can be used for that the libfluttser.so has already been loaded into memory.

Now run it and set a breakpoint.
