Vue2 的响应式系统基于 `Object.defineProperty` 实现，核心是通过数据劫持结合发布-订阅模式来追踪依赖和触发更新。以下是详细原理和源码解析：

---

### 1. 初始化响应式数据

当 Vue 实例初始化时，会通过 `Observer` 类递归地将数据对象转换为响应式。

**源码位置**：`src/core/observer/index.js`

```javascript
// Observer 类构造函数
export class Observer {
  constructor(value) {
    this.value = value;
    this.dep = new Dep(); // 依赖管理器
    def(value, "__ob__", this); // 为对象添加 __ob__ 属性

    if (Array.isArray(value)) {
      // 处理数组
      protoAugment(value, arrayMethods); // 覆盖数组原型方法
      this.observeArray(value);
    } else {
      // 处理对象
      this.walk(value);
    }
  }

  walk(obj) {
    Object.keys(obj).forEach((key) => {
      defineReactive(obj, key);
    });
  }

  observeArray(items) {
    for (let i = 0, l = items.length; i < l; i++) {
      observe(items[i]); // 递归观察数组元素
    }
  }
}
```

---

### 2. 定义响应式属性

`defineReactive` 函数使用 `Object.defineProperty` 定义属性的 getter/setter。

```javascript
export function defineReactive(obj, key, val) {
  const dep = new Dep(); // 每个属性对应一个 Dep 实例

  // 递归处理嵌套对象
  let childOb = observe(val);

  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: function reactiveGetter() {
      const value = val;
      if (Dep.target) {
        // 当前正在计算的 Watcher
        dep.depend(); // 收集依赖
        if (childOb) {
          childOb.dep.depend(); // 子对象依赖收集
        }
        if (Array.isArray(value)) {
          dependArray(value); // 数组元素依赖收集
        }
      }
      return value;
    },
    set: function reactiveSetter(newVal) {
      if (val === newVal) return;
      val = newVal;
      childOb = observe(newVal); // 新值转为响应式
      dep.notify(); // 通知所有 Watcher 更新
    },
  });
}
```

---

### 3. 数组的响应式处理

Vue2 通过重写数组的 7 个变异方法实现响应式。

**源码位置**：`src/core/observer/array.js`

```javascript
const arrayProto = Array.prototype;
export const arrayMethods = Object.create(arrayProto);

const methodsToPatch = [
  "push",
  "pop",
  "shift",
  "unshift",
  "splice",
  "sort",
  "reverse",
];

methodsToPatch.forEach(function (method) {
  const original = arrayProto[method];
  def(arrayMethods, method, function mutator(...args) {
    const result = original.apply(this, args);
    const ob = this.__ob__;
    let inserted;
    switch (method) {
      case "push":
      case "unshift":
        inserted = args;
        break;
      case "splice":
        inserted = args.slice(2);
        break;
    }
    if (inserted) ob.observeArray(inserted); // 观察新增元素
    ob.dep.notify(); // 触发更新
    return result;
  });
});
```

---

### 4. 依赖管理（Dep 和 Watcher）

- **Dep**：每个响应式属性对应一个 Dep 实例，用于存储依赖（Watcher）。
- **Watcher**：代表一个依赖，当数据变化时触发回调。

**Dep 类源码**：`src/core/observer/dep.js`

```javascript
export default class Dep {
  static target: ?Watcher; // 全局唯一的当前正在计算的 Watcher
  subs: Array<Watcher>;

  constructor() {
    this.subs = [];
  }

  addSub(sub: Watcher) {
    this.subs.push(sub);
  }

  depend() {
    if (Dep.target) {
      Dep.target.addDep(this); // Watcher 记录 Dep
    }
  }

  notify() {
    const subs = this.subs.slice();
    for (let i = 0, l = subs.length; i < l; i++) {
      subs[i].update(); // 通知所有 Watcher 更新
    }
  }
}
```

**Watcher 类关键逻辑**：`src/core/observer/watcher.js`

```javascript
export default class Watcher {
  constructor(vm: Component, expOrFn: string | Function) {
    this.vm = vm;
    this.getter = expOrFn;
    this.value = this.get();
  }

  get() {
    pushTarget(this); // 设置 Dep.target
    let value;
    const vm = this.vm;
    try {
      value = this.getter.call(vm, vm); // 触发 getter，收集依赖
    } finally {
      popTarget(); // 恢复 Dep.target
    }
    return value;
  }

  update() {
    queueWatcher(this); // 进入异步更新队列
  }

  run() {
    this.get(); // 实际更新逻辑（如重新渲染）
  }
}
```

---

### 5. 异步更新队列

Vue 通过 `nextTick` 实现异步批量更新，避免重复计算。

**源码位置**：`src/core/observer/scheduler.js`

```javascript
const queue = [];
let waiting = false;

function flushSchedulerQueue() {
  queue.forEach((watcher) => {
    watcher.run(); // 执行所有 Watcher 的更新
  });
  resetSchedulerState();
}

function queueWatcher(watcher: Watcher) {
  queue.push(watcher);
  if (!waiting) {
    nextTick(flushSchedulerQueue); // 下一个事件循环执行
    waiting = true;
  }
}
```

---

### 关键流程图

```
Data
  │
  ├─ Observer 转换响应式
  │   ├─ Object: defineReactive()
  │   └─ Array: 重写原型方法
  │
  ├─ Getter 触发时（Dep.target 存在）
  │   └─ dep.depend() → Watcher 添加到 Dep.subs
  │
  └─ Setter 触发时
      └─ dep.notify() → 所有 Watcher.update()
          └─ queueWatcher() → 异步执行 run()
```

---

### 局限性

1. **对象新增属性**：无法检测，需使用 `Vue.set(obj, 'key', value)`。
2. **数组索引修改**：`arr[index] = newValue` 无法触发更新，需使用 `Vue.set` 或变异方法。
3. **数组长度修改**：`arr.length = newLength` 无法触发更新。

---

通过以上机制，Vue2 实现了数据的响应式更新，确保视图与数据保持同步。
