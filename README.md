

> Chinese Readme: [中文版readme](README_CN.md).

## Introduction

WBBlades is a tool set based on Mach-O file parsing, including unused code detection (supporting ObjC and Swift), app size analysis, and log recovery without dSYM file.

## Installation

```
$ git clone https://github.com/wuba/WBBlades.git
$ cd WBBlades
$ sudo make install
```

Build with SwiftPM

```
swift build -c release --arch arm64 --arch x86_64
sudo cp .build/apple/Products/Release/blades /usr/local/bin
```

**If you see some message like `[1] 70390 killed blades` ,please `make install`again.**


## Usage

- Unused Code Detection ObjC & Swift

   `$ blades -unused xxx.app -from xxx.a xxx.a ....`
   
	> -from indicating that only the unused code in the following static libraries is analyzed. Without this parameter, the default is all classes in the APP.
   
- App Size Analysis (Directly measure the size of .a or .framework after linking)

  `$ blades -size xxx.a xxx.framework ....`
  
  > Supporting input a folder path, all static libraries under the folder will be analyzed.
  
- Log Recovery without dSYM File (In the case of missing dSYM file, try `ObjC` crash stack symbolization, `Swift` is not supported)

  `$ blades -symbol xxx.app -logPath xxx.ips`

## Tool Features

### Unused code (unused class) detection support range

| Description                     | Support | Code Example                                     |
| :----------------------- | :----------: | :------------------------------------------- |
| ObjC classes's static call       |      ✅       | `[MyClass new]`                              |
| ObjC classes's dynamic call           |      ✅       | `NSClassFromString(@"MyClass")`              |
| ObjC dynamic call througn string concatenation    |      ❌       | `NSClassFromString(@"My" + @"Class")`        |
| ObjC load method         |      ✅       | `+load{...} `                                |
| ObjC & Swift being inherited       |      ✅       | `SomClass : MyClass`                         |
| ObjC & Swift being properties      |      ✅       | `@property (strong,atomic) MyClass *obj;`    |
| Swift class direct call         |      ✅       | `MyClass.init()`                             |
| Swift call using runtime    |      ❌       | `objc_getClass("Demo.MyClass")`              |
| Swift generic parameters           |      ✅       | `SomeClass<MyClass>.init()`                  |
| Swfit class dynamic call in ObjC   |      ✅       | `NSClassFromString("Demo.MyClass")`          |
| Swift type declaration in the container |      ❌       | `var array:[MyClass]`                        |
| Swift multiple nesting           |      ✅       | ` class SomeClass {class MyClass {...} ...}` |

### App Size Analysis Tool

Supports quick detection of the linked size of a static library. No need to compile and link. **For example: If you want to know how much app size will increase when an SDK is imported or updated, you can use `blades -size` to estimate the size**, without the need to connect the SDK to compile and link successfully to calculate.

### Crash Log Symbolization Tool Without dSYM File

In the case of losing the dSYM file, try to restore the log via `blades -symbol`. **For example, in an app packaging, the dSYM file is cleared after a period of time, but the app file is retained. In this case, you can consider using blades for symbolization. **Before using the tool, pay attention to a few points:

- If your app is a debug package or a package that does not strip the symbol table, you can use `dsymutil app -o xx.dSYM `to extract the symbol table. Then use the symbol table to symbolize the log.

- This tool only supports ObjC, and its principle is to determine the function of the crash by analyzing the address of the ObjC method in Mach-O. Therefore, it is not suitable for Swfit, C, and C++. In addition, tools are not omnipotent, and are only used as emergency supplementary technical means. In daily situations, it is recommended to use symbol tables for log symbolization.

## Developer for WBBlades

邓竹立

## Contributors for WBBlades

邓竹立，彭飞，朴惠姝，曾庆隆，林雅明

## Contributing & Feedback

We sincerely hope that developers can provide valuable comments and suggestions, and developers can provide feedback on suggestions and problems by submitting PR or Issue.

## Related Technical Articles

- [58tongcheng Size Analysis and Statistics for iOS Client Components](https://blog.csdn.net/csdnnews/article/details/100354658/)
- [Unused Class Detection Based on Mach-O Disassembly](https://www.jianshu.com/p/c41ad330e81c)
- [Open Source｜WBBlades：APP Analysis Tool Set Based on Mach-O File Analysis](https://mp.weixin.qq.com/s/HWJArO5y9G20jb2pqaAQWQ)
- [The Storage Difference between Swift and ObjC from the Perspective of Mach-O](https://www.jianshu.com/p/ef0ff6ee6bc6)
- [New Approach to Swift Hook - Virtual Method Table](https://mp.weixin.qq.com/s/mjwOVdPZUlEMgLUNdT6o9g)

## Thanks

GitHub: [https://github.com/aquynh/capstone](https://github.com/aquynh/capstone "GitHub for capstone") 
