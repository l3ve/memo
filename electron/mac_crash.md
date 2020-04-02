> 记录下如何解析 electron 在 MacOS 下崩溃产生的 dump 文件，略过了一些必要的知识点，大家自行补充。[讨论点赞传送门](https://github.com/l3ve/memo/issues/1)

### 涉及的相关连接

1. [electron api crashReporter](https://www.electronjs.org/docs/api/crash-reporter)
2. [node-minidump](https://github.com/electron/node-minidump)
3. [breakpad](https://github.com/google/breakpad)

### 第一步收集崩溃日志 dump 文件

这一步官方文档已经写的很清楚了。

``` javascript
const { crashReporter } = require('electron')

// 关于 start 的一些注意事项这里就不多说了
crashReporter.start({
  submitURL: 'https://your-domain.com/url-to-submit',
  uploadToServer: true
})
```
然后可以在 electron 的 console 里模拟下崩溃。

``` javascript
process.crash()
```
理论上，这时候就可以在服务器（https://your-domain.com/url-to-submit）接到一个 post 请求，然后把这个二进制文件流保存起来。

恭喜你，得到了一个崩溃的 dump 文件。

### 第二步解析 dump 文件

官方有个 npm 包 [node-minidump](https://github.com/electron/node-minidump) 就是做解析 dump 文件的。

二话不说，查看 readme，clone 之，run 之。

``` javascript
minidump.addSymbolPath(path1, ..., pathN)

minidump.walkStack(minidumpFilePath, [symbolPaths, ]callback)

minidump.dumpSymbol(binaryPath, callback)
```
使用 ```minidump.walkStack``` ，传 dump 文件，在回调里```report.toString() ```，我们就可以看到如下内容。

```
Operating system: Mac OS X
                  10.15.3 19D76
CPU: amd64
     family 6 model 142 stepping 10
     8 CPUs

GPU: UNKNOWN

Crash reason:  EXC_BAD_INSTRUCTION / EXC_I386_INVOP
Crash address: 0x107c5d304
Process uptime: 17 seconds

Thread 0 (crashed)
 0  Electron Framework + 0x1d5d304
    rax = 0x00007fc2fcc06120   rdx = 0x0000000000000000
    rcx = 0x00007fc2fcf39110   rbx = 0x00007ffee9d04e70
    rsi = 0x000000010c723000   rdi = 0x00007fc2fcf39110
    rbp = 0x00007ffee9d04e60   rsp = 0x00007ffee9d04e60
     r8 = 0x0000003e865026f1    r9 = 0x0000000000000005
    r10 = 0x000000010c729e80   r11 = 0x0000003e4f9523b9
    r12 = 0x00007ffee9d04f20   r13 = 0x000000010c723000
    r14 = 0x00007ffee9d04e80   r15 = 0x00007ffee9d04e98
    rip = 0x0000000107c5d304
    Found by: given as instruction pointer in context
...
```

到这里算是大功告成 1% 了。

这里有几个重要的消息：

1. Crash reason:  EXC_BAD_INSTRUCTION / EXC_I386_INVOP
2.  0  Electron Framework + 0x1d5d304

第一点就不用说了，导致你崩溃的理由，具体可以谷歌下，都能找到大致的原因，比如空指针啥的，然而并没有什么用，没办法帮你定位到具体的崩溃点。重点就是第二点。告诉你是 ```Electron Framework``` 这个东西崩溃了，崩溃的点在 ```0x1d5d304```。

但是即使这样，也解决不了问题，例如，如果是我们自己的 node 导致了崩溃，也只能得到```0 self.node  + 0x10086```。所以这里我们需要 **符号表**（别问我为啥知道，我是个伪前端）。

### 第三步关联符号表

接下来就是这文章的重点了。

首先是剩下的两个 API，在不是自己熟悉的专业范围面前，调用后并没有什么卵用，只能看看源码了

``` javascript
minidump.addSymbolPath(path1, ..., pathN)

minidump.dumpSymbol(binaryPath, callback)
```

1. minidump.addSymbolPath

 ``` javascript
 var globalSymbolPaths = []
module.exports.addSymbolPath = Array.prototype.push.bind(globalSymbolPaths)
 ```
 只是把符号表文件路径存起来，解析的时候用到而已

2. minidump.dumpSymbol

 ``` javascript
const commands = {
      ...
      dump_syms: (() => {
          ...
          return path.resolve(__dirname, '..', 'build', 'src', 'tools', 'mac', 'dump_syms', 'dump_syms_mac')
      })()
}
module.exports.dumpSymbol = function (binary, callback) {
	  var dumpsyms = commands.dump_syms
	  if (!dumpsyms) {
	    callback(new Error('Unable to find "dump_syms"'))
	    return
  	  }
  	  // Search for binary.dSYM on OS X.
  	  var dsymPath = binary + '.dSYM'
	  if (process.platform === 'darwin' && fs.existsSync(dsymPath)) {
	     binary = dsymPath
	   }
	  // 众所周知，调用命令行，没啥好讲的
	  execute(dumpsyms, ['-r', '-c', binary], callback)
}
```
调用接口后，报错 `Error: spawn ~/node_modules/minidump/build/src/tools/mac/dump_syms/dump_syms_mac ENOENT`。
这里我们可以获取到几个重要信息

	1.  从参数 `binary ` 可以看到，我们需要一个后缀名为 `.dSYM` 的文件，是的，这就是符号表
	2.  报错信息看，对应目录下，确实没有这么一个可执行文件

 `.dSYM`文件，我们是自己项目生成的，如果崩溃点是 `node || c++` 就由对应的开发生成，我们这次是用`process.crash()`让 electron 崩溃的，所以需要`Electron Framework.dSYM`[下载地址](https://github.com/electron/electron/releases/tag/v4.2.12)。

 但是没有 dump_syms_mac 文件，却一直毫无头绪。

 ### 第四步 breakpad 解析 dump

 老办法，第三方库类有问题，那就看源码。

 源码里可以了解到`minidump`底层其实是用 Google 的`breakpad `来解析 dump 文件的。那么我们的问题就转移到了**breakpad 如何在 macOS 上解析 dump 文件**。

 此时网上的资源可就多了很多可以参考的，有兴趣的同学可以自行了解。

 那么我们要做的第一步就是编译上面缺失的文件，在`breakpad`项目`/breakpad-master/src/tools/mac/dump_syms`里有一个`dump_syms.xcodeproj`的项目，可以用**Xcode**编译下，生成一个`dump_syms`可执行文件。这个可执行文件就是之前调用`minidump.dumpSymbol`报出找不到的`dump_syms_mac`。

 那么好了，可以继续之前后面卡住的步骤了。

 接下来网上也有挺多文章介绍的，我就简单的说说过程。以之前`Electron Framework`的崩溃点为例（具体的文件都在`electron-v4.2.12-darwin-x64-dsym`里，`breakpad_symbols`则是可以直接用的字符表，可以以此为参考，这就是最终目标目录）

 1. 生成 `.sym` 文件
     * `./dump_syms Electron Framework.dSYM > Electron Framework.sym`
     * `minidump.dumpSymbol()`则能获取到文件流，需要自己去生成`.sym`

  2. 生成固定的文件结构

     对照`breakpad_symbols`可以看到，我们需要的文件结构应该是`Electron Framework/E5853EC4F6AC3269BA025E66A24420230/Electron Framework.sym`。中间这串哈希就是最后的关键，其实就是来自于`Electron Framework.sym`文件的第一行内容。

     * `head -1 Electron Framework.sym`用命令行查看
     * `minidump.dumpSymbol`的话就直接获取就好了

获得的信息如下：

`MODULE mac x86_64 E5853EC4F6AC3269BA025E66A24420230 Electron Framework`

所以我们最后字符表目录的结构是这样的

	//文件夹名字和哈希都是从上面来的，还有最好保持名字的一致性
    └─ symbols
        ├─Electron Framework
        │  └─ E5853EC4F6AC3269BA025E66A24420230
        │        └─ Electron Framework.sym
        ├─Electron
        │	 └─ E5853EC4F6AC3269BA025E66A24420230
        │        └─ Electron.sym


### 最后一步

二选一，其实都一样

1. 用` breakpad`生成的`minidump_stackwalk`可执行文件解析

  在`breakpad-master/src/processor`里，直接用命令行`./minidump_stackwalk ./crash.dmp ./symbols`


2. 用`minidump.walkStack(minidumpFilePath, [symbolPaths, ]callback)`其实实现就是上面的方法

解析出来是这样的：

``` javascript
 0  Electron Framework!atom::AtomBindings::Crash() [atom_bindings.cc : 143 + 0x0]
    rax = 0x00007ff625f36400   rdx = 0x0000000000000000
    rcx = 0x00007ff625c1c080   rbx = 0x00007ffee022ce80
    rsi = 0x000000010ff60000   rdi = 0x00007ff625c1c080
    rbp = 0x00007ffee022ce70   rsp = 0x00007ffee022ce70
     r8 = 0x000000482ff826f1    r9 = 0x0000000000000005
    r10 = 0x000000010ff66e80   r11 = 0x00000048b8f40549
    r12 = 0x00007ffee022cf30   r13 = 0x000000010ff60000
    r14 = 0x00007ffee022ce90   r15 = 0x00007ffee022cea8
    rip = 0x0000000115be32d4
    Found by: given as instruction pointer in context
 1  Electron Framework!mate::internal::Dispatcher<void ()>::DispatchToCallback(v8::FunctionCallbackInfo<v8::Value> const&) [callback.h : 129 + 0x3]
    rbp = 0x00007ffee022ced0   rsp = 0x00007ffee022ce80
    rip = 0x0000000115c14071
    Found by: previous frame's frame pointer
 2  Electron Framework!v8::internal::FunctionCallbackArguments::Call(v8::internal::CallHandlerInfo*) [api-arguments-inl.h : 95 + 0x6]
    rbp = 0x00007ffee022cf80   rsp = 0x00007ffee022cee0
    rip = 0x0000000114f96068
    Found by: previous frame's frame pointer
```
现在就可以看到详细的崩溃点了，好了接下来就是解 bug 的时间了。

这里有几个点说一下：

* 保证崩溃 `dump` 文件和 `dsym` 是匹配的，例子里就是 `electron` 的版本号
* 崩溃点其实包括了 `node || C++`, 当然需要他们提供` dsym`，还需要配置，具体的可以参考[这里](https://chromium.googlesource.com/breakpad/breakpad/+/master/docs/mac_breakpad_starter_guide.md)

---
[讨论点赞传送门](https://github.com/l3ve/memo/issues/1)