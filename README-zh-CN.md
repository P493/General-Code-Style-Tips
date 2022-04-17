# 通用代码风格小提示 (General Code Style Tips)

一些编码小提示。

# 0. 为什么

时至今日，软件项目的复杂度已非一两个资深技术人员的意志所能左右。软件开发必然是要依靠沟通和协作，开发人员最直接的协作就体现在代码编写和阅读上。为了产品质量的保证与更好的协作，良好的代码质量 (code quality) 与代码风格 (code style) 必不可少。

希望这里的小提示能给你带来帮助。

# 1. 最基础的沟通工具 - 英语

IT 作为高新技术产业，从硬件到软件，从学习新的语言框架到使用正确的术语搜索、参与相关话题，所有的一手信息都是英语，更别说我们的代码本身就需要用英语来书写。

学习英语这个话题此处不做过多展开。值得注意的是，学习母语我们通常会从拼音开始，同样的**英语音标**和口语，也是相当重要却长期被我们忽视的一课。**说得出来才好沟通**。掌握**英语音标**以后，见词即知发音，结合发音来记忆单词是非常高效的。

日常应用，我们可以在熟悉**音标**和**词性**的基础上结合字典，随用随查，有多余时间再看看**语法**。

在这里，也欢迎大家分享音标和口语相关学习经验:
- [EnglishAnyone](https://www.youtube.com/user/EnglishAnyone)

# 2. 最基础的思考方式 - 汇编

reference:
- [Compiler Explorer](https://godbolt.org/): 一个可以看汇编的在线编译器

我们可能听说过时间换空间、空间换时间的抉择，也见过 **stack overflow** 之类的报错，还知道一些编程语言在函数调用时可以传递任意个数的参数。如果我们想要知道计算机底层发生了什么，为什么不来点汇编语言。

汇编语言，作为硬件的最顶层和软件的最底层，我们学习它不是为了直接用它来写软件，而是为了理解硬件 (通常就是说 cpu) 是如何工作的，然后用汇编的思考方式去理解高级编程语言和一些设计理念，最后带着这些理解去编写代码。

在我看来，开发者说自己精通某些语言和框架却不理解汇编，就像化学学生说自己能写很多时髦的化学方程式却不懂什么是元素周期表一样。

# 3. 命名

命名的重要性已经有很多相关讨论了。

## 3.1 术语

**保持专业**。

我们都知道用英语单词混合其他语言的拼音或字符来命名不是一个好的选择。从域名到项目名，从文件名到变量名，尝试正确地使用术语。

在项目前期，项目组内就应该在一些术语的选择和缩写上达成共识并严格遵守。如改变什么东西，用 alter、change、edit 还是 modify，表示时间用 date、datetime 还是 time，用 modification_time 还是允许使用缩写惯例的 m_time。

再比如，我们需要做一个供应链相关项目，那么相关的术语就可能包括:
provider, reseller, retailer, consumer, customer, cost, tax, profit, order, bill

**我们可以更专业的**。

## 3.2 case 问题

常见的几种 `camelCase`、`PascalCase`、`kebab-case`、`snake_case` 如何应用，在项目前期尽量达成共识，能用 lint 工具统一的就尽早配置。

# 4. 注释与空白

## 4.1 命名即注释

有时准确的变量、函数名本身就是注释，此时添加注释反而显得冗余。

```javascript
/* avoid */
function getNames(path, ext, findAll) {
  let arr = ...
}

/* prefer */
function getFileNamesByExtension(dirPath, extension, recursive) {
  let fileNames = ...
}
```

## 4.2 空格

在英文字符边界和注释符号等特殊字符附近添加空格。有个 [pangu.js](https://github.com/vinta/pangu.js) 专门讨论这件事，不过不建议使用插件，尽量依靠良好习惯。

```javascript
/* avoid */
console.log('hello');//这里的comment仅作演示=>just for readable

/* prefer */
console.log('hello'); // 这里的 comment 仅作演示 => just for readable
```

语句太长或链式调用时尝试换行。

```javascript
/* avoid */
res = new HttpResponse(new FileStream(filePath),new HttpResponseHeader(someRawObjList.map().filter().reduce()));

/* prefer */
res = new HttpResponse(
  new FileStream(filePath),
  new HttpResponseHeader(
    someRawObjList
    .map()
    .filter()
    .reduce()
  )
);
```

## 4.3 空行

空格作为分隔符是提高语句的可读性，空行则是提高逻辑的可读性。

不同模块、函数甚至几个变量声明之间都可以添加空行。相比空格，空行的使用更灵活，其哲学在于主观地把代码分割为各个有一定辨识度的或逻辑上需关注点分离的区域。

```javascript
/* avoid */
function test() {
  const a = ...
  const b = ...
  for (...) {
    ...
  }
  fnA();
  fnB();
}

/* prefer */
function test() {
  const a = ...
  const b = ...

  for (...) {
    ...
  }

  fnA();
  fnB();
}
```

# 5. 控制流程

## 5.1 卫语句

keyword: **guard clause, cyclomatic complexity**

reference:
- [https://redirect.sonarsource.com/doc/cognitive-complexity.html](https://redirect.sonarsource.com/doc/cognitive-complexity.html)

逻辑分支一多，我们的代码中就容易出现嵌套 `if & else`

```javascript
/* avoid */
function test() {
  // ...

  if (XXX) {
    if (YYY) {
      // main logic
    }
    else {
      handleYYYNotSatisfied();
    }
  }
  else {
    handleXXXNotSatisfied();
  }

  // ...
}

/* prefer */
function test() {
  // ...

  // 复杂的逻辑就提出来另建一个 function，降低单个函数的复杂度也方便使用卫语句
  // 如果需要捕获本地变量，使用闭包
  someMeaningfulName();

  // ...
}

function someMeaningfulName() {
  if (!XXX) {
    handleXXXNotSatisfied();
    return;
  }

  if (!YYY) {
    handleYYYNotSatisfied();
    return;
  }

  // main logic
}
```

使用卫语句可以:
- 降低 cyclomatic complexity
- 减少缩进，增强可读性
- 使主干里的 `main logic` 和守卫分离，同时判断条件和异常处理合并在一起。正常阅读习惯是从上到下，试想用嵌套 `if & else` 时主干有 20 行代码，在看完这 20 行代码后眼前突然又出现几个 `else` 分支，此时阅读者一定能记起这几个 `else` 分支对应的判断条件吗
- 对于守卫分支的维护也更方便。新增、删除或修改一个守卫分支，不会影响主干和其他分支的代码，当我们看 `git diff` 或其他 diff 查看器时最能体会到

下面结合注释说明

```javascript
/* avoid */
function test() {
  if (XXX) { // 看到条件 XXX，但是要想知道不满足 XXX 条件时的逻辑，就要滑动鼠标滚轮满屏幕找，还得注意缩进对齐才能找到正确的 else 分支
    if (YYY) {
      // 主逻辑，通常也就是所有条件满足时，正常情况下我们的业务逻辑会走到这里
      // 一些老代码的主逻辑可能有几十上百行，就算是新代码，在业务膨胀后，假设这里有 20 行代码

      // 1
      // 2
      // 3
      // 4
      // 5
      // 6
      // 7
      // 8
      // 9
      // 10
      // 11
      // 12
      // 13
      // 14
      // 15
      // 16
      // 17
      // 18
      // 19
      // main logic
    }
    else {
      // 在阅读并尝试理解 20 行代码的主逻辑后，再看到这里，是否还能想起这里的 else 分支对应哪个判断条件
      handleYYYNotSatisfied();
    }
  }
  else {
    // 多个 else 分支同时出现在眼前，谁对应谁
    handleXXXNotSatisfied();
  }
}

/* prefer */
function test() {
  if (!XXX) {
    // 不满足则立即处理并 return
    // 条件和异常处理代码都放在了一起，成为一个独立的代码块，一个 if 就是一个守卫
    handleXXXNotSatisfied();
    return;
  }

  // 想要在这里增删改一个守卫，不会影响到其他代码。当然通常我们需要考虑守卫的顺序

  if (!YYY) {
    handleYYYNotSatisfied();
    return;
  }

  // 独立的主干放这里更清晰明了
  // main logic
}
```

另外，卫语句的哲学在于提前 `return`，也有可能多个判断条件不怎么区分先后顺序，也有可能每个分支里才是业务上的正常代码而被守卫的 `main logic` 在业务上应该报错。

```javascript
function handleMsg(msgJson) {
  const msgObj = parseMsg(msgJson);

  // 类似状态、枚举或存在一定模式的判断，少于 3 个时可使用 if 处理，否则考虑使用模式匹配
  if (msgObj.isApiCall) {
    handleMsgAsApiCall(msgObj);
    return;
  }

  if (msgObj.isApiCallback) {
    handleMsgAsApiCallback(msgObj);
    return;
  }

  // 有些时候，正常的我们能处理的情况考虑完了，代码走到这里只有抛错最合适
  throw new Error("unknown message type");
}

function parseMsg(msgJson) {
  if (!XXX) {
    throw ...
  }

  if (!YYY) {
    throw ...
  }

  if (!ZZZ) {
    throw ...
  }

  // ...

  return msgObj;
}
```

最后，在我看来，代码质量的一种简单评判方式是看代码中出现了多少 `else` 关键字。曾经我独自写的一个项目有大约一万行代码，整个项目内 `else` 关键字只出现了 6 次，没有 `else if` 关键字，没有嵌套 `if & else`。也许这个项目质量没有那么好，也许只是我有点强迫症，who knows

## 5.2 模式匹配

模式匹配常见于许多函数式编程语言，能够极大的简化判断逻辑和分支的写法，一些语言的支持情况如下:
- js: https://github.com/tc39/proposal-pattern-matching
- java: https://docs.oracle.com/en/java/javase/17/language/pattern-matching.html
- c#: https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/functional/pattern-matching
- python: https://www.python.org/dev/peps/pep-0636
- rust: https://doc.rust-lang.org/book/ch06-02-match.html

在不能随意升级到编程语言高版本或如 js 还没支持模式匹配的情况下，可以尝试使用键值映射的方式在一定程度上减少 `if & else` 或 `switch` 类代码的冗余。键值映射在 js 里可直接用 `object`，其他语言里可能是 `Map` 或 `Dictonary` 之类的结构。

```javascript
/* avoid */
let statusText;
switch (statusCode) {
  case 200:
    statusText = 'Ok';
    break;
  case 404:
    statusText = 'Not found';
    break;
  case 500:
    statusText = 'Internal server error';
    break;
  default:
    statusText = 'Unknown error';
}

/* prefer */
const statusTextMap = {
  200: 'Ok',
  404: 'Not found',
  500: 'Internal server error'
};
const statusText = statusTextMap[statusCode] ?? 'Unknown error';



/* avoid */
if (action.type === 'save') {
  // ...
}
else if (action.type === 'load') {
  // ...
}
else if (action.type === 'delete') {
  // ...
}

/* prefer */
const actionHandlerMap = {
  save: action => {
    // ...
  },
  load: action => {
    // ...
  },
  delete: action => {
    // ...
  }
}

const handler = actionHandlerMap[action.type];
handler && handler(action);
```

使用映射来控制代码流程相比模式匹配而言，缺点很明显，映射的键是固定的值而不是类似 `predicate` 的表达式，值虽然可以是函数但存在本地变量捕获、为了传参每个函数的签名必须相似 (强类型语言更是需要泛型来约束) 等问题。所以为了控制流程的灵活性和代码的简洁，各个编程语言都在尝试或已经支持模式匹配。

## 5.3 给迭代加一点函数式编程

大多数不是很复杂的迭代都可以使用函数式的写法提高可读性。各语言的函数式支持一般在类似 `Array` 或 `Enumerable` 的类型中，常用的操作有:
- map
- reduce
- filter, where
- forEach
- includes, any, anyMatch
- find, first, findFirst

```javascript
/* avoid */
const selectedRowIds = [];
for (let i = 0; i < table.rows.length; i++) {
  const row = table.rows[i];
  if (row.selected) {
    selectedRowIds.push(row.id);
  }
}

/* prefer */
const selectedRowIds = table.rows
  .filter(row => row.selected)
  .map(row => row.id);
```

## 5.4 DRY

keyword: **DRY principle, code duplication**

DRY 原则于我们日常而言就是尽量减少代码重复。通常作法是某几行较长的代码或某几个代码块的外观极为相似，那么提取其不同点作为最小依赖，合理构造最小依赖，提取相同点合理使用最小依赖，合理是指语法合理、命名合理。此外，想想封装继承多态，想想工具类、基类、泛型。

```java
/* avoid */
if (sunPresented) {
  System.out.println("everything is fine and it's daytime");
}
else {
  // 网上很多代码片段甚至一些教程都有类似的写法
  // 是否调用 println 方法打印字符串这件事其实和判断条件完全没有关系
  // 为什么两条路径中的代码如此相似，多数是因为 copy oriented programming
  // 也许一点冗余也是可以接受的，who knows
  System.out.println("everything is fine and it's nighttime");
}

/* prefer */
String whatTime = sunPresented ? "daytime" : "nighttime";
System.out.println("everything is fine and it's " + whatTime);
```

```javascript
/* avoid */
if (this.option.pattern == "Base64") {
  this.tbx2.val = await new Base64Encoder({
    encoding: this.option.encoding
  }).handle(this.tbx1.val);
}
else if (this.option.pattern == "Base58") {
  this.tbx2.val = await new Base58Encoder({
    encoding: this.option.encoding
  }).handle(this.tbx1.val);
}
else if (this.option.pattern == "Base32") {
  this.tbx2.val = await new Base32Encoder({
    encoding: this.option.encoding
  }).handle(this.tbx1.val);
}
else if (this.option.pattern == "Base16") {
  this.tbx2.val = await new Base16Encoder({
    encoding: this.option.encoding
  }).handle(this.tbx1.val);
}

/* prefer */
const constructorMap = {
  Base64: Base64Encoder,
  Base58: Base58Encoder,
  Base32: Base32Encoder,
  Base16: Base16Encoder
};

const encoderConstructor = constructorMap[this.option.pattern];
if (!encoderConstructor) {
  return;
}

const encoder = Reflect.construct(
  encoderConstructor,
  [
    {
      encoding: this.option.encoding
    }
  ]
);

this.tbx2.val = await encoder.handle(this.tbx1.val);
```
