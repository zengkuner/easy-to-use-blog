## Promise 对象

Promise 对象代表一个异步操作，有 3 种状态：`pending`（进行中）、`fufilled`（已成功）和`rejected`（已失败）。

### 生命周期

1. 处理中 unsettled
2. 处理完 settled

与 3 种状态的关系图如下：

![关系图](https://p9-xtjj-sign.byteimg.com/tos-cn-i-73owjymdk6/889f33d7bc9c486f9fb76d938b49bcc3~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgYW1iaXRpb3Vz:q75.awebp?rk3s=f64ab15b&x-expires=1741511569&x-signature=ziSfD6SAWwu%2Bi2mozaQctLt%2F97s%3D#pic_center=30x40)

但是我们无法读取到这 3 个状态，而是通过 Promise 提供的接口方法来书写对应的处理程序。

Promise 构造函数接受一个函数作为参数，该函数的两个参数分别是`resolve`和`reject`。

### Promise 常用的 5 个方法

#### 1. then()

当 Promise 执行的内容符合成功条件时，调用`resolve`函数，传给`then`的函数就是`resolve`函数，Promise 中只有执行了`resolve`，`then`函数才会执行。

```js
Promise.resolve(1).then((res) => {
  console.log(res); // 1
});

Promise.reject(1).then((res) => {
  console.log(res);
}); // Error
```

#### 2. catch()

当我们在 Promise 里面抛出异常，调用了`reject()`方法，传给`catch()`的函数就是`reject()`方法。

```js
Promise.reject(1).catch((res) => {
  console.log(res); // 1
});
```

#### 3. all()

- all 方法可以完成并行任务，它接收一个数组，数组的每一项都是一个`Promise`对象。
- 当数组中所有的`Promise`对象的状态都达到`resolve`的时候，`all()`方法就`resolve`。
- 当有数组中只要有一个`reject`，则`all()`就`reject`，如果有多个`Promise`对象发生了`reject`，则`all()`方法获得执行速度最快的那个`reject`。
- 当 all 方法`resolve`时，返回结果的顺序和执行时间无关，和传入顺序一致。

#### 4. race()

- `race()`方法和`all()`方法一样，接收的参数是每一个都是`Promise`对象的数组，但是与`all`不同的是，不管执行结果，只跟 Promise 对象的执行速度有关，获得执行速度最快的那一个。
- 如果执行速度一样快，则按传入顺序获得第一个的状态。

#### 5. finally（）

finally()方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。
