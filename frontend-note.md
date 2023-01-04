# HTML CSS

## Flex 布局

### 使用 flex 布局

1. 容器

   ```css
   .box {
     display: flex;
   }
   ```

2. 行内元素

   ```css
   .box {
     display: inline-flex;
   }
   ```

flex 布局后，`float`、`vertical-align`、`clear`失效。

### 容器属性

1. `flex-direction`：主轴方向

   属性值

   - `row`：子元素起点在左，左到右。
   - `row-reverse`：起点在右，右到左。
   - `column`：起点在上，上到下。
   - `column-reverse`

2. `flex-wrap`：换行

   - `nowarp`：不换行，缩小各子元素宽度。
   - `warp`：换行。
   - `warp-reverse`：换行，但从下至上，原第一排在最下方。

3. `flex-flow`：`flex-direction`和`flex-wrap`缩写。

   ```css
   flex-flow: column nowarp;
   ```

4. `justify-content`：项目在主轴上对齐方式

   - `flex-start`：左对齐，默认值。
   - `flex-end`：右对齐。
   - `center`：居中。
   - `space-between`：两端对齐，项目间间隔相等。
   - `space-around`：项目间间隔相等。

5. `align-items`：项目在交叉轴的对齐方式

   - `flex-start`
   - `flex-end`
   - `center`
   - `base-line`：和项目第一行文字基线对齐。
   - `strench`：默认值，没有高度将撑满容器。

6. `align-content`：多根轴线的对齐方式

   - `flex-start`：左对齐，默认值。
   - `flex-end`：右对齐。
   - `center`：居中。
   - `space-between`：两端对齐，项目间间隔相等。
   - `space-around`：项目间间隔相等。
   - `stretch`

### 项目属性

1. `order`：项目排列顺序，默认 0，越小越靠前。

2. `flex-grow`：项目放大比例，默认 0。

   如果所有项目的`flex-grow`属性都为 1，则它们将等分剩余空间（如果有的话）。如果一个项目的`flex-grow`属性为 2，其他项目都为 1，则前者占据的剩余空间将比其他项多一倍。

3. `flex-shrink`：项目缩小比例，默认 1。

   所有项目的`flex-shrink`属性都为 1，当空间不足时，都将等比例缩小。如果一个项目的`flex-shrink`属性为 0，其他项目都为 1，则空间不足时，前者不缩小。

4. `flex-basis`：项目占据的主轴空间，默认为 auto 即项目原本大小。

5. `flex`：`flex-grow`、`flex-shrink`、`flex-basis`三者合一。

   `flex:1`代表`flex-grow:1`、`flex-shrink:1`、`flex-basis:0%`，项目将自动平分剩余空间。

6. `align-self`：覆盖`align-items`属性，允许单个项目在交叉轴上有不一样的对齐方式。

## Canvas

### 线条

- `moveTo(x,y)`：起点
- `lineTo(x,y)`：终点
- `stroke()`：绘制

### 矩形

`fillRect(*x,y,width,height)`

参数：

- `x、y`：左上角坐标
- `width、height`：宽高

### 圆

- `beginPath()`

  开始一条路径，或重置当前的路径。

- `arc(x, y, r, startAngle, endAngle)`

  参数：

  - `x、y`：圆中心坐标
  - `r`：圆半径
  - `startAngle、endAngle`：起始角、结束角。

- `stroke()`

### 文本

- `font`：设置属性

  ```javascript
  ctx.font = "30px Arail";
  ```

- `fillText(str, x, y)`：绘制实心文本。

- `strokeText(str, x, y)`：绘制空心文本。

### 图像

`drawImage(image,x,y*)`

注意需图片加载出来`onload`后再调用。

### 渐变

- 线性渐变

  `createLinearGradient(x,y,x1,y1)`

- 圆/径向渐变

  `createRadialGradient(x,y,r,x1,y1,r1) `

- 颜色停止

  `addColorStop(location, color)`

```javascript
const grd = ctx.createLinearGradient(0, 0, 200, 0);
grd.addColorStop(0, "red");
grd.addColorStop(1, "white");

ctx.fillStyle = grd;
ctx.fillRect(50, 125, 150, 75);
```

### 水印实战

原理：生成单个`canvas`水印，放到大`div`中，此大`div`设置`absolute`定位、高`z-index`、`background-img:url()`，依靠背景图片默认`repeat`。

css 解读

1. `position: absolute`：将元素从文档流拖出来，对其最接近的一个具有定位属性的父定位包含框进行绝对定位。如果不存在这样的定位包含框，则相对于浏览器窗口。
2. `pointer-events: none`：该元素永远不会成为鼠标事件的 target。当其后代元素的`pointer-events`属性指定其他值时，鼠标事件可以指向后代元素。

```javascript
/*
 * @Author: cbw
 * @Date: 2022-09-23 16:21:05
 * @LastEditors: cbw
 * @LastEditTime: 2022-09-26 11:22:07
 * @Description:
 */
class WaterMark {
  #canvasOptions; // canvas默认配置
  #canvasIndividualOptions; //canvas个性化配置
  #waterMarkStyle; // 水印默认配置
  #waterMarkIndividualStyle; // 水印个性化配置
  #wm; // 水印DOM
  #Uuid; // 唯一id
  #waterMarkStyleStr; // style字符串

  constructor(canvasOptions = {}, waterMarkStyle = {}) {
    // canvas默认配置
    this.#canvasOptions = {
      width: 400, // canvas宽
      height: 400, // canvas高
      font: "normal 16px 思源黑体_ Regular",
      fillStyle: "rgba(10, 100, 80, .2)", // 文本颜色
      textAlign: "center",
      fillTextArr: ["Boen", "3150987521986"], // 文本
      rotate: -20, // 旋转角度
      fillTextX: 100, // 文本横坐标
      fillTextY: 100, // 文本纵坐标
    };
    // 水印默认配置
    this.#waterMarkStyle = {
      position: "absolute",
      left: 0,
      top: 0,
      right: 0,
      bottom: 0,
      "z-index": "99999",
      "pointer-events": "none", // 永远不成文鼠标事件的 target
      container: document.body, // 水印创建位置
    };
    // canvas个性化配置
    this.#canvasIndividualOptions = canvasOptions;
    // 水印个性化配置
    this.#waterMarkIndividualStyle = waterMarkStyle;
    // 初始化
    this.#init();
  }

  /**
   * 创建canvas
   * @param {Object} options 选项
   * @returns canvasUrl
   */
  createCanvasUrl(options = {}) {
    const canvas = document.createElement("canvas"); // 创建canvas
    // 设置属性
    canvas.setAttribute("width", options?.width ?? this.#canvasOptions.width);
    canvas.setAttribute(
      "height",
      options?.height ?? this.#canvasOptions.height
    );
    const ctx = canvas.getContext("2d");
    ctx.font = options?.font ?? this.#canvasOptions.font;
    ctx.fillStyle = options?.fillStyle ?? this.#canvasOptions.fillStyle;
    ctx.textAlign = options?.textAlign ?? this.#canvasOptions.textAlign;
    ctx.rotate(
      (Math.PI / 180) * (options?.rotate ?? this.#canvasOptions.rotate)
    );
    const fillTextArr = options?.fillTextArr || this.#canvasOptions.fillTextArr;
    for (let i = 0; i < fillTextArr.length; i++) {
      const fillText = fillTextArr[i];
      // 防止多文本重叠
      ctx.fillText(
        fillText,
        options?.fillTextX ?? this.#canvasOptions.fillTextX,
        (options?.fillTextY ?? this.#canvasOptions.fillTextY) + 20 * i
      );
    }
    const canvasUrl = canvas.toDataURL(); // 获取base64图片URL
    return canvasUrl;
  }

  /**
   * 创建水印
   * @param {String} bgcImgUrl
   * @param {Object} options
   */
  createWaterMark(bgcImgUrl, options = {}) {
    this.#Uuid = this.getUuid();
    const waterMark = document.createElement("div");
    waterMark.setAttribute("id", this.#Uuid);
    this.#waterMarkStyleStr = "";
    // 拼接成style字符串
    for (let key in this.#waterMarkStyle) {
      this.#waterMarkStyleStr +=
        key + `:${options?.[key] ?? this.#waterMarkStyle[key]};`;
    }
    this.#waterMarkStyleStr += `background-image:url(${bgcImgUrl});`;
    waterMark.setAttribute("style", this.#waterMarkStyleStr); // 设置style属性
    options?.container?.appendChild(waterMark) ??
      this.#waterMarkStyle.container.appendChild(waterMark);
    return waterMark;
  }

  /**
   * 生成uuid
   * @returns
   */
  getUuid() {
    return "xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx".replace(
      /[xy]/g,
      function (c) {
        var r = (Math.random() * 16) | 0,
          v = c == "x" ? r : (r & 0x3) | 0x8;
        return v.toString(16);
      }
    );
  }

  /**
   * 初始化
   */
  #init() {
    const base64Url = this.createCanvasUrl(this.#canvasIndividualOptions); // base64图片
    this.#wm = this.createWaterMark(base64Url, this.#waterMarkIndividualStyle); // 创建水印
    this.#observer();
  }

  /**
   * 移除水印
   */
  #remove() {
    const wmDiv = document.getElementById(this.#Uuid);
    // 防止预移出节点不存在
    if (!wmDiv) {
      this.#waterMarkIndividualStyle?.container?.removeChild(this.#wm) ??
        this.#waterMarkStyle.container.removeChild(this.#wm);
    }
  }

  /**
   * 防止控制台删除水印
   */
  #observer() {
    const targetNode =
      this.#waterMarkIndividualStyle?.container ??
      this.#waterMarkStyle.container; // 监听节点
    // 监听配置
    const observerConfig = {
      subtree: true,
      childList: true,
      attributes: true,
    };
    // 创建observer对象
    const observer = new MutationObserver(() => {
      const wmDiv = document.getElementById(this.#Uuid);
      // wmDiv不存在
      if (!wmDiv) {
        this.init();
        return;
      }
      // css样式被修改
      if (wmDiv.getAttribute("style") !== this.#waterMarkStyleStr) {
        wmDiv.setAttribute("style", this.#waterMarkStyleStr);
      }
    });
    // 开始监听
    observer.observe(targetNode, observerConfig);
  }
}

const wm = new WaterMark();
const wm2 = new WaterMark(
  {},
  (waterMarkStyle = { left: "150px", top: "150px" })
);
```

# JavaScript

## ES6

#### Symbol-新数据类型

Symbol 是 JavaScript 的第七种数据类型。常用于表示独一无二的字符串，例如函数名等。

##### 定义

1. `Symbol()`

   局部，相同的串并不代表是同一个 Symbol。

2. `Symbol.for()`

   全局，开辟内存空间，相同的串代表是同一个 Symbol。

##### 描述

1. `symbol.description`：通用。
2. `Symbol.keyfor()`：由`Symbol.for()`建立的对象独享。

##### 用途

1. 唯一的 key。

2. 对象私有属性。

   遍历时，将无法取到以 Symbol 对象为 key 的属性。

##### 注意事项

1. `for...in`取不到以 Symbol 对象为 key 的属性。
2. `Object.getOwnPropertySymbols()`可以拿到以 Symbol 对象为 key 的属性。
3. `Reflect.ownKeys()`能够获取所有属性。

#### 数据结构

##### Set

类似数组，但所有 value 唯一。

常用

- 属性：`size`
- 方法：
  1. 增：`add`
  2. 查：`has`
  3. 删：`delete`
  4. `clear`：清空。
  5. `values`：获得 Set 迭代对象。

与数组互转

- 转数组：`Array.from()`、`[...set]`
- 转 Set：`new Set(arr)`

##### WeakSet

和`Set`差不多，但是`WeakSet`有几个不同点：

1. 只能存放<u>引用数据类型</u>。

2. `WeakSet`的对象是弱引用。

   `WeakSet`对对象的引用不会被考虑进垃圾回收机制，只要没有引用该对象，该对象就会被回收，无论是否在`WeakSet`中被引用。因此容易被弱引用造成影响的方法都不被提供，如`values`、`size`、`for of`等。

   ```javascript
   const objs = new WeakSet();
   let obj = { qq: "123" };
   objs.add(obj);
   objs.add({
     a: "123",
     b: {
       c: "hello",
     },
   });
   console.log(objs);
   setTimeout(() => {
     console.log(objs);
   }, 5000);
   ```

##### Map

类似对象，但所有 key 唯一，且 key 可以是任意值（对象 key 本质是字符串）。

常用

1. 属性
2. 方法
   - 增：
     - `set`，key 同样时，即为更改。
     - `new Map([[key, value], [], ...])`
   - 删：`delete`
   - 查：`has`、`get`
3. 遍历
   - `entries`，可以解构一下[key, value]
   - `keys`
   - `values`
   - `for...of`
   - `forEach`

与数组互转

- 转数组：`[...map]`
- 转 map：`new Map([[key, value], [], ...])`

##### WeakMap

与 map 差不多，但**键 key 必须是引用数据类型**，且 WeakMap 是弱引用类型。

变化：`size`、`keys`等方法都用不了，因为是弱引用，不加入垃圾回收机制。

#### 运算符的扩展（ES6）

##### 指数运算符`**`

```js
num1 ** num2 = num1^num2
// 右结合
num1 ** num2 * num3 = num1^(num2^num3)
// 新赋值运算符
num1 **= 3 // num1 ^3
```

##### 链判断运算符`?.`

三种用法：

1. 对象属性

   `obj`是否存在？`obj.obj2`是否存在？`obj.obj2.data`是否存在？顺序着来，不存在直接返回`undefined`。

   ```js
   const data = obj?.obj2?.data || {};
   ```

2. 对象方法

   ```js
   obj?.func();
   ```

3. 函数调用

   ```js
   func?.();
   ```

##### Null 判断运算符`??`

通常赋默认值是以`||`方式提供，但 如`null、undefined、NaN、0、""、false`也会出现会被囊括其中。Null 判断运算符`??`只有运算符左侧的值为`undefined`、`null`时才会返回右侧的值。

```js
const num = 0 ?? 1; // num = 0
const num = obj?.num ?? 1; // num = 1
```

## 文字翻转

```js
split("").reverse().join("");
```

## 类型判断

### `typeof`

```js
type of 1 // number
type of '1' // string
type of {} // object
type of [1, 2, 3] // object
type of function run() {} // function
type of hdcms // hdcms未定义情况下：undefined
type of hdcms // hdcms已声明情况下：undefined，因为初始值为undefined
```

### `instanceof`

```js
[] instanceof Array // true
{} instanceof Object // true
```

## 同源、跨域页面间通信

### 同源页面

同源：协议、url、端口号一致。

1. A 页面

   ```javascript
   window.addEventListener("storage", (e) => {
     console.log(e);
   });
   ```

2. B 页面

   ```javascript
   localStorage.setItem(key, value);
   ```

### 跨域页面

跨域：协议、url、端口号至少一个不一样。

1. A 页面

   ```javascript
   window.addEventListener("message", (e) => {
     console.log(e);
     // e.origin 查看源地址
   });
   ```

2. B 页面

   ```javascript
   const targetWindow = window.open("http://localhost:10001/user");
   setTimeout(() => {
     targetWindow.postMessage("来自10002的消息", "http://localhost:10001");
   }, 3000);
   ```

## 内存泄漏

不再用到的内存，没有及时释放，叫做**内存泄漏**。

### 引起的原因

1. 全局变量
2. 定时器
3. 闭包使用不当
4. 网络回调函数
5. DOM 元素（js、DOM 树都清理）

### Garbage Collection（GC）

1. 标记清除法

   根对象开始寻找可引用的对象，未被引用的对象移出。

2. 引用计数法

   新引用+1，移出引用-1，引用为 0 的对象回收。

## Websocket

### 基本用法

#### 方法

1. `close()`：主动关闭连接。
2. `send()`：客户端想服务端发送数据。

#### 事件

1. `close`：连接断开触发。
2. `error`：连接错误触发。
3. `message`：收到服务端发送的数据。
4. `open`：打开连接时触发。

例子

```javascript
const ws = new WebSocket(url);
// 建立连接
ws.addEventListener("open", () => {
  ws.send("hello server"); // 向服务端发送数据
});
// 接受消息
ws.addEventListener("message", (e) => {
  // e.data
});
// 断开连接
ws.addEventListener("close", () => {});
// 断开连接
ws.addEventListener("error", () => {});
```

对于后端，每创建一个新的连接，都会有一个`conn`对象。

## try...catch

### 语法

```javascript
try {
} catch (err) {
  throw new Error();
} finally {
  // 总会做的事情
}
```

### 注意事项

```javascript
function func() {
  try {
    return 1;
  } catch (err) {
    /* ... */
  } finally {
    alert("finally");
  }
}
```

这个例子，先`alert`、后`return`。

## 全局变量污染

1. 立即执行函数

   ```javascript
   (function (window) {
     function show() {
       console.log(`js2.js ---`);
     }

     window.js2 = {
       show,
     };
   })(window);
   ```

2. 块作用域

   ```javascript
   {
     let show = function () {
       console.log(`js1.js ---`);
     };

     window.js1 = {
       show,
     };
   }
   ```

## Web Workers

### 基本使用

1. 检测浏览器是否支持`Web Workers`

   ```javascript
   if (window.Worker) {
   }
   ```

2. 创造一个`worker`

   ```javascript
   const worker1 = new Worker(aURL, options);
   ```

   此`worker`指定一个脚本来执行线程。

   脚本里(main.js)可以写一个事件处理函数作为响应

   ```javascript
   onmessage = function (e) {
     // e.data
     postMessage(data);
   };
   ```

3. 向`worker`发送数据

   ```javascript
   worker1.postMessage(data);
   ```

4. 监听`worker`返回的消息

   ```javascript
   worker1.onmessage = function (e) {};
   ```

5. 杀掉`worker`

   ```
   worker1.terminate()
   ```

## 字符串常用方法

### 查找

1. `indexOf`、`lastIndexOf(char, loc)`

2. `includes`

3. `startsWith(str)`

   是否以 str 开头。

### 截取

1. `substr(start, num)`
2. `slice(start, end)`
3. `subString(start, end)`：start 和 end 为非负的整数。

### 替换

`replace(word, replaceWord)`

### 重复

`str.repeat(num)`

## 数组常用方法

### 检测

```javascript
Array.isArray();
```

### 数组与字符串互转

#### 转为字符串

1. `join()`

2. `String()`、`toString()`

   默认间隔英文逗号“,”。

#### 转为数组

1. `split()`

2. `Array.from(obj, map)`

3. `[...arr] = str`

   原理解构赋值+rest 操作符。str 本身是有 length 属性的字符串，所以每个字符都放到了变量 arr 里。

### 元素操作

#### 添加

1. `push`

2. `unshift`

   添加到数组头部。

3. `splice(start, delNum, ele1,...)`

#### 删除

1. `pop`

2. `shift`

   删除数组尾部元素。

3. `splice(start, delNum)`

#### 移动

1. `splice`

   ```javascript
   function itemMove(arr, from, to) {
     arr.splice(to, 0, ...arr.splice(from, 1));
     return arr;
   }
   ```

### 替换

以 obj 替换已有数组[start, end)所有元素。

```javascript
arr.fill(obj, start, end);
```

### 清空数组

1. `arr = []`
2. 推荐：`arr.length = 0`
3. `arr.splice(0, arr.length)`

### 创建数组

1. `Array.of(params,...)`

   ```javascript
   const arr = Array.of(1, 2, 3, true, "123");
   ```

2. `Array.from()`

   通过拥有 length 属性的对象或可迭代的对象来返回一个数组。

   ```javascript
   Array.from(obj, mapFunc, this);
   ```

   可实现浅拷贝，如

   ```javascript
   const arr = Array.from([{ a: "1" }, "b"]);
   ```

3. `new Array()`

   仅传一个参数 num，即创建 num 个为 undefined 的元素。多个参数与`Array.of`一致。

### 数组截取

1. `slice`

   截取数组[start, end)部分，返回一个新数组。不对原数组操作。

   ```javascript
   arr.slice(start, end);
   ```

   注意

   1. 只传一个参数时，end 代表`arr.length`。
   2. slice(0)是浅拷贝！！！

2. `splice`

   此函数会**修改数组本身**，返回被修改的内容。

   ```javascript
   arr.splice(start);
   ```

### 查找

以下查找，针对数组里的对象都是“址”查找，不是一样的地址，不会匹配。

1. `indexOf`

   查找在数组中某一指定元素（必须`===`）的第一次出现的位置。返回 index、-1。

   ```js
   arr.indexOf(obj);
   ```

2. `includes`

   判断一个数组是否包含一个指定的值，返回 true、false。

   ```javascript
   arr.includes(ele);
   ```

3. `find`

   查找通过测试的第一个值，返回查到的值、undefined。

   ```javascript
   arr.find(item => item > 18)

   // 手撕
   Array.prototype.find(callback) {
     for(let item of this) {
       if(callback(item)) return item;
     }
     return undefined;
   }
   ```

4. `findIndex`

   查找通过测试的第一个值，返回查到的值的索引、-1。

   ```javascript
   arr.findIndex((item) => item > 18);
   ```

5. `some`

   查找是否有通过测试的值，返回 true、false。

   ```javascript
   arr.some((item, index) => item > 18);
   ```

6. `every`

   类 some，查找是否都有通过测试，返回 true、false。

7. `filter`

   过滤不符合条件的元素，返回一个新数组（浅拷贝）。

   ```javascript
   arr2 = arr.filter((item) => item > 18);
   ```

   原理：

   ```javascript
   Array.prototype.filter = function (callback) {
     const arr = [];
     for (const iterator of this) {
       callback(iterator) && arr.push(iterator);
     }
     return arr;
   };
   ```

### 合并

1. `arr.concat(array2,...)`

   返回一个新数组，将 arr、arr2,...连接。传入的参数如果是数组，将被展开一层。如果是非数组，将直接作为元素添加。

2. `[...arr1, ...arr2]`

3. `Object.assign()`

### 排序

```javascript
arr.sort((a, b) => a - b); // 小于0升序，大于0降序
```

原理：

```javascript
Array.prototype.swap = function (index_a, index_b) {
  const box = this[index_a];
  this[index_a] = this[index_b];
  this[index_b] = box;
};

Array.prototype.sort = function (callback) {
  for (let i = 0; i < this.length; i++) {
    for (let j = i + 1; j < this.length; j++) {
      if (callback(this[i], this[j]) > 0) {
        this.swap(i, j);
      }
    }
  }
};
```

### 翻转

```javascript
arr.reverse();
```

### 集合运算

#### 去重

```javascript
[...new Set(arr)];
```

#### 交集

```javascript
function show(arr1, arr2) {
  const arr = [];
  const set1 = new Set(arr1);
  const set2 = new Set(arr2);
  return [...set1].filter((item) => set2.has(item));
}
```

#### 并集-交集

```javascript
function show(arr1, arr2) {
  const set1 = new Set(arr1);
  const set2 = new Set(arr2);

  const fn = (s1, s2) => {
    const arr = [];
    for (let item of s1) {
      if (!s2.has(item)) {
        arr.push(item);
      }
    }
    return arr;
  };

  const res1 = fn(set1, set2);
  const res2 = fn(set2, set1);
  return [...res1, ...res2];
}
```

#### 差集

```javascript
arr1.filter((item) => !new Set(arr2).has(item));
```

### 高阶

1. 累计：`array.reduce(function(pre, currentValue, currentIndex, arr), initialValue)`

   参数：`pre`是上次的返回值，第一次为`initialValue ?? array[0]`。

   原理

   ```javascript
   Array.prototype.reduce = function (callback, initValue) {
     // 检查数组是否为null或undefined
     if (this == undefined) throw new TypeError("this is null or undefined");
     // 检查callback是否是函数
     if (typeof callback !== "function")
       throw new TypeError(`${callback} is not a function`);

     const arr = Object(this); // 确保arr为对象
     const arrLength = arr.length >>> 0; // 确保length为正数

     let index = 0; // 第一个有效值索引
     if (initValue === undefined) {
       // 寻找第一个有效值
       while (index < arrLength && !(index in arr)) index++;
       // index超出数组范围，证明是空数组
       if (index >= arrLength) {
         throw new TypeError("empty array");
       }
       // 设置初始值
       initValue = initValue ? initValue : arr[index++];
     }
     let res = initValue; // 初始化结果
     // 计算结果
     for (let i = index; i < arrLength; i++) {
       if (i in arr) res = callback(res, this[i], i, this);
     }
     return res;
   };
   ```

2. 映射：`map`

   原理

   ```javascript
   Array.prototype.map = function (callback = (item) => item) {
     const newArr = [];
     this.forEach((element) => {
       newArr.push(callback(element));
     });
     return newArr;
   };
   ```

## 对象

### 属性

#### 属性特征

1. 设置属性特征

   ```javascript
   Object.defineProperty(obj, property, {
     writable: false, // 不可写
     enumerable: false, // 不可遍历
     configurable: false, // 不可配置、删除
     value
   });

   Object.definePropertys(obj,{property1, property2,...})
   ```

2. 读取属性特征

   ```javascript
   Object.getOwnPropertyDescriptor(obj, propertyStr);

   Object.getOwnPropertyDescriptors(obj);
   ```

#### 删除属性

```javascript
delete obj.property;
```

#### 禁止添加属性

```javascript
Object.preventExtensions(obj);
```

检测是否禁止：`Object.isExtensible()`

#### 封闭对象

禁止添加属性，且对象不可配置

```javascript
Object.seal(obj);
```

检测是否被封禁：`Object.isSealed()`

#### 冻结对象

对象不能被修改，不可增删属性、不可配置、不可写，原型不可修改。

```javascript
Object.freeze(obj);
```

#### 检测某属性是否存在

1. `hasOwnProperty`：不查原型链
2. `in`：查原型链

#### 访问器

```javascript
const data = {
  set property(value) {},
  get property() {},
};
```

注意，`set`和打点访问赋值同时针对一个属性，`set`优先。

### 遍历

`for in`

### 拷贝

浅拷贝

1. `Object.assign({}, obj1, obj2)`
2. `{...obj1, ...obj2}`

深拷贝

1. 老版

   ```javascript
   function cloneDeep(obj) {
     if (Array.isArray(obj)) {
       const arr = [];
       for (const ele of obj) {
         arr.push(cloneDeep(ele));
       }
       return arr;
     } else if (obj instanceof Object) {
       const copy_obj = {};
       for (const key in obj) {
         // for in会遍历所有可枚举属性
         if (obj.hasOwnProperty(key)) {
           copy_obj[key] = cloneDeep(obj[key]);
         }
       }
       return copy_obj;
     } else {
       return obj;
     }
   }
   ```

2. 新版

   ```javascript
   function cloneDeep(obj) {
     if (obj instanceof Object) {
       const res = Array.isArray(obj) ? [] : {};
       for (const [key, value] of Object.entries(obj)) {
         res[key] = cloneDeep(value);
       }
       return res;
     } else {
       return obj;
     }
   }
   ```

### 创建对象

```javascript
Object.create(prototype, properties);
```

## if()表达式和==原理

### if()

```javascript
if(1) // true ---> if(Boolean(1))
if(undefined) // fasle
if({}) // true
if([]) // true ---> if(Boolean([]))
```

`if()`里，其实就是执行 Boolan()方法。

### ==

```javascript
1 == true; // true
(2 == true[1]) == // false ---> Number(2) == Number(true) ---> 2 == 1
  true; // true
```

`==`，本质是执行`Number()`方法。

### Boolean()

|   类型    |    值     | Boolean() |
| :-------: | :-------: | :-------: |
| undefined | undefined |   false   |
|   null    |   null    |   false   |
|  string   |    ''     |   false   |
|  string   |    '0'    |   true    |
|  number   |     0     |   false   |
|  number   |     1     |   true    |
|  boolean  |   false   |   false   |
|  boolean  |   true    |   true    |
|  object   |    {}     |   true    |
|  object   |  {num:0}  |   true    |
|  object   |    []     |   true    |
|  object   |    [0]    |   true    |

总结：

1. undefined、null 都为 false。
2. 字符串只有''为 false。
3. 数值类型只有 0 为 false。
4. 引用数据类型都为 true。
5. 布尔类型看本身。

### Number()

|   类型    |    值     | Number() |
| :-------: | :-------: | :------: |
| undefined | undefined |   NaN    |
|   null    |   null    |    0     |
|  string   |    ''     |    0     |
|  string   |    '0'    |    0     |
|  string   |    '1'    |    1     |
|  string   |   '1a'    |   NaN    |
|  number   |     0     |    0     |
|  number   |     1     |    1     |
|  boolean  |   false   |    0     |
|  boolean  |   true    |    1     |
|  object   |    {}     |   NaN    |
|  object   |  {num:0}  |   NaN    |
|  object   |    []     |    0     |
|  object   |    [0]    |    0     |
|  object   |   [0,1]   |   NaN    |

总结：

1. undefined 为 NaN。
2. null 为 0。
3. 字符类型长度为 0 必为 0。长度不为 0 看 value 是否包含非数字，不包含就是去掉引号后的值。否则 NaN。
4. 数值类型保持原值。
5. 布尔类型 true 为 1，false 为 0。
6. 引用数据类型。
   - 对象{}为 NaN。
   - 数组长度为 0 即为 0。长度为 1 则转那一个数值，长度大于 1 则 NaN。

## Math

1. `max`、`min`

   可以使用 spread、rest 或 apply、call 来改变传入参数是列表还是数组。

2. `ceil`、`floor`

   ceil：天花板，floor：地板。

3. `round`

   四舍五入。

4. `random`

   取[0, num]的整数：`Math.floor(Math.random() * (num + 1))`。

   取[num1, num2]的整数：本质还是[0 ,num]

   ```javascript
   function getRandom(num1, num2) {
     return (
       Math.min(num1, num2) + Math.floor(Math.random() * (num2 - num1 + 1))
     );
   }
   ```

## Date

### 获取时间戳

1. `+date`
2. `Number(date)`
3. `date.valueOf()`
4. `date.getTime`

### 时间戳转 ISO 时间

```javascript
new Date(timeStamp);
```

### 格式化

好库：moment.js

1. 年：`getFullYear`
2. 月：`getMonth`，从 0 开始。
3. 日：`getDate`
4. 小时：`getHours`
5. 分钟：`getMinutes`
6. 秒：`getSeconds`

```javascript
const d = new Date("1999-11-10 03:03:12");

function formatDate(date, format) {
  const config = {
    YY: date.getFullYear(),
    MM: date.getMonth(),
    DD: date.getDate(),
    HH: date.getHours(),
    mm: date.getMinutes(),
    SS: date.getSeconds(),
  };

  for (let item in config) {
    format = format.replace(item, config[item]);
  }

  return format;
}

console.log(formatDate(d, "YY年MM月"));
```

# Vue

## 创建全局组件

```javascript
Vue.component(name, {
  template: '', // html代码
  props: ,
})
```

需要在创建实例之前使用该方法。

## 计算属性

计算属性是基于响应式依赖进行缓存的，相比方法，**只有在相关响应式依赖发生变化时才重新求值**。

## key

> Vue 会尽可能高效地渲染元素，通常会复用已有元素而不是从头开始渲染。

Vue 为你提供了一种方式来表达“这两个元素是完全独立的，不要复用它们”。只需添加一个具有唯一值的 `key` attribute 即可。

## v-for

1. 永远不要把`v-if` 和 `v-for `同时用在同一个元素上。

   `v-for`的优先级比`v-if`的优先级更高，因此每次重渲染时，都会遍历整个列表，不论活跃用户是否发生了变化，因此浪费性能。

   解决方法：

   - 先使用**计算属性筛选**一次。
   - 将`v-if`转移到容器元素上。

2. `key`不能使用`index`

   > 不要使用对象或数组之类的非基本类型值作为 `v-for` 的 `key`。请用字符串或数值类型的值。

   `index`代表当前项的索引（0,1,2,3,4,...），当发生删除、增加等操作时，其后面的元素的`index`都会发生变化，此时`diff`算法就认为后面的`key-index`映射全部发生了变化，将全部重新渲染，严重影响性能。因此推荐`key`使用**唯一值**，如时间戳`+new Date()`、身份证号、学号等...

## 同步组件和异步组件

### 同步组件

```js
import componentA from "./componentA.vue";
```

### 异步组件

只在组件**需要渲染的时候才进行加载渲染**并进行缓存，以备下次访问。

```js
componentA: () => import("./componentA.vue");
```

调用异步组件的方法-延时

```js
setTimeout(() => {
  this.$nextTick(() => {
    console.log(this.$refs.com);
  });
}, 100);
```

优点：提升首页渲染速度。

### 区别

1. nextTick

   父组件获取子组件时：

   同步组件：nextTick 可以获取组件。

   异步组件：**第一次 nextTick 之后无法获取组件**。

2. 打包

   打包成单独的 js 文件存储在 static/js 文件夹里面

3. 生命周期顺序

   **异步组件**：父组件`beforeCreate`、`created`、`beforeMount`、`mounted` --->挨个子组件`beforeCreate`、`created`、`beforeMount`、`mounted`

   **同步组件**：父组件`beforeCreate`、`created`、`beforeMount` --->挨个子组件`beforeCreate`、`created`、`beforeMount`--->挨个子组件`mounted`---> 父组件`mounted`

## 组件

1. 当一个组件被定义，`data`必须声明为返回一个初始数据对象的函数，因为组件可能被用于创建多个实例。

   否则，会出现多个组件使用一个数据对象的情况。

2. 使用事件抛出值

   - 子组件

     ```js
     <button @click="$emit('put', 999)"><button />
     ```

   - 父组件

     `num`接收来自子组件传来的值`num`

     ```js
     <Father @put="num = $event"/>
     ```

     或者

     ```js
     <Father @put="change"/>

     methods:{
       change(num)
       {
         // change方法的第一个形参即子组件传来的值
       }
     }
     ```

3. 组件使用`v-model`

   - 父组件

     ```vue
     // 法一：有缺陷，初始值传不过去
     <Father v-model="message" />
     // 法二：刨根问底
     <Father v-bind:message="message" v-on:input="message = $event">
     // or
     <Father :message="message" @input="message = $event">
     ```

   - 子组件

     ```javascript
     Vue.component("Son", {
       props: ["message"],
       template: `
         <input
           v-bind:value="message"
           v-on:input="$emit('input', $event.target.value)"
         >
       `,
     });
     ```

   小知识：`update:myPropName`模式可以用`.sync`修饰符使父子组件通信的`prop`进行双向绑定。

   - 子组件

     数据发生变化时

     ```js
     this.$emit("update:title", newtitle);
     ```

   - 父组件

     - 刨根问底

       ```js
       <text-document
         v-bind:title="doc.title"
         v-on:update:title="doc.title = $event"
       ></text-document>
       ```

     - `.sync`修饰符

       ```js
       <text-document v-bind:title.sync="doc.title"></text-document>
       ```

## prop

1. 子组件接收`prop`并作为本地数据使用。

   ```js
   props: ['initCount'],
   data() {
     return {
       count: this.initCount
     }
   }
   ```

2. 带有默认值的对象

   对象或数组默认值**必须从一个工厂函数获取**

   ```js
   props: {
     propA: {
       type: Object,
       // 对象或数组默认值必须从一个工厂函数获取
       default: function () {
      		return { message: 'hello' }
       }
     }
   }
   ```

## 插槽

### 使用

1. a.vue

   ```vue
   <template>
     <div>
       <header>
         <slot name="header"></slot>
       </header>
       <main>
         <slot></slot>
       </main>
       <footer>
         <slot name="footer"></slot>
       </footer>
     </div>
   </template>
   ```

2. App.vue

   ```vue
   <A>
     <template v-slot:header>
       <h1>header</h1>
     </template>
     <template v-slot:footer>
       <h1>footer</h1>
     </template>
     <h1>main</h1>
   </A>
   ```

### 插槽内容使用子组件数据

子组件

```vue
<slot v-bind:propName="propName"></slot>
// propName是子组件内部数据
```

插槽内容

```vue
<template v-slot="allProp">
  // allProp可以使用所有插槽的所有属性，通过打点区分。
</template>
```

### 注意事项

1. 不带`name`的`<slot>`默认名称为“default”。
2. `v-slot`只能添加在`<template>`上。

## 访问组件或元素

### 访问根实例

```js
this.$root;
```

### 访问父组件

```js
this.$parent;
```

### 访问子组件

1. `this.$children`
2. `ref`

### 依赖注入

父组件使用实例新选项`provide`

```js
provide() {
  return {
    getMap: this.getMap
  }
}
```

子组件使用实例新选项`inject`

```js
inject: ["getMap"];
```

## vue 生命周期

1. `new Vue()`

   初始化事件和生命周期。

2. `breforeCreate()`

   创建实例前，可以访问实例选项`$options`，但无法访问`$el`、`data`等。

3. `created()`

   创建实例后，可以访问`data`，无法访问`$el`。

   `created` ~`breforeMount`

   - 查询是否有`$el`选项，没有则**暂停生命周期**，需手动挂载`$mount`，反之进行下一步。

   - 查询是否有模板`template`，没有则使用外部`HTML`作为模板编译，反之则`render`函数编译模板。

     优先级：`render`>`template`>`outHTML`

4. `beforeMount()`

   挂载前，无法访问`$el`。

5. `mounted()`

   挂载后，可以访问`$el`。

6. `breforeUpdate()`

   视图层`view`数据并未更新，`$el`、`data`均已更新，但真实`DOM`还未更新。

7. `updated()`

   视图层`view`数据更新，真实`DOM`更新。

8. `beforeDestory()`

   仍可使用`this`、

9. `destoryed()`

   Vue 实例销毁，解绑事件监听、子实例、`watcher`。

## vue 自定义指令

### 全局注册

```js
Vue.directive("focus", {
  inserted: function (el) {
    el.focus();
  },
});
```

### 局部注册

实例新选项`directives`

```js
directives: {
  focus: {
    inserted: function (el) {
      el.focus()
    }
  }
}
```

使用：v-focus

## forceUpdate

迫使 Vue 实例重新渲染。注意它仅仅影响实例本身和插入插槽内容的子组件，而不是所有子组件。

## 过滤器

常在**双花括号插值**、**`v-bind`表达式**中用于文本格式化，需被添加在`JavaScript`表达式尾部。

如：

```vue
<!-- 在双花括号中 -->
{{ message | capitalize }}

<!-- 在 `v-bind` 中 -->
<div v-bind:id="rawId | formatId"></div>
```

### 创建过滤器

1. 局部

   ```js
   filters: {
     function a() {}
   }
   ```

2. 全局

   ```js
   Vue.filter(funcName, function (value) {});
   ```

### 使用

`{{ message | filterA | filterB }}`中`message`用于函数`filterA`的第一个参数，`filterA`的结果被传入`filterB`。

## 混入

1. 创建混入对象

   ```javascript
   const myMixin = {
     created() {
       this.show();
     },
     methods: {
       show() {
         console.log("混入！");
       },
     },
   };
   ```

2. 使用混入对象

   ```javascript
   const vm = new Vue({
     mixins: [myMixin],
   });
   // 全局引入：
   Vue.mixin(myMixin);
   ```

注意事项：

- 选项合并发生冲突时，以组件数据为准。未冲突，将合并。
- 钩子函数合并，混入先被调用，其次调用组件的。

## 插件

### 开发插件

插件暴露`install(Vue, options)`方法，在此方法里 do something。

```javascript
const obj = {
  install(_Vue) {
    // 如果较多时，可以forEach批量注册components
    _Vue.component("my", {
      render(createElement) {
        return createElement("h" + this.level, this.$slots.default);
      },
      props: {
        level: {
          type: Number,
          require: true,
        },
      },
    });
  },
};
```

### 使用插件

```javascript
Vue.use(obj);
```

## 过渡

### 单元素/组件过渡

Vue 提供了封装组件`transition`

```vue
<transition name=""></transition>
```

常用于：

1. `v-if`
2. `v-show`
3. 动态组件
4. 组件根节点

#### 过渡类名

默认无名`transition`：`v-enter`、`v-enter-active`、`v-enter-to`、`v-leave`、`v-leave-active`、`v-leave-to`。

有`name`：`name`取代`v`。

#### 自定义类名

- `enter-class`
- `enter-active-class`
- `enter-to-class` (2.1.8+)
- `leave-class`
- `leave-active-class`
- `leave-to-class` (2.1.8+)

#### 注意事项

1. CSS 过渡在动画中 `v-enter` 类名在节点插入 DOM 后不会立即删除，而是在 `animationend` 事件触发时删除。

### 多元素过渡

可使用`v-if/v-else`。

#### 过渡模式

`transition`新`props`：`mode`

mode 常用值：

1. `out-in`：当前元素过渡完，新元素再来。
2. `in-out`：新元素先过渡，当前元素再走。

#### 注意事项

1. 同名标签切换，记得设置`key`。

### 多组件过渡

可使用动态组件。

### 列表过渡

`<transition-group>`

常用`props`：

1. `tag`：默认`span`标签。

   `<transition-group>`为真实元素，默认`span`。

### 可复用过渡组件

使`<transition>`或`<transition-group>`成为根组件。

### 动态过渡

所有属性都是动态可绑定的。

## 模板 template 编译原理

流程

1. 定位模板

   寻找根节点`$el`

   - 不存在，手动`mount`，下一步。
   - 存在，下一步。

   寻找模板`template`，render 函数编译。

   - 不存在，使用外部 HTML。
   - 存在，使用。

2. **解析器**：生成 AST 语法树

   一段一段生成，开始标签、文本、注释、结束标签。确定层级关系使用栈，开始标签推入栈，结束标签弹出栈。

3. **优化器**：标记静态节点

   方便重新渲染，不需再为静态节点创建新虚拟树。

   标记：

   - 所有静态子树。
   - 静态根树。

4. **代码生成器**：生成 render 函数

   首先，得到渲染函数字符串。

   ```javascript
   with (this) {
     return _c("div", { attrs: { id: "app" } }, [
       _c("div", { staticClass: "class-name", attrs: { title: `title` } }, [
         _v(" " + _s(name) + " "),
       ]),
       _v(" "),
       _c("div", [_v("tetttt")]),
     ]);
   }
   ```

   再通过 new Function 得到渲染函数，以便得到该模板的虚拟 DOM。

   注意：

   - `_c`：createElement，处理元素节点为虚拟 DOM 节点。
   - `_v`：createTextVNode，处理文本节点为虚拟 DOM 节点。
   - `_e`：createEmptyVnode，处理注释节点为虚拟 DOM 节点。

# 杂

## Git

工作区：写好的代码，以文件形式保存的。

暂存区：`add`之后的。

本地仓库：`commit`之后的。

### Git 版本回退

1. 查看提交记录

   复制指定`commit`的版本`id`。

   ```
   git log
   ```

2. 版本回退

   ```
   git reset
   ```

   - `git reset --soft`

     暂存区、工作区保持不变，本地仓库回滚到**指定版本`commit`完成后的那一刻**。

   - `git reset --mixed`

     工作区保持不变，本地仓和暂存区 回滚到指定版本。

   - `git reset --hard`

     本地仓、暂存区、工作区都回滚到指定版本。

## Lodash 常用

1. `shuffle`
2. 拷贝
   - 浅拷贝`clone`
   - 深拷贝`cloneDeep`
3. 防抖`debounce`

## 跨域

### options 预检请求

区分简单请求和复杂请求：

1. 使用方法`put/delete/patch/post`;
2. 发送`json`格式的数据`（content-type: application/json）`
3. 请求中带有自定义头部；

在跨域复杂请求的时候才会`preflight request`，因为怕对服务器数据造成影响。

请求报文：

1. `Access-Control-Request-Method`：告知服务器实际请求所使用的 HTTP 方法；
2. `Access-Control-Request-Headers`：告知服务器实际请求所携带的自定义首部字段。

响应报文：

1. `Access-Control-Request-Method`：告知客户端被允许使用的 HTTP 方法；
2. `Access-Control-Request-Headers`：告知客户端被允许携带的自定义首部字段。
3. `Access-Control-Max-Age`：浏览器指定时间内无需再次发送预检请求。

## MV\*模式

### MVC

#### Smalltalk-80 MVC 解释

- View：管理用户界面的层次。
- Model：提供操作数据的接口，执行业务逻辑。
- Controller：View 和 Model 之间的协作。

用户对 View 操作后，View 将处理的权限移交给 Controller。Controller 进行应用逻辑（调 Model 哪个接口、对 View 数据预处理...），具体的业务逻辑交给 Model。当**Model 变更时通过观察者模式告诉 View**，View 收到消息后向 Model 请求最新的数据，更新界面。

#### 优缺点

- 优点：逻辑分离（应用逻辑、业务逻辑、展示逻辑）、多视图更新（观察者模式）。
- 缺点：单元测试困难，View 无法组件化（高度依赖 Model）。

#### 前端思考：

1. 前端由于 HTTP 是单工协议，服务端无法向前端发送消息（websocket 除外），只能前端发送请求到后端。
2. MVC 模式有点类似于 Vuex。在 Vuex 中，更改 state 状态的唯一方法就是提交 mutation，即业务逻辑。进行应用逻辑的类似于 action，action 中提交的是 mutation。展示逻辑就类似于 Getters，负责将数据归纳整理，提供给页面想要的数据。

### MVP

#### 解释

大致与 MVC 相同，唯一不同的是 Controller 变成了 Presenter。

用户对 View 操作后，View 将处理的权限移交给 Presenter。Presenter 进行应用逻辑（调 Model 哪个接口、对 View 数据预处理...），具体的业务逻辑交给 Model。

不同的是，当 Model 变更时通过观察者模式告诉 Presenter，**Presenter 通过 View 提供的接口告诉 View**。

#### 优缺点

- 优点：便于对 Presenter 单元测试、View 可组件化。
- 缺点：Presenter 变得臃肿，不仅有应用逻辑，还有同步逻辑。

### MVVM

#### 解释

大致与 MVP 相同，唯一不同的是将 Presenter 中的同步逻辑分给了“Binder”。Binder 主要是负责 View 和 Model 双向绑定。

用户对 View 操作后，View 将处理的权限移交给 View-Model。View-Model 进行应用逻辑（调 Model 哪个接口、对 View 数据预处理...），具体的业务逻辑交给 Model。

不同的是，当 Model 变更时，**通过 Binder 自动把数据更新到 View 上**。当用户**对 View 操作时，也会自动更新到 Model**。

#### 优缺点

- 优点：解决了大量同步问题，提高代码可维护性。便于测试，由于 Binder 的存在，Model 正确即 View 正确，减少 View 同步更新测试。
- 缺点：建立大量 View-Model、数据绑定以指令写在 View 中不便测试。

## 动态规划

思路：

1. 确定状态

   - `f[i][j]`。
   - 最后一步：最优策略的最后一步。
   - 子问题：通过最后一步，把问题规模缩小。

2. 转移方程

3. 初始条件和边界状态

   边界状态即数组不可越界。初始状态一般类似于 f(0) = 0。

4. 计算顺序

   一维从小到大，二维从左到右、从上至下。​

## 前端跨域

### 什么是跨域？

当端口号、域名、协议三者有一不同，即为跨域。

### 实战解决方案

1. 服务端设置 cors 白名单

2. Nginx 代理

   前端想要访问的内容，本质是存在跨域问题的。但在客户端和服务端之间增加了一层，这一层与服务端同源，同时又允许客户端访问。客户端访问的就是中间这一层，而中间这一层再请求真实的服务端，返回数据。

### 前端代理

`api`接口，存在多个地址的问题，它们可能来自多个 ip。而跑前端项目时，请求的 baseIP 固定，而真实的接口 IP 又不一致，所以请求不到接口。

解决方案

1. 前端本地做代理，请求接口时，更改 baseIP。
2. Nginx 代理。

## 太杂

`console.table()`
