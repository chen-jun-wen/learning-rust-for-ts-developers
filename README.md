# Learn Rust for TS developer

## log

TyepScript

```ts
console.log(`hello world`);
console.log(`hello `, world);

// nodejs
process.stdout.write(`hello world`); // 尾部不加 \n
```

Rust

```rust
// 在 Rust 使用「宏」(macro) 打印信息
println!("hello world");
// 在 println! 「宏」 中用 `{}` 表示要插值的内容
println!("hello {}", "world");

// println! 中的 ln 是 line 的意思
print!("hello world"); // 尾部不加 \n
```

## primative value

TypeScript

```ts
let count: number = 0.0;
let value: bigint = 100000;
let name: string = "hello world";
let bool: boolean = true;
// 数值可以用 _ 分隔，以便阅读
let bigInt: bigint = 100_000_000n;

// 空值
let nothing: undefined = undefined;
let nil: null = null; // 表示引用数据类型为空的情况

/// 自动类型推导
let n = 100; // number
let s = ""; // string
```

Rust

```rust
// 双精度浮点型，对应 ts 的  `number`
let count: f64 = 0.0;
// ts 中 `bigint` 可以用来存储无限大的整数
// 在 Rust 中没有 `bigint` 类型，i64 精度超出后会溢出
let value: i64 = 0.0;
// Rust 还有更丰富的数据类型，请查阅 []()

// ts 的字符串和 rust 中的 &str 相似
// 虽然两者的底层编码不一样，但是都是不可变的引用
let name: &str = "hello world";

let b: bool = true;

// Rust 的空值只有一个
let nothing = None;


/// Rust 还有更丰富的数据类型
// 数值
let float32: i32 = 0.0;
let int8: i8 = 0.0;
let int32: i32 = 0.0;
let auto_int: isize = 0; // 根据环境选择 i32 或 i64

// 字符串
let s1: String = String::from("hello world");
let s2: &str = "hello world".into();

// Rust 也和 ts 一样，会自动推导类型
let f = 100.0; // f32
let f_64 = 100.0f64; // f64
let s = String::from(""); // String
```

## ADT

### Tuple

```ts
// TypeScript
let tup: [number; string; boolean] = [0, "", true];

//
let tup1 = [0, true, boolean] as const;
```

---

```rust
// Rust

// 和 TS 不同的是， Rust 中的枚举使用 `()` 而不是 `[]`
let tup: (f64, &str, bool) = (0.0, "hello world", true);
```

### Struct

```ts
// TypeScript

// 在 TS 中可以通过 object 来使用一片连续的内存空间
// 虽然 object 虽然可以当成 `哈希表` 使用，但这不是好的习惯
// 在 key 不确定的使用场景应该使用 `Map`

// 在 TS 中通过 `type alias` 定义一个数据结构的模板
type User = {
    name: string;
    height: number;
    age: number;
    isMale: boolean;
};

// 初始化一个结构体
let tom: User = {
    name: "Tom",
    height: 0.1,
    age: 3,
    isMale: true,
};

// 在 TS 中可以自动推导类型
// 虽然看似没有像上面那样先创建 `类型`，然后再使用
// 大多引擎会内部创建一个 `隐藏类` 用于访问连续内存
let jerry = {
    name: "jerry",
    height: 0.03,
    age: 2.6,
    // TS 中标识符使用 camelCase
    isMale: true,
};
```

---

```Rust
// Rust

// 在 Rust 中使用 `Struct` 关键字使用一片连续内存
struct User {
    name: &str;
    height: f64;
    age: f64;
    // Rust 标识符使用 snake_case
    is_male: bool;
}

// Rust 是一门设计严谨的编程语言，结构体必须先声明再使用
let tom = User {
    name: String::from("Tom"),
    height: 0.1,
    age: 3,
    is_male: true,
}

// Rust 还可以声明一个没有任何字段的结构体
struct Bar;
```

### enum

```ts
// TypeScript

// TS 枚举分为 `Numeric enums`, `String enums`, `Heterogeneous enums`
// 但是他们之间并没有本质区别，仅仅表示一个可以被穷举的集合

// `Numeric enums`
enum Foo {
    A,
    B,
}

// `String enums`
enum Bar {
    A = "A",
    B = "B",
}

// 虽然 enum 可以这么用，感觉没啥意义
// `Heterogeneous enums`
enum Baz {
    A = 0,
    B = "B",
}

Foo.A; // 使用 `.` 操作符索引枚举成员
```

```rust
// Rust

enum Foo {
    A,
    B,
}

Foo::A // 使用 `::` 操作符索引枚举成员

// Rust 的枚举非常强大
// 除了表示一个可以被穷举的集合以外
// 集合的每个成员还能存放不同的数据类型
enum Bar {
    // Tuple: `(i32, &str, bool)`
    a(i32, &str, bool),
    // Tuple: `(String)`
    b(String),
    // Struct: `{ x: i32, y: bool }`
    c{x: i32, y: bool},
}
```

### Array & Vector

TypeScript

```ts
// TS 的 Array 我们不需要考虑内存布局，不需要考虑容量不够后扩容拷贝的开销，TS 中的数组可以存放任意数据类型
// 这让我们可以把重心放在业务逻辑的实现上，TS 很好为我们屏蔽掉了底层的实现细节
// 但与此同时也失去了对底层的控制，如果要用 TS 写高性能的程序费力不讨好

let arr = [1, {}, [], ""];

// Infer to `number | {} | [] | string`;
type ArrawItem = typeof arr extends (infer Item)[] ? Item : never;
```

---

```rust
// 在 Rust 中 Arraw 是一段连续的内存
// 使用 `[T;N]` 表示，T 是类型，N 是长度
// 数组 `length` 需要在初始化时候确定
// 数组每一项的数据类型需要在定义时候就明确下来，数组中每一项都要是一样的数据类型
let arr: [i32;5] = [0, 0, 0, 0, 0];

// Rust 中数组每一项的类型都是一样的
// 如果需要需要每一项的类型不一样，可以结合 `enum` 一起使用
enum Foo {
    a(i32),
    b(bool),
    c{ x: i32, y: f64 },
}
// 和 `enum` 结合使用就能用 Array 存储不同的数据类型了
// 数组的每一项还是 `Foo` 枚举类型
// Rust 的枚举可以定义不同的数据类型，巧妙了让数组每一项可以存放 `不同的数据类型`
let baz: [Foo;3] = [
    Foo::a(100),
    Foo::b(true),
    Foo::c{ x: 100, y: 1.2 },
];

// 为了追求性能，在 Rust 中 Array 的长度是不变的，这是一个很大的限制
// 所以 Rust 中一般很少使用 Array, 和 TS 中比较相似的是 `Vector`
// Vector 在一些别的语言中又叫做 Slice (golang)，表示一种会动态扩容的线性数据结构
// 使用类型 `[T]` 或 `Vec<T>` 表示
let v1: [i32] = Vec::new(); // 初始化一个 `Vector`
let v2: Vec<i32> = Vec::new();
let v3: [i32] = vec![1, 2, 3]; // Rust 提供了一个 `vec!` 宏简化了 `Vector` 的初始化
let v4 = vec![1i32, 2, 3]; // 如果使用 `vec!` 宏可以不需要声明类型， Rust 会自动推导
```

#### 增删改查

```ts
// TypeScript

let arr: number[] = [];

arr.push(100); // 在数组末尾添加一个元素
arr.pop(); // 取出数组末尾的一个元素
arr.shift(); // 取出数组的第一个元素
arr.unshift(0); // 在数组头部插入元素

arr[0]; // 如果元素不存在会返回 `undefined`

arr[0] = 100; // 根据下标修改数组某一项的值
```

---

```rust
// Rust

// 使用 `mut` 修饰后的标识符才是可变的
let mut v: [i32] = vec![];

v.push(100); // // 在 `Vector` 末尾添加一个元素
// 100
println("{}", v.pop()); // 取出 `Vector` 末尾的元素

// 和 TS 一样通过 [] 索引，但是如果元素不存在会引发 panic(类似 TS 中的 throw Error，表示遇到无法处理的错误，需要终止程序)
v[0]

// 和 TS 一样，但是如果操作下标超出 Vector 最大范围会引发 panic
// Rust 对错误是不符合预期的操作是 0 容忍的，发现错误及时终止程序
v[0] = 100;
```
