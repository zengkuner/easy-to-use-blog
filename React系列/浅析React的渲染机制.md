## React 的渲染机制

一道面试题：`useLayoutEffect`、`useEffect`、`cleanup`清理函数、`render`函数、浏览器页面渲染 这 5 件事的执行顺序是什么？

直接给出结论：

#### 组件首次挂载时，没有清理函数，执行顺序是：

1. Render
2. Commit
3. useLayoutEffect 回调
4. 浏览器绘制（和上一步几乎同时发生）
5. useEffect 回调
6. Render
7. 浏览器绘制

#### 当组件更新时：

1. 状态变化触发重新渲染
2. Render 阶段：生成新的虚拟 DOM
3. 协调过程，找出差异（diff 算法，对比新旧虚拟 DOM）
4. 提交阶段：Commit DOM 更新（真实 DOM）
5. 执行上一次的`useLayoutEffect`清理函数
6. 执行新的`useLayoutEffect`回调
7. 浏览器绘制页面
8. 执行上次的`useEffect`清理函数（如果依赖变化）
9. 执行新的`useEffect`回调
10. Render 函数执行
11. 浏览器页面绘制
    > 其实我自己实践时先进行浏览器页面绘制，再执行`useLayoutEffect`回调。

#### 当组件卸载时：

1. 触发卸载
2. 执行`useLayoutEffect`的清理函数
3. 浏览器可能更新 DOM（但组件已移除）
4. 执行`useEffect`的清理函数

---

### 详细原理分析

#### 1. **Render 阶段**

- **作用**：调用组件的 `render` 方法，生成新的虚拟 DOM。
- **特点**：
  - 纯计算阶段，不操作真实 DOM。
  - 可能被 React 中断（并发模式下），以优先处理更高优先级的任务。
- **输出**：更新计划（哪些 DOM 需要修改）。

#### 2. **协调阶段（Reconciliation）**

- **作用**：通过 Diff 算法比较新旧虚拟 DOM，确定需要更新的部分。
- **关键点**：React 会生成一个副作用链表（Effect List），记录需要执行的操作（如插入、更新、删除 DOM 节点）。

#### 3. **提交阶段（Commit Phase）**

- **步骤**：
  1. **更新真实 DOM**：根据副作用链表，将变更应用到真实 DOM。
  2. **同步执行 `useLayoutEffect` 的清理函数**（仅在依赖变化或卸载时执行）：
     - 清理函数是上一次渲染中 `useLayoutEffect` 回调返回的函数。
     - 执行顺序与组件树中组件的挂载顺序**相反**（类似 `componentWillUnmount`）。
  3. **同步执行 `useLayoutEffect` 的回调函数**：
     - 回调函数能访问到最新的 DOM 状态（此时浏览器尚未渲染）。
     - 适合需要同步读取布局（如获取 DOM 尺寸）的操作。

#### 4. **浏览器页面渲染**

- **过程**：
  1. **布局（Layout）**：计算 DOM 元素的几何信息（位置、大小等）。
  2. **绘制（Paint）**：将像素绘制到屏幕上。
- **关键点**：
  - 此时用户可以看到最新的 UI。
  - `useLayoutEffect` 的副作用已执行完毕，因此对 DOM 的修改会反映到本次渲染中。

#### 5. **异步执行 `useEffect` 的清理和回调**

- **执行顺序**：
  1. **清理函数**：上一次渲染中 `useEffect` 回调返回的函数（仅在依赖变化或卸载时执行）。
  2. **回调函数**：新的 `useEffect` 回调。
- **特点**：
  - 在浏览器渲染完成后**异步执行**（通过 `requestIdleCallback` 或微任务队列调度）。
  - 适合不需要阻塞页面渲染的操作（如数据请求、事件订阅）。

---

### **关键差异对比**

| 特性                 | `useLayoutEffect`                              | `useEffect`                               |
| -------------------- | ---------------------------------------------- | ----------------------------------------- |
| **执行时机**         | 同步执行（在 DOM 更新后、浏览器渲染前）        | 异步执行（在浏览器渲染完成后）            |
| **适合场景**         | 需要同步读取/修改 DOM（如防闪烁）              | 数据获取、订阅等不紧急操作                |
| **对用户体验的影响** | 可能阻塞渲染（慎用）                           | 不阻塞渲染                                |
| **清理函数执行时机** | 在 DOM 更新后、下一次 `useLayoutEffect` 回调前 | 在浏览器渲染后、下一次 `useEffect` 回调前 |

---

### **典型场景示例**

#### 场景：修改 DOM 样式避免闪烁

```jsx
function Component() {
  const ref = useRef();

  useLayoutEffect(() => {
    // 同步读取 DOM 尺寸
    const width = ref.current.offsetWidth;
    // 同步修改 DOM 避免闪烁
    ref.current.style.height = `${width}px`;
  }, []);

  return <div ref={ref} />;
}
```

- 如果使用 `useEffect`，用户可能会先看到未调整高度的元素，再看到高度变化（导致闪烁）。

---

### **执行流程图解**

```
触发更新
  │
  ↓
Render 阶段（生成虚拟 DOM）
  │
  ↓
协调阶段（Diff 算法）
  │
  ↓
提交阶段（更新真实 DOM）
  │
  ├──→ 执行 useLayoutEffect 清理函数（若有）
  │
  ├──→ 执行 useLayoutEffect 回调
  │
  ↓
浏览器渲染（布局 + 绘制）
  │
  ├──→ 执行 useEffect 清理函数（若有）
  │
  └──→ 执行 useEffect 回调
```

---

### **总结**

1. **同步操作**（`useLayoutEffect`）在浏览器渲染前完成，适合需要立即生效的 DOM 操作。
2. **异步操作**（`useEffect`）在浏览器渲染后完成，适合非紧急任务。
3. **清理函数**的执行时机取决于它属于 `useLayoutEffect` 还是 `useEffect`。
4. 错误地使用 `useLayoutEffect` 可能导致性能问题（阻塞渲染），而错误地使用 `useEffect` 可能导致视觉不一致（如闪烁）。
