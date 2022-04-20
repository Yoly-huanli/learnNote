[toc]

# 新的数据结构

## Set()不可重复类型

不能去重引用类型，只能去重基本类型

### 主要方法

```js
constructor：构造函数，返回Set
size：返回实例成员总数

add()：添加值，返回实例
delete()：删除值，返回布尔,true或者false
has()：检查值，返回布尔
clear()：清除所有成员,返回undefined
keys()：返回以属性值为遍历器的对象
values()：返回以属性值为遍历器的对象
entries()：返回以属性值和属性值为遍历器的对象
forEach()：使用回调函数遍历每个成员
```

其中，entries()属性，以及对应的values(),key()属性

```js
let set = new Set(['a', 'b', 'c']);
console.log(set.entries()); // SetIterator {"a", "b", "c"}
console.log([...set.entries()]); // [["a", "a"], ["b", "b"], ["c", "c"]]
```

### 模拟实现Set

```js
function Set() {
    var items = {};
    this.has = function(value){
        return items.hasOwnProperty(value);
    },
    this.add = function(value){
        if (!this.has(value)) {
            items[value] = value;
            return true;
        }
        return false;
    },
    this.remove = function(value){
        if (this.has(value)) {
            delete value;
            return true;
        }
        return false;
    },
    this.clear = function(){
        items={};
    },
    this.size = function(){
        var count = 0;
        for(var prop in items){
            if (items.hasOwnProperty(prop)) {
                ++count;
            }
        }
        return count;
    },
    this.values= function(){
        var values = [];
        for(var value in items){
            if (items.hasOwnProperty(value)) {
                values.push(value);
            }
        }
        return values;
    },
```

### 与数组关系

```js
const a= new Set([1,2,3,1,2,3])
console.log(a)//去重，Set(3){1, 2, 3}
a.add(4) //a.delete(),a.keys,a.values,a.entries
console.log(a) //Set(4){1, 2, 3, 4}
const b=new Set([1,2,5,7,7])
console.log(new Set([...a,...b]))//并集{1, 2, 3, 4, 5,7}
```

使用

```js
//数组去重
[...new Set(arr)]或Array.from(new Set(arr))
//数组取并集
new Set([...a, ...b])
//字符串去重
[...new Set(str)].join("")
//如
var str='hhhhhhhh';
var thestr=new Set(str);
console.log(thestr); //Set(1){"h"}
console.log([...thestr].join('')); //h
```

### 与对象的区别

对象的key都会被转换为字符串,对象作为key，会使用objkey.toString()，结果是'[object Object]'

```js
let objkey={
    'mimi':'nini'
}
let obj={
    '1':'hello',
     1:'hi',
     [objkey]:'miao'
}
console.log(obj)
// {1: "hi", [object Object]: "miao"}
```

### 类型转换

- Array.from()

- [...]

*********

## 2.WeakSet()

- WeakSet的成员只能是对象,Set的成员不仅可以是对象还可以是其他类型的值。
- 成员都是弱引用，垃圾回收机制不考虑WeakSet结构对此成员的引用,其他对象不再引用成员时，垃圾回收机制会自动回收此成员所占用的内存，不考虑此成员是否还存在于WeakSet结构中
- 只能使用add(),delete(),has()
- 不能遍历

```js
// 储存DOM节点：DOM节点被移除时自动释放此成员，不用担心这些节点从文档移除时会引发内存泄漏
let hd={'hello':'hi'}; //引用次数1
let hd2=hd; ////引用次数2
let newweakset=new WeakSet();
newweakset.add(hd); //引用次数不变
hd=null; //引用次数减1
hd2=null;//引用次数减1
console.log(newweakset) //[[Entries]] 0:value: {hello: "hi"}__proto__: WeakSet
//WeakSet还认为自己有值，会显示出来，实际上没有
console.log(newweakset.has(hd)) //false
```

*******

## 3.Map类型

键值对，键可以是对象

```js
const map1 = new Map()
const objkey = {p1: 'v1'}

map1.set(objkey, 'hello')
console.log(map1.get(objkey)) //hello
```
+ 可以接收数组作为参数，与Set相同，需要外边[]包裹

```js
const map2 = new Map([
  ['name', 'Aissen'],
  ['age', 12]
])
```
+ map.size获取大小长度
+ 可以用set设置键值对且支持链式调用

```js
map4.set('k2', 2).set('k3', 4).set('k4', 5)
```
+ map.has('k1')判断指定的键是否存在，delete删除，clear清空
+ map1.keys,values,entries

*****

## 4.Set、Map、WeakSet 和 WeakMap 的区别

    Set
        成员唯一、无序且不重复
        [value, value]，键值与键名是一致的（或者说只有键值，没有键名）
        可以遍历，方法有：add、delete、has
    WeakSet
        成员都是对象
        成员都是弱引用，可以被垃圾回收机制回收，可以用来保存DOM节点，不容易造成内存泄漏
        不能遍历，方法有add、delete、has
    Map
        本质上是键值对的集合，类似集合
        可以遍历，方法很多可以跟各种数据格式转换
    WeakMap
        只接受对象作为键名（null除外），不接受其他类型的值作为键名
        键名是弱引用，键值可以是任意的，键名所指向的对象可以被垃圾回收，此时键名是无效的
        不能遍历，方法有get、set、has、delete

# proxy

