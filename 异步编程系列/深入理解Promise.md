## Promise 对象

Promise 对象代表一个异步操作，有 3 种状态：`pending`（进行中）、`fufilled`（已成功）和`rejected`（已失败）。

### 生命周期

1. 处理中 unsettled
2. 处理完 settled

与 3 种状态的关系图如下：

![关系图](https://p9-xtjj-sign.byteimg.com/tos-cn-i-73owjymdk6/889f33d7bc9c486f9fb76d938b49bcc3~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgYW1iaXRpb3Vz:q75.awebp?rk3s=f64ab15b&x-expires=1741511569&x-signature=ziSfD6SAWwu%2Bi2mozaQctLt%2F97s%3D#pic_center=30x40)

但是我们无法读取到这 3 个状态，而是通过 Promise 提供的接口方法来书写对应的处理程序。
