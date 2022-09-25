# General Code Style Tips

Some tips about coding.

English | [简体中文](./README-zh-CN.md)

# 0. Why

Nowadays, the complexity of software project is beyond the will of one or two senior technical staffs. Software development certainly need communication and collaboration, the most direct collaboration between developers could be found in code writing and reading. For guaranteeing of product quality and better collaboration, good code quality and code style are surely necessary.

Hope tips here could bring you some help.

# 1. The most basic communication tool - English

IT as a high-tech industry, from hardware to software, from learning new programming language and framework, to using correct term to search and participate in related topics, all the primary information is in english, not to mention our code need writing in english.

Not talking too much about the topic of learning english here. What we should pay attention to is that, we usually start learning mother language with phonics, similarly **english phonetic symbol** and oral language are very important classes which has been ignored for a long time. **Speaking bring better communication**. After getting familiar with **english phonetic symbol**, it's easy to pronounce when we see a word, and memorizing words with their pronunciation is very efficient.

In daily, we could use dictionary anytime on the basis of **phonetic symbol** and **part of speech**, learn **grammar** if there is time.

Welcome to share experience about learning phonetic symbol and oral language:
- [EnglishAnyone](https://www.youtube.com/user/EnglishAnyone)

# 2. The most basic way of thinking - Assembly

reference:
- [Compiler Explorer](https://godbolt.org/): an online compiler showing assembly

We may have heard the choice between time for space and space for time, seen errors like **stack overflow**, known passing any number of parameters in function call is valid in some programming languages. If we want to know what happens at the bottom of computer, why not get some assembly.

Assembly language, as the top layer of hardware and the bottom layer of software, we learn it not for directly writing software, but for understanding how hardware (usually mean cpu) work, then use assembly thinking to understand high level programming languages and some design concepts, finally code with these thoughts.

In my opinion, developers saying they are master of some programming languages and frameworks but without understanding assembly, is just like chemistry students saying they know many fancy chemical equations but without knowing what's periodic table of elements.

# 3. Naming

There's already a lot of discussion about importance of naming.

## 3.1 Terminology

**Be professional**.

We know that naming with english word mixing phonetic alphabet or character of other language is not a good choice. From domain name to project name, from file name to variable name, try to use terms correctly.

Early in the project, the choice of some terms and abbreviations should be agreed upon and strictly followed within project team. Eg: change something, should use 'alter', 'change', 'edit' or 'modify'. Represent time, should use 'date', 'datetime' or 'time'. Should use 'modification_time' or 'm_time' as abbreviation for practice.

Another example, if we need to develop a project about supply chain, then related terms may include:
provider, reseller, retailer, consumer, customer, cost, tax, profit, order, bill

**We could be more professional**.

## 3.2 Case Issue

How to use common `camelCase`, `PascalCase`, `kebab-case` and `snake_case`, should be agreed upon early in the project, use lint tool to unify as soon as it is available.

# 4. Comment And Blank

## 4.1 Naming As Comment

Sometimes accurate variable and function names itself are comments, adding comments seems redundant at this moment.

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

## 4.2 Blank Space

Add blank space between english character boundary and position nearby special character. There is a [pangu.js](https://github.com/vinta/pangu.js) talking about this. But plugin is not suggested, better to rely on good habits.

```javascript
/* avoid */
console.log('hello');//这里的comment仅作演示=>just for readable

/* prefer */
console.log('hello'); // 这里的 comment 仅作演示 => just for readable
```

Try to wrap long statements and function chaining.

```javascript
/* avoid */
res = new HttpResponse(new FileStream(filePath),new HttpResponseHeader(someRawObjList.aaa().bbb().ccc()));

/* prefer */
res = new HttpResponse(
  new FileStream(filePath),
  new HttpResponseHeader(
    someRawObjList
    .aaa()
    .bbb()
    .ccc()
  )
);
```

## 4.3 Blank Line

As separator, blank space is used to enhance readability of statements, and blank line is used to enhance readability of logics.

We could add blank line between different modules, functions, and even several variable declarations. Compare to blank space, the use of blank line is more flexible, the philosophy is that subjectively divide the code into recognizable areas or areas with logically separation of concerns.

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

# 5. Control Flow

## 5.1 Guard Clause

keyword: **guard clause, cyclomatic complexity**

reference:
- [https://redirect.sonarsource.com/doc/cognitive-complexity.html](https://redirect.sonarsource.com/doc/cognitive-complexity.html)

With many logic branches, nested `if & else` is easy to appear in our code.

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

  // Make a new function for complex logic, reduce the complexity of a single function and it's convenient for using guard clause
  // If capturing local variables is needed, use closure
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

Use guard clause could:
- Reduce cyclomatic complexity
- Reduce indentation, enhance readability
- Separate `main logic` in main branch from guards, also merge judgment condition and exception handling. Normal reading habit is from top to bottom, just think there are 20 lines code in main branch when using nested `if & else`, after reading 20 lines code, suddenly there are several `else` branches, at this point could reader recall judgment conditions matching these `else` branches
- Convenient to maintain guard branch. Adding, deleting or modifying a guard branch does not affect code in main branch or other branches, this is best experienced when we look at `git diff` or other diff viewers

Below is a description with comments

```javascript
/* avoid */
function test() {
  if (XXX) { // See condition XXX, if we want to know the logic when XXX is not satisfied, we have to scroll the mouse wheel all over the screen and pay attention to indentation to find the matching else branch
    if (YYY) {
      // Usually when all the conditions are met, our business logic will run to here
      // Main logic of some old code may have dozens or hundreds of lines, even if it is new code, after expansion, suppose there are 20 lines of code here

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
      // After reading and understanding 20 lines code, look at here, could reader recall judgment condition matching this else branch
      handleYYYNotSatisfied();
    }
  }
  else {
    // Several else branches appear, which matching which
    handleXXXNotSatisfied();
  }
}

/* prefer */
function test() {
  if (!XXX) {
    // Not satisfied then handle immediately and return
    // Code of condition and exception handler are placed together, become a standalone code block, one 'if' for one guard
    handleXXXNotSatisfied();
    return;
  }

  // Adding, deleting or modifying a guard here does not affect other code. Of course usually we need to consider the order of guards

  if (!YYY) {
    handleYYYNotSatisfied();
    return;
  }

  // The independent main branch here is clearer
  // main logic
}
```

In addition, the philosophy of guard clause is about early `return`, maybe multiple judgment conditions do not bother to order of priority, maybe each guard branch should come with normal business code and the guarded `main logic` should throw an error.

```javascript
function handleMsg(msgJson) {
  const msgObj = parseMsg(msgJson);

  // Similar to status, enumeration or judgment which has a certain pattern here, use 'if' statement when less than 3 times, otherwise consider using pattern matching
  if (msgObj.isApiCall) {
    handleMsgAsApiCall(msgObj);
    return;
  }

  if (msgObj.isApiCallback) {
    handleMsgAsApiCallback(msgObj);
    return;
  }

  // Sometimes, all normal situations that we could handle have been considered, and code goes here should just throw an error
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

Finally, in my opinion, a simple way to judge code quality is to find how many `else` keywords appear in code. Once I wrote a project alone with about 10,000 lines of code, and the `else` keyword only appeared 6 times in the whole project, no `else if` keyword, no nested `if & else`. Maybe the quality of this project is not that good, maybe it's just me being a little obsessive, who knows.

## 5.2 Pattern Matching

Pattern matching is common in many functional programming languages, which could greatly simplify the writing of judgment logic and branches, below is the support progress of some languages:
- js: https://github.com/tc39/proposal-pattern-matching
- java: https://docs.oracle.com/en/java/javase/17/language/pattern-matching.html
- c#: https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/functional/pattern-matching
- python: https://www.python.org/dev/peps/pep-0636
- rust: https://doc.rust-lang.org/book/ch06-02-match.html

If upgrade to a higher version of the programming language is not so easy or within which pattern matching is not yet supported like js, try to use key-value mapping to reduce the redundancy of `if & else` or `switch` code to a certain extent. Key-value mapping could be directly used through `object` in js, and it may be a structure such as `Map` or `Dictonary` in other languages.

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

Compare to pattern matching, using mapping to control code flow has obvious disadvantages, the key of mapping is a fixed value instead of an expression like `predicate`. Although the value could be a function, there are local variable capturing issues, signatures of functions must be similar in order to pass parameters, (strongly typed languages require generics for constraint) and other issues. So for flexibility of control flow and code simplicity, various programming languages are trying or already support pattern matching.

## 5.3 Add a little functional programming to iteration

Most of the low complex iterations could be written functionally to enhance readability. The functional support of each language is generally in types like `Array` or `Enumerable`, and commonly used operations are:
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
  .reduce((ids, row) => {
    if (row.selected) {
      ids.push(row.id);
    }
    return ids;
  }, []);
```

## 5.4 DRY

keyword: **DRY principle, code duplication**

The DRY principle in our daily is to minimize code duplication. The usual practice is that certain lines of code or certain code blocks are very similar in appearance, then extract the differences as the minimum dependencies, construct the minimum dependencies reasonably, extract the same points and use the minimum dependencies reasonably, reasonable means reasonable syntax and naming. Also, think of encapsulation, inheritance and polymorphism, think of utility classes, base classes and generics.

```java
/* avoid */
if (sunPresented) {
  System.out.println("everything is fine and it's daytime");
}
else {
  // Many code snippets and even some tutorials on the Internet have similar look like this
  // Whether or not to call the println method to print a string has nothing to do with the judgment condition at all
  // Why is the code in the two paths so similar, mostly because copy oriented programming
  // Maybe a little redundancy is acceptable, who knows
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
