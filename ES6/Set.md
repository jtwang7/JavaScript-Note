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


# WeakSet
## 与 Set 的区别
### 本质区别
1. WeakSet 的成员只能是对象，而不能是其他类型的值。
2. WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用。
3. ES6 规定 WeakSet 不可遍历。
> 原因：
> 1. WeakSet 内部有多少个成员，取决于垃圾回收机制有没有运行，运行前后很可能成员个数是不一样的，而垃圾回收机制何时运行是不可预测的。
> 2. 成员都是弱引用，随时可能消失，遍历机制无法保证成员的存在，很可能刚刚遍历结束，成员就取不到了。

首先我们要知道垃圾回收的运行机制：垃圾回收机制根据对象的可达性（reachability）来判断回收，如果对象还能被访问到，垃圾回收机制就不会释放这块内存。
在 Set 中的对象类型，除了可能在其他地方被引用之外，还会因为被 Set 结构自身所引用 (因为 Set 包含了该对象)，因此，当外界不再引用该对象时，由于仍存在 Set 对它的引用，垃圾回收机制始终不会自动回收它的内存。此时假如我们不手动取消 Set 对它的引用，导致内存无法释放，进而可能会引发内存泄漏。而对于 WeakSet 来说，由于它内部采用弱引用，因此都不会计入垃圾回收机制，所以就不存在这个问题。
> 弱引用：如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 WeakSet 之中。

### 属性和方法上的区别
1. WeakSet 没有 size 属性
2. WeakSet 没有 clear 方法以及所有遍历方法
> 这些差异都与 WeakSet 无法保证成员个数有关

## 使用场景
WeakSet 适合临时存放一组对象，以及存放跟对象绑定的信息。只要这些对象在外部消失，它在 WeakSet 里面的引用就会自动消失。最常见的应用：存放 DOM 节点信息，因为 DOM 节点容易发生删改，用 WeakSet 存储不用担心这些节点从文档移除时，会引发内存泄漏。
> 形象表述，我们可以将 WeakSet 理解为一个专门用于存放东西的仓库，仓库里的东西若被外界使用，那么它就始终记录在仓库保管档案中，若它永远都不在被外界使用了，那仓库就没有继续保管它的必要，便会自动将其从档案记录中删除。