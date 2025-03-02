## Promise 对象

Promise 对象代表一个异步操作，有 3 种状态：`pending`（进行中）、`fufilled`（已成功）和`rejected`（已失败）。

### 生命周期

1. 处理中 unsettled
2. 处理完 settled

与 3 种状态的关系图如下：  
<<<<<<< HEAD
![关系图](https://p9-xtjj-sign.byteimg.com/tos-cn-i-73owjymdk6/889f33d7bc9c486f9fb76d938b49bcc3~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgYW1iaXRpb3Vz:q75.awebp?rk3s=f64ab15b&x-expires=1741511569&x-signature=ziSfD6SAWwu%2Bi2mozaQctLt%2F97s%3D)
=======
![关系图]([/异步编程系列/images/promise-life-cycle.png](https://github.com/zengkuner/easy-to-use-blog/blob/master/%E5%BC%82%E6%AD%A5%E7%BC%96%E7%A8%8B%E7%B3%BB%E5%88%97/images/promise-life-cycle.png))
>>>>>>> 610a9dc828121caec98612c43f6969dde5bf6554

但是我们无法读取到这 3 个状态，而是通过 Promise 提供的接口方法来书写对应的处理程序。
