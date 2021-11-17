> 前言："ES6 标准入门 --- 阮一峰" 阅读笔记
> 本文基于个人理解对 ES6 知识点做相应的总结，用于快速回顾各部分的关键信息。
> 若要深入理解，请仔细阅读 [ES6 入门教程](https://es6.ruanyifeng.com/)

# Set
## 性质
Set 是 ES6 新提供的一种数据结构，它内部成员值唯一 (没有重复值)，是类数组对象 (因此可以应用扩展运算符或 `Array.from()` 等消耗 iterator 的操作)。

## 声明和初始化
声明：ES6 为创建 Set 数据结构提供了 Set 构造函数。
```js
let set = new Set();
```

初始化：Set 构造函数可以接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数，用来初始化。
```js
let set1 = new Set([1, 2, 3, 4]); // 接受数组
let set2 = new Set(document.querySelectorAll('div')); // 接受具有 iterable 接口的数据结构，document.querySelectorAll() 返回一个 NodeList 静态实例，它是一个类数组对象，具有 iterable 接口
```

## Set 实例的属性和方法
> 实例的属性和方法都挂载在原型对象上，因此下文用 `Set.prototype.xxx` 来表示实例的属性和方法。

### 属性
1. `Set.prototype.constructor`：构造函数，默认就是 Set 函数。
2. `Set.prototype.size`：返回 Set 实例的成员总数。

### 操作方法
1. `Set.prototype.add(value)`：向 Set 数组结构中，添加某个值，返回 Set 结构本身 (原地操作)。
2. `Set.prototype.delete(value)`：删除 Set 数组结构中的某个值，返回一个布尔值，表示删除是否成功。
3. `Set.prototype.has(value)`：判断某个值是否是 Set 的成员，返回一个布尔值。
4. `Set.prototype.clear()`：清除 Set 所有成员，没有返回值。

### 遍历方法
1. `Set.prototype.keys()`：返回键名的遍历器对象 (包含 next 方法的对象，调用 next 返回 {done: xxx, value: xxx})
2. `Set.prototype.values()`：返回键值的遍历器对象
3. `Set.prototype.entries()`：返回键值对的遍历器对象
4. `Set.prototype.forEach()`：使用回调函数遍历每个成员

注意事项：
1. Set 的遍历顺序 = 插入顺序。这个特性有时非常有用，比如使用 Set 保存一个回调函数列表，调用时就能保证按照添加顺序调用。
2. 由于 Set 结构没有键名，只有键值（或者说键名和键值是同一个值），所以keys方法和values方法的行为完全一致。entries方法返回的遍历器，同时包括键名和键值，所以每次输出一个数组，它的两个成员完全相等。
3. Set 结构的实例默认可遍历，默认遍历器生成函数就是它的 values 方法。

## 应用
1. 数组去重
```js
[...new Set(array)] // 接收一个数组，生成 set 后再用扩展运算符重新构建一个数组
```

2. 字符串去重
```js
[...new Set('abbecff')].join('') // 字符串是类数组对象，具有 iterable 接口
```

3. 并集 / 交集 / 差集
```js
// 并集
let union = new Set([...a, ...b]);

// 交集
let intersect = new Set([...a].filter(x => b.has(x)));

// (a 相对于 b 的) 差集
let difference = new Set([...a].filter(x => !b.has(x)));
```