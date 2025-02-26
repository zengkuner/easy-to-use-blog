### 字面量类型与联合类型

#### 字面量类型

一般写法

假设变量 x 是 number 类型

```javascript
let x: number;
let x: string; // 字符串类型
```

直接赋值

```javascript
let x: "strong";
```

这样写表示`x`是字符串类型并且它的值只能为`strong`

如果给 x 赋其他值，比如：

```javascript
x = "weak";
```

这样写会类型报错：`不能将类型“'weak'”分配给类型“'strong'”`。
