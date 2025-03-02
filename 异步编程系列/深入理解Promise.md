## Promise 对象

Promise 对象代表一个异步操作，有 3 种状态：`pending`（进行中）、`fufilled`（已成功）和`rejected`（已失败）。

### 生命周期

1. 处理中 unsettled
2. 处理完 settled

与 3 种状态的关系图如下：  
![关系图]([/异步编程系列/images/promise-life-cycle.png](https://github.com/zengkuner/easy-to-use-blog/blob/master/%E5%BC%82%E6%AD%A5%E7%BC%96%E7%A8%8B%E7%B3%BB%E5%88%97/images/promise-life-cycle.png))

但是我们无法读取到这 3 个状态，而是通过 Promise 提供的接口方法来书写对应的处理程序。
