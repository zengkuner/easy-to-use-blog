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
