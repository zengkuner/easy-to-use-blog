### 字面量类型与联合类型

#### 字面量类型

一般写法

假设变量 x 是 number 类型

```typescript
let x: number;
let x: string; // 字符串类型
```

直接赋值

```typescript
let x: "strong";
```

这样写表示`x`是字符串类型并且它的值只能为`strong`

如果给 x 赋其他值，比如：

```typescript
x = "weak";
```

这样写会类型报错：`不能将类型“'weak'”分配给类型“'strong'”`。

所以字面量类型相当于定义了一个常量，显然不太适用，通常是搭配联合类型一起使用的。

#### 联合类型
1. 联合类型表示“这个值可以是A类型或B类型”。
2. 联合类型使用管道符`|`来分隔每种类型。

示例：
```typescript
let value: number | string；

value = 42; // 正确
value = 'hello'; // 正确
```
