# Kinzoku

Implementation of wgpu-native in Swift.

Library binaries are provided for
 - macOS
   - arm64
   - x86_64 (testing)
 - manylinux
   - arm64 (testing)
   - x86_64 (testing)

General Notes
 - Create getters and setters for all KZ classes that make interfacing with them akin to C--just provide middleman
 - ~~All the 'self.pointers' tuples can probably be compressed to just an [UnsafeMutablePointer<Any>], but readibility is key right now~~
 - WGPUChainedStructs should be removed eventually
   - Appears basically everwhere with "nextInChain" and what not
   - Maybe implement a .intoChainedStruct() for all classes, or allow all classes in the init()?
 - (1) ~~Consider making any structs/classes that are just 'public var c: WGPUClass' into typealiases~~
   - ~~Typealiases can have extensions for other functions. Any non-pointer holding classes can be converted easily~~
     - ~~Extensions also may allow for getters/setters, so interfacing swift-like would be better~~
     - ~~Removes the issue of invoking .c constantly, we can just use ?~~
     - ~~Maybe make instance hold all pointers in [UnsafeMutablePointer<Any>], deallocate all at once (in Instance, for example--see the first comment)~~
       - ~~Less memory efficient, cleaner code~~
       - ~~Depending on how things are sized, it might actually be more memory efficient, as double-up'd objects wouldn't be an issue~~

 - Compress class files into more abstract files
 - (2) ~~Set enums to have primitive WGPUTypes, that way no conversion is necessary (removes all the calls to constructors and .rawValues)~~

 - Make functions with callbacks (presumably async) into actual swift async functions
 - (3) ~~Eventually exclude the header files, because we basically define it ourselves in this file~~
   - ~~Would also remove all dependencies from the project, which is nice. WgpuHeaders would be irrelevent~~
   - ~~Callbacks can become regular closures probably~~
 - The wgpu.h free functions are interesting. We unfortunately have to use strdup() constantly, so I wonder if it cleans those types for structs
 - ~~Maybe change the definition of `manualPointer()` so it uses `Unmanaged.passUnretained()`. That way Swift's ARC handles memory as normal and we don't have to figure out deinit constantly~~
   - Changed definition. manualPointer has been split to have a copying method and a dangling method

1. [Not possible due to all C types being derivatives of OpaquePointer]
2. [Not possible, WGPUTypes cannot be the basis of enums, raw values are required (but inits might be possible)]
3. [Not possible, C structs are not the same as Swift structs, we can't redefine those: we need at least one .h for structs]
