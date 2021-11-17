> 前言："ES6 标准入门 --- 阮一峰" 阅读笔记
> 本文基于个人理解对 ES6 知识点做相应的总结，用于快速回顾各部分的关键信息。
> 若要深入理解，请仔细阅读 [ES6 入门教程](https://es6.ruanyifeng.com/)

# Map
## 性质
Map 可以看作是 ES6 对对象类型的一个拓展。JavaScript 的对象（Object），本质上是键值对的集合（Hash 结构），但是传统上只能用字符串当作键。而 Map 数据结构“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。
> Object 结构提供了“字符串—值”的对应，Map 结构提供了“值—值”的对应，是一种更完善的 Hash 结构实现。

## 声明和初始化
声明：ES6 为创建 Map 数据结构提供了 Map 构造函数。
```js
let map = new Map();
```

初始化：Map 构造函数可以接受一个数组成员为双元素的数组（或者具有 iterable 接口的其他数据结构，例如 Set）作为参数，用来初始化。
```js
// Map 将双元素识别为 [key, value]
const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]);

// 利用双元素+ iterable 接口的 Set 结构初始化 Map
const set = new Set([
  ['foo', 1],
  ['bar', 2]
]);
const m1 = new Map(set);
```

## 注意事项
1. 只有对同一个对象的引用，Map 结构才将其视为同一个键：Map 的键实际上是跟内存地址绑定的，只要内存地址不一样，就视为两个键。
> 该特性解决了同名属性碰撞（clash）的问题，在扩展别人的库的时候，如果使用对象作为键名，就不用担心自己的属性与原作者的属性同名。

2. Map 将 `0` 和 `-0` 视为同一个键，`NaN` 视为同一个键。 布尔值 `true`和字符串 `true` 则是两个不同的键。另外，`undefined` 和 `null` 也是两个不同的键。


## Map 实例的属性和方法
> 实例的属性和方法都挂载在原型对象上，因此下文用 `Map.prototype.xxx` 来表示实例的属性和方法。

### 属性
1. `Map.prototype.constructor`：构造函数，默认就是 Map 函数。
2. `Map.prototype.size`：返回 Map 实例的成员总数。

### 操作方法
1. `Map.prototype.set(key, value)`：设置键名key对应的键值为value，然后返回当前 Map 结构(原地操作)。如果key已经有值，则键值会被更新，否则就新生成该键。
2. `Map.prototype.get(key)`：读取并返回key对应的键值，如果找不到key，返回undefined。
3. `Map.prototype.has(key)`：返回一个布尔值，表示某个键是否在当前 Map 对象之中。
4. `Map.prototype.delete(key)`：删除某个键，返回true。如果删除失败，返回false。
5. `Map.prototype.clear()`：清除 Map 所有成员，没有返回值。

### 遍历方法
1. `Map.prototype.keys()`：返回键名的遍历器对象 (包含 next 方法的对象，调用 next 返回 {done: xxx, value: xxx})
2. `Map.prototype.values()`：返回键值的遍历器对象
3. `Map.prototype.entries()`：返回键值对的遍历器对象
4. `Map.prototype.forEach()`：使用回调函数遍历每个成员

注意事项：
1. Map 的遍历顺序 = 插入顺序。这意味着我们遍历 Map 调用时能保证按照添加顺序调用。
2. Map 结构的实例默认可遍历，默认遍历器生成函数就是它的 entries 方法。



# WeakMap
## 与 Map 的区别
### 本质区别
1. WeakMap 只接受对象作为键名（null除外），不接受其他类型的值作为键名。
2. WeakMap 的键名所指向的对象，不计入垃圾回收机制。

### 属性和方法区别
1. WeakMap 没有 size 属性
2. WeakMap 没有 clear 方法和所有遍历方法。

## 设计目的
当我们在某个对象上存放数据时，这个对象就会对存放的数据(键值对)持有引用。当存放的数据永远不再被使用时，由于它们仍被当前对象持有，因此不会被垃圾回收机制自动释放，我们就必须手动删除这个引用。
WeakMap 就是为了解决这个问题而诞生的，它的键名所引用的对象都是弱引用，即垃圾回收机制不将该引用考虑在内。因此，只要所引用的对象的其他引用都被清除，垃圾回收机制就会释放该对象所占用的内存。
> 一旦不再需要，WeakMap 里面的键名对象和所对应的键值对会自动消失，不用手动删除引用。


!!! 注意，WeakMap 弱引用的只是键名，而不是键值。
```js
const wm = new WeakMap();
let key = {};
let obj = {foo: 1};

wm.set(key, obj);
obj = null;
wm.get(key)
// Object {foo: 1}
```
可以看到，尽管外界不再使用 obj，但由于 WeakMap 只是弱引用键名，因此，其内部保存的键值不会受外部影响。
> 可以理解为，WeakMap 和外界相关的部分只存在于键名，而键名和键值之间的映射关系是由 WeakMap 自身维护的。
