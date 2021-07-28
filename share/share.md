title: wasm for FE
speaker: 33
css: 
    - ./style.css


<slide class="bg-black-blue aligncenter"  .dark">

# WASM  {.text-landing.text-shadow}

对前端来说，有了 .wasm 能上天？ {.text-intro}


<slide class="bg-black-blue aligncenter"  .dark">

# 优点 {.text-landing.text-shadow}


- 快快快快 
- 快快快 
- 快快 
- 小
  {.content-left .f20 .ptop} 

<slide class="bg-black-blue aligncenter"  .dark">

# 快？！ show me the code



<slide class="bg-black-blue aligncenter"  .dark">

# 前端就这？JavaScript 就这？

!![](./public/youdonotneedrust.png) {.ptop}

[文章在这](https://mrale.ph/blog/2018/02/03/maybe-you-dont-need-rust-to-speed-up-your-js.html)


<slide class="bg-black-blue aligncenter"  .dark">

# 差距在哪里呢？

我们能够优化的唯一一点：jit 无法优化的部分(静态编译型语言 和  动态解析型语言 的区别) {.ctx .ptop} 


<slide class="bg-black-blue aligncenter"  .dark">

# 真无敌？

wasm 能干啥？不能干啥？ {.ctx .ptop} 

<slide class="bg-black-blue aligncenter"  .dark">

# 干

- 提高纯密集型运算，例如：加解密算法，音视频编解码，diff 判断等，且交互的数据量应该越小越好
- 重新实现前端的功能，wasm 现在基本上可以调用所有的 js 能调的 api。
- 提供了另一种代码不可见的安全方案，wasm 可以反编译，但是可读性极其差，类似于汇编。


<slide class="bg-black-blue aligncenter"  .dark">

# 算了吧

- 性能瓶颈是浏览器的 api
- 数据交互量大的
