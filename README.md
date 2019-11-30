# flutter_lldb
flutter engine remote debug with lldb

# how to use

1、 clone this repository

```
git clone https://github.com/lizhangqu/flutter_lldb.git
```

2、 build your local engine 

 - [Flutter Engine 编译指北](https://fucknmb.com/2019/02/26/Flutter-Engine-%E7%BC%96%E8%AF%91%E6%8C%87%E5%8C%97/)
 - [Compiling-the-engine](https://github.com/flutter/flutter/wiki/Compiling-the-engine)

3、 build your debuggable apk with local engine

```
./flutterw build apk --debug --target-platform=android-arm --local-engine-src-path=/Users/lizhangqu/software/flutter_dev/engine/src --local-engine=android_debug_unopt
```

4、 install the built apk

```
adb install /path/to/sample.apk
```

5、 launch the apk

Launch the apk yourself.

6、run lldb-server in remote device

You must set environment named `ANDROID_HOME`.

`[]` in the command is optional and the value is default.

The package name parameter is required.

Now run `flutter_lldb`

```
./flutter_lldb [--port 8888] [--abi armeabi] [--local-engine-src-path=/path/to/engine/src] [--local-engine=android_debug_unopt] com.example.package_name
```

There is a sample command

```
./flutter_lldb --local-engine-src-path=/Users/lizhangqu/software/flutter_dev/engine/src --local-engine=android_debug_unopt com.vdian.flutter.wdb_buyer.example
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
            "pid": "30440",
            "initCommands": [
                "platform select remote-android",
                "platform connect connect://localhost:8888"
            ],
            "postRunCommands": [
                "add-dsym /Users/lizhangqu/software/flutter_dev/engine/src/out/android_debug_unopt/libflutter.so",
                "settings set target.source-map /Users/lizhangqu/software/flutter_dev/engine/src/out/android_debug_unopt /Users/lizhangqu/software/flutter_dev/engine/src"
            ],
        }
    ]
}
    
```

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
            "pid": "30440",
            "initCommands": [
                "platform select remote-android",
                "platform connect connect://localhost:8888"
            ],
            "postRunCommands": [
                "add-dsym /Users/lizhangqu/software/flutter_dev/engine/src/out/android_debug_unopt/libflutter.so",
                "settings set target.source-map /Users/lizhangqu/software/flutter_dev/engine/src/out/android_debug_unopt /Users/lizhangqu/software/flutter_dev/engine/src"
            ],
        }
    ]
}
    
```

Now run it and set a breakpoint.
