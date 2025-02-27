## 字面量类型与联合类型

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

1. 联合类型表示“这个值可以是 A 类型或 B 类型”。
2. 联合类型使用管道符`|`来分隔每种类型。

示例：

```typescript
let value: number | string；

value = 42; // 正确
value = 'hello'; // 正确
```

#### 字面量类型搭配联合一起使用

一般适用于有固定值的集合，比如星期、方向等，这样做可以提前规错误，避免取到预期之外的值。

```typescript
let heading: "North" | "South" | "East" | "West";
heading = "North"; // 正确
heading = "hello"; // 错误
```

## any 与 unknown 类型

#### any 类型

any 类型相当于没有设置类型限制，变量可以赋值为任意类型，any 类型是具有传播性的，any 类型的变量可以赋值给任意类型的其他变量，并导致其他变量的类型丢失。

```typescript
let a: any = "hello";
let b: number = 1;
b = a; // 这样做不会报错，并把b的类型变成了any类型
```

#### unknow 类型

当一个变量我们事先不知道它的类型时，我们可以给它定义为`unknow`类型，这样定义之后，该变量可以赋任意类型的值。

```typescript
let a: unknow;
a = 1;
a = "hello";
a = {};
```

但是`unknow`类型的变量不可以赋值给其他变量。

##### 使用类型断言来规避这种情况

```typescript
let a: unknow;
a = "hello";
let b: number = 1;
b = a as number; // 类型断言
```

##### 使用类型守卫来规避

```typescript
let a: unknow;
a = "hello";
let b: number = 1;
// 类型守卫
if (typeof a === "number") {
  b = a;
}
```
