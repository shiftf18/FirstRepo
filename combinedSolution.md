

# 1.implement-curry.md

### Problem Description

Currying is a useful technique used in JavaScript applications.

Please implement a curry() function, which accepts a function and return a curried one.


```js
const join = (a, b, c) => {
  return `${a}_${b}_${c}`;
};

const curriedJoin = curry(join);

curriedJoin(1, 2, 3); // '1_2_3'

curriedJoin(1)(2, 3); // '1_2_3'

curriedJoin(1, 2)(3); // '1_2_3'
```


### Solution

```js
/**
 * @param { Function } func
 */
function curry(func) {
  // Return a wrapper function to make it curry-able.
  return function curried(...args) {
    // If passed arguments count is greater than or equal to original function 'func'
    // parameters count, directly call 'func' with passed arguments.
    if (args.length >= func.length) {
      return func.apply(this, args);
    } else {
      // Otherwise return another wrapper function to gather new argument
      // and pass it to `curried` function. This will continue until
      // arguments count >= parameters count.
      return function (...args2) {
        return curried.apply(this, args.concat(args2));
      };
    }
  };
}
```



# 10.first-bad-version.md

### Problem Description

Say you have ~~500~~ multiple versions of a program, write a program that will find and return the first bad revision given a `isBad(version)` function.

Versions after first bad version are supposed to be all bad versions.

**notes**

1. Inputs are all non-negative integers

2. if none found, return -1

#

### Naïve Solution

```js
/*
 type TypIsBad = (version: number) => boolean
 */

/**
 * @param {TypIsBad} isBad
 */
function firstBadVersion(isBad) {
  // firstBadVersion receive a check function isBad
  // and should return a closure which accepts a version number(integer)
  return (version) => {
    // write your code to return the first bad version
    // if none found, return -1
    for (let i = 0; i <= version; i++) {
      if (isBad(i)) {
        return i;
      }
    }
    return -1;
  };
}
```

#

### Solution with Binary Search

```js
/*
 type IsBad = (version: number) => boolean
 */

/**
 * @param {IsBad} isBad
 */
function firstBadVersion(isBad) {
  // firstBadVersion receive a check function isBad
  // and should return a closure which accepts a version number(integer)
  return (version) => {
    // write your code to return the first bad version
    // if none found, return -1
    let left = 0;
    let right = version;

    while (left < right) {
      const mid = left + Math.floor((right - left) / 2);

      if (isBad(mid)) {
        right = mid;
      } else {
        left = mid + 1;
      }
    }

    return isBad(left) ? left : -1;
  };
}
```



# 109.implement-Math-pow.md

### Problem Description

Can you write your own [Math.pow()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/pow) ? The power would only be integers.

```js
pow(1, 2);
// 1

pow(2, 10);
// 1024

pow(4, -1);
// 0.25
```

All inputs are safe.

**Follow-up**

You can easily solve this problem by multiplying the base one after another, but it is slow. For power of `n`, it is needed to do the multiplication `n` times, can you think of a faster solution ?

#

### Solution

```js
/**
 * @param {number} base
 * @param {number} power - integer
 * @return {number}
 */
function pow(base, power) {
  if (power < 0) {
    return 1 / _pow(base, -power);
  }

  return _pow(base, power);
}

function _pow(base, power) {
  if (power === 0) {
    return 1;
  }

  if (power === 1) {
    return base;
  }

  const halfResult = _pow(base, Math.floor(power / 2));
  const result = halfResult * halfResult;
  return power % 2 === 0 ? result : base * result;
}
```



# 11.what-is-Composition-create-a-pipe.md

### Problem Description

what is Composition? It is actually not that difficult to understand, see [@dan_abramov 's explanation](https://whatthefuck.is/composition).

Here you are asked to create a `pipe()` function, which chains multiple functions together to create a new function.

Suppose we have some simple functions like this

```js
const times = (y) => (x) => x * y;
const plus = (y) => (x) => x + y;
const subtract = (y) => (x) => x - y;
const divide = (y) => (x) => x / y;
```

Your `pipe()` would be used to generate new functions

```js
pipe([times(2), times(3)]);
// x _ 2 _ 3

pipe([times(2), plus(3), times(4)]);
// (x _ 2 + 3) _ 4

pipe([times(2), subtract(3), divide(4)]);
// (x \* 2 - 3) / 4
```

**notes**

1. to make things simple, functions passed to `pipe()` will all accept 1 argument

#

### Solution

```js
/**
 * @param {Array<(arg: any) => any>} funcs
 * @return {(arg: any) => any}
 */
function pipe(funcs) {
  return function (n) {
    let result = n;
    for (let func of funcs) {
      result = func(result);
    }
    return result;
  };
}
```



# 113.Virtual-DOM-I.md

### Problem Description

Suppose you have solved [110. serialize and deserialize binary tree](https://bigfrontend.dev/problem/serialize-and-deserialize-binary-tree), have you wondered how to do similar task to DOM tree ?

HTML string could be thought as some sort of [serialization](https://en.wikipedia.org/wiki/Serialization), the browser parses(deserialize) the HTML → construct the DOM tree.

Besides XML base, we could try JSON for this. If we log the element presentation in React, like below

```js
const el = (
  <div>
    <h1> this is </h1>
    <p className="paragraph">
      {' '}
      a <button> button </button> from <a href="https://bfe.dev">
        <b>BFE</b>.dev
      </a>
    </p>
  </div>
);

console.log(el);
```

we would get this( ref, key .etc are stripped off)

```js
{
  type: 'div',
  props: {
    children: [
      {
        type: 'h1',
        props: {
          children: ' this is ',
        },
      },
      {
        type: 'p',
        props: {
          className: 'paragraph',
          children: [
            ' a ',
            {
              type: 'button',
              props: {
                children: ' button ',
              },
            },
            ' from',
            {
              type: 'a',
              props: {
                href: 'https://bfe.dev',
                children: [
                  {
                    type: 'b',
                    props: {
                      children: 'BFE',
                    },
                  },
                  '.dev',
                ],
              },
            },
          ],
        },
      },
    ],
  },
};
```

Clearly this is the same tree structure but only in object literal.

Now you are asked to serialize/deserialize the DOM tree, like what React does.

**Note**

**Functions like event handlers and custom components are beyond the scope of this problem, you can ignore them**, just focus on basic HTML tags.

You should support:

1. TextNode (string) as children
2. single child and multiple children
3. camelCased properties

`virtualize()` takes in a real DOM tree and create an object literal. `render()` takes in a object literal presentation and recreate a DOM tree.

#

### Solution

```js
/**
 * @param {HTMLElement}
 * @return {object} object literal presentation
 */
function virtualize(element) {
  const result = {
    type: element.nodeName.toLowerCase(),
    props: {},
  };

  for (const attr of element.attributes) {
    const name = attr.nodeName === 'class' ? 'className' : attr.nodeName;
    result.props[name] = attr.nodeValue;
  }

  const children = [];
  for (const childNode of element.childNodes) {
    if (childNode.nodeType === Node.TEXT_NODE) {
      children.push(childNode.nodeValue);
      continue;
    }

    const child = virtualize(childNode);
    children.push(child);
  }

  result.props.children = children.length === 1 ? children[0] : children;
  return result;
}

/**
 * @param {object} valid object literal presentation
 * @return {HTMLElement}
 */
function render(obj) {
  if (typeof obj === 'string') {
    return document.createTextNode(obj);
  }

  const {
    type,
    props: { children, ...attrs },
  } = obj;
  const element = document.createElement(type);

  for (const attr in attrs) {
    const attrName = attr === 'className' ? 'class' : attr;
    element.setAttribute(attrName, attrs[attr]);
  }

  if (typeof children === 'string') {
    element.appendChild(render(children));
    return element;
  }

  for (const child of children) {
    element.appendChild(render(child));
  }

  return element;
}
```



# 118.Virtual-DOM-II-createElement.md

### Problem Description

> This is a follow-up on [113. Virtual DOM I](https://bigfrontend.dev/problem/Virtual-DOM-I).

Suppose you have solved above problem, now let's take a look at [React.createElement()](https://reactjs.org/docs/react-api.html#createelement):

```js
React.createElement(type, [props], [...children]);
```

1. First argument is type, it could be set to Custom Component, but here in this problem, it would only be HTML tag name
2. Second argument is props, here in this problem, it would only be the (common) camelCased HTML attributes
3. the rest arguments are the children, which in React supports many data types, but in this problem, it only has the element type of MyElement, or string for TextNode.

**You are asked to create your own createElement() and render()**, so that following code could create the exact HTMLElement in [113. Virtual DOM I](https://bigfrontend.dev/problem/Virtual-DOM-I).

```js
const h = createElement;

render(
  h(
    'div',
    {},
    h('h1', {}, ' this is '),
    h(
      'p',
      { className: 'paragraph' },
      ' a ',
      h('button', {}, ' button '),
      ' from ',
      h('a', { href: 'https://bfe.dev' }, h('b', {}, 'BFE'), '.dev')
    )
  )
);
```

**Notes**

1. The goal of this problem is not to create the replica of React implementation, you can have your own object representation format other than the one in [113. Virtual DOM I](https://bigfrontend.dev/problem/Virtual-DOM-I).

2. Details about ref, key are ignored here, they will be put in other problems. Re-render is not covered here, it will be in another problem as well.

#

### Solution

```js
/**
 * MyElement is the type your implementation supports
 *
 * type MyNode = MyElement | string
 */

/**
 * @param { string } type - valid HTML tag name
 * @param { object } [props] - properties.
 * @param { ...MyNode} [children] - elements as rest arguments
 * @return { MyElement }
 */
function createElement(type, props, ...children) {
  return {
    type,
    props: {
      ...props,
      children,
    },
  };
}

/**
 * @param { MyElement }
 * @returns { HTMLElement }
 */
function render(myElement) {
  if (typeof myElement === 'string') {
    return document.createTextNode(myElement);
  }

  const {
    type,
    props: { children, ...attrs },
  } = myElement;
  const element = document.createElement(type);

  for (const attrName in attrs) {
    const _attrName = attrName === 'className' ? 'class' : attrName;
    element.setAttribute(_attrName, attrs[attrName]);
  }

  for (const child of children) {
    element.appendChild(render(child));
  }

  return element;
}
```



# 12.implement-Immutability-helper.md

### Problem Description

If you use React, you would meet the scenario to copy the state for a slight change.

For example, for following state

```js
const state = {
  a: {
    b: {
      c: 1,
    },
  },
  d: 2,
};
```

if we are to modify `d` to a new state, we could use [\_.cloneDeep](https://lodash.com/docs/4.17.15#cloneDeep), but it is not efficient because `state.a` is cloned while we don't need to change that.

A better way is to do shallow copy like this

```js
const newState = {
  ...state,
  d: 3,
};
```

now is the problem, if we want to modify `c`, we would have to do something like

```js
const newState = {
  ...state,
  a: {
    ...state.a,
    b: {
      ...state.b,
      c: 2,
    },
  },
};
```

We can see that for simple data structure it would be enough to use spread operator, but for complex data structures, it is verbose.

Here comes the [Immutability Helper](https://reactjs.org/docs/update.html), you are asked to implement your own Immutability Helper `update()`, which supports following features.

**1. {$push: array} push() all the items in array on the target.**

```js
const arr = [1, 2, 3, 4];
const newArr = update(arr, { $push: [5, 6] });
// [1, 2, 3, 4, 5, 6]
```

**2. {$set: any} replace the target**

```js
const state = {
  a: {
    b: {
      c: 1,
    },
  },
  d: 2,
};

const newState = update(state, { a: { b: { c: { $set: 3 } } } });
/*
{
  a: {
    b: {
      c: 3
    }
  },
  d: 2
}
*/
```

Notice that we could also update array elements with `$set`

```js
const arr = [1, 2, 3, 4];
const newArr = update(arr, { 0: { $set: 0 } });
//  [0, 2, 3, 4]
```

**3. {$merge: object} merge object to the location**

```js
const state = {
  a: {
    b: {
      c: 1,
    },
  },
  d: 2,
};

const newState = update(state, { a: { b: { $merge: { e: 5 } } } });
/*
{
  a: {
    b: {
      c: 3,
      e: 5
    }
  },
  d: 2
}
*/
```

**4. {$apply: function} custom replacer**

```js
const arr = [1, 2, 3, 4];
const newArr = update(arr, { 0: { $apply: (item) => item * 2 } });
// [2, 2, 3, 4]
```

#

### Solution

```js
/**
 * @param {any} data
 * @param {Object} command
 */
function update(data, command) {
  if (typeof data !== 'object' && !Array.isArray(data)) {
    throw new Error();
  }

  let copiedData = copy(data);
  _update(copiedData, command);
  return copiedData;
}

function _update(data, command) {
  for (const key in command) {
    if (key === '$push' && Array.isArray(command[key]) && Array.isArray(data)) {
      data.push(...command[key]);
      return;
    }

    if (
      typeof command[key] === 'object' &&
      command[key].hasOwnProperty('$set')
    ) {
      data[key] = command[key].$set;
      return;
    }

    if (
      typeof command[key] === 'object' &&
      command[key].hasOwnProperty('$apply') &&
      Array.isArray(data)
    ) {
      if (data[key]) {
        data[key] = command[key].$apply(data[key]);
        return;
      }
    }

    if (
      typeof command[key] === 'object' &&
      command[key].hasOwnProperty('$merge')
    ) {
      if (typeof data[key] === 'object') {
        data[key] = {
          ...data[key],
          ...command[key].$merge,
        };
        return;
      } else {
        throw new Error();
      }
    }

    if (typeof command[key] === 'object') {
      _update(data[key], command[key]);
    }
  }
}

function copy(data) {
  let newData;

  if (Array.isArray(data)) {
    newData = [];

    for (const el of data) {
      if (Array.isArray(el) || typeof el === 'object') {
        newData.push(copy(el));
      } else {
        newData.push(el);
      }
    }
  } else if (typeof data === 'object') {
    newData = {};

    for (const key in data) {
      if (typeof data[key] === 'object' || Array.isArray(data[key])) {
        newData[key] = copy(data[key]);
      } else {
        newData[key] = data[key];
      }
    }
  }

  return newData;
}
```



# 13.Implement-a-Queue-by-using-Stack.md

### Problem Description

In JavaScript, we could use array to work as both a Stack or a queue.

```js
const arr = [1, 2, 3, 4];

arr.push(5); // now array is [1, 2, 3, 4, 5]
arr.pop(); // 5, now the array is [1, 2, 3, 4]
```

Above code is a Stack, while below is a Queue

```js
const arr = [1, 2, 3, 4];

arr.push(5); // now the array is [1, 2, 3, 4, 5]
arr.shift(); // 1, now the array is [2, 3, 4, 5]
```

now suppose you have a stack, which has only follow interface:

```js
class Stack {
  push(element) {
    /* add element to stack */
  }
  peek() {
    /* get the top element */
  }
  pop() {
    /* remove the top element */
  }
  size() {
    /* count of elements */
  }
}
```

Could you implement a Queue by using only above Stack? A Queue must have following interface

```js
class Queue {
  enqueue(element) {
    /* add element to queue, similar to Array.prototype.push */
  }
  peek() {
    /* get the head element */
  }
  dequeue() {
    /* remove the head element, similar to Array.prototype.pop */
  }
  size() {
    /* count of elements */
  }
}
```

**notes**

you can only use Stack as provided, Array should be avoided for the purpose of practicing.

#

### Solution 1 (O(1) enqueue, O(n) dequeue and peek)

```js
/* you can use this Class which is bundled together with your code

class Stack {
  push(element) { // add element to stack }
  peek() { // get the top element }
  pop() { // remove the top element}
  size() { // count of element }
}
*/

/* Array is disabled in your code */

// you need to complete the following Class
class Queue {
  constructor() {
    this.content = new Stack();
  }

  enqueue(element) {
    this.content.push(element);
  }
  peek() {
    const reversedQueue = this._reverse(this.content);
    return reversedQueue.peek();
  }
  size() {
    return this.content.size();
  }
  dequeue() {
    const reversedQueue = this._reverse(this.content);
    const poppedItem = reversedQueue.pop();
    this.content = this._reverse(reversedQueue);
    return poppedItem;
  }
  _reverse(queue) {
    const reversedQueue = new Stack();
    while (queue.size() > 0) {
      const lastItem = queue.pop();
      reversedQueue.push(lastItem);
    }

    return reversedQueue;
  }
}
```

#

### Solution 2 (O(n) enqueue, O(1) dequeue and peek)

```js
/* you can use this Class which is bundled together with your code

class Stack {
  push(element) { // add element to stack }
  peek() { // get the top element }
  pop() { // remove the top element}
  size() { // count of element }
}
*/

/* Array is disabled in your code */

// you need to complete the following Class
class Queue {
  constructor() {
    this.content = new Stack();
    this.reversedContent = new Stack();
  }

  enqueue(element) {
    while (this.content.size() > 0) {
      const poppedItem = this.content.pop();
      this.reversedContent.push(poppedItem);
    }

    this.reversedContent.push(element);

    while (this.reversedContent.size() > 0) {
      const poppedItem = this.reversedContent.pop();
      this.content.push(poppedItem);
    }
  }
  peek() {
    return this.content.peek();
  }
  size() {
    return this.content.size();
  }
  dequeue() {
    return this.content.pop();
  }
}
```



# 14.Implement-a-general-memoization-function.md

### Problem Description

[Memoization](https://whatthefuck.is/memoization) is a common technique to boost performance. If you use React, you definitely have used `React.memo` before.

Memoization is also commonly used in algorithm problem, when you have a recursion solution, in most cases, you can improve it by memoization, and then you might be able to get a Dynamic Programming approach.

So could you implement a general `memo()` function, which cache the result once called, so when same arguments are passed in, the result will be returned right away.

```js
const func = (arg1, arg2) => {
  return arg1 + arg2;
};

const memoed = memo(func);

memoed(1, 2);
// 3, func is called

memoed(1, 2);
// 3 is returned right away without calling func

memoed(1, 3);
// 4, new arguments, so func is called
```

The parameters are arbitrary, so memo should accept an extra resolver parameter, which is used to generate the cache key, like what [\_.memoize()](https://lodash.com/docs/4.17.15#memoize) does.

```js
const memoed = memo(func, () => 'samekey');

memoed(1, 2);
// 3, func is called, 3 is cache with key 'samekey'

memoed(1, 2);
// 3, since key is the same, 3 is returned without calling func

memoed(1, 3);
// 3, since key is the same, 3 is returned without calling func
```

Default cache key could be just `Array.from(arguments).join('_')`

**note**

It is a trade-off of space for time, so if you use this in an interview, please do analyze how much space it might cost

#

### Solution

```js
/**
 * @param {Function} func
 * @param {(args:[]) => string }  [resolver] - cache key generator
 */
function memo(func, resolver) {
  const cache = new Map();

  return function (...args) {
    const key = resolver ? resolver(...args) : args.join('_');
    if (cache.has(key)) {
      return cache.get(key);
    }

    const result = func.apply(this, args);
    cache.set(key, result);
    return result;
  };
}
```



# 140.Virtual-DOM-III-Functional-Component.md

### Problem Description

> This is a follow-up on [118. Virtual DOM II - createElement](https://bigfrontend.dev/problem/virtual-dom-II-createElement).

In problem 118, you are asked to implement `createElement()` and `render()` function which supports intrinsic HTML elements, like `<p/>`, `<div/>` etc.

In this problem, you are ask to support custom **Functional Component**.

[Functional Component](https://reactjs.org/docs/components-and-props.html#function-and-class-components) are functions that:

1. accept **single object argument** -`props`, which contains `children`, `className` and other properties.
2. returns an MyElement by calling `createElement()`.

Say we have a Functional Component - `Title`

```js
const h = createElement;
const Title = ({ children, ...res }) => h('h1', res, ...children);
```

Then we should be able to use it in `createElement` and `render()`, just the same way as an intrinsic element.

```js
h(Title, {}, 'This is a title');

h(Title, { className: 'class1' }, 'This is a title');
```

Please **modify your createElement() and render()** from [118. Virtual DOM II - createElement](https://bigfrontend.dev/problem/virtual-dom-II-createElement) if necessary, so that the example in problem 118 could be rewritten as below:

```js
const Link = ({ children, ...res }) => h('a', res, ...children);
const Name = ({ children, ...res }) => h('b', res, ...children);
const Button = ({ children, ...res }) => h('button', res, ...children);
const Paragraph = ({ children, ...res }) => h('p', res, ...children);
const Container = ({ children, ...res }) => h('div', res, ...children);

h(
  Container,
  {},
  h(Title, {}, ' this is '),
  h(
    Paragraph,
    { className: 'paragraph' },
    ' a ',
    h(Button, {}, ' button '),
    ' from ',
    h(Link, { href: 'https://bfe.dev' }, h(Name, {}, 'BFE'), '.dev')
  )
);
```

#

### Solution 1

```js
/**
 * MyElement is the type your implementation supports
 *
 * type MyNode = MyElement | string
 * type FunctionComponent = (props: object) => MyElement
 */

/**
 * @param { string | FunctionComponent } type - valid HTML tag name or Function Component
 * @param { object } [props] - properties.
 * @param { ...MyNode} [children] - elements as rest arguments
 * @return { MyElement }
 */
function createElement(type, props, ...children) {
  return {
    type,
    props: {
      ...props,
      children,
    },
  };
}

/**
 * @param { MyElement }
 * @returns { HTMLElement }
 */
function render(myElement) {
  if (typeof myElement === 'string') {
    return document.createTextNode(myElement);
  }

  const { type, props } = myElement;

  if (typeof type === 'function') {
    return render(type(props));
  }

  const { children, ...attrs } = props;
  const element = document.createElement(type);

  for (const attrName in attrs) {
    const _attrName = attrName === 'className' ? 'class' : attrName;
    element.setAttribute(_attrName, attrs[attrName]);
  }

  for (const child of children) {
    element.appendChild(render(child));
  }

  return element;
}
```

#

### Solution 2

```js
/**
 * MyElement is the type your implementation supports
 *
 * type MyNode = MyElement | string
 * type FunctionComponent = (props: object) => MyElement
 */

/**
 * @param { string | FunctionComponent } type - valid HTML tag name or Function Component
 * @param { object } [props] - properties.
 * @param { ...MyNode} [children] - elements as rest arguments
 * @return { MyElement }
 */
function createElement(type, props, ...children) {
  if (typeof type === 'function') {
    return type({ children, ...props });
  }

  return {
    type,
    props: {
      ...props,
      children,
    },
  };
}

/**
 * @param { MyElement }
 * @returns { HTMLElement }
 */
function render(myElement) {
  if (typeof myElement === 'string') {
    return document.createTextNode(myElement);
  }

  const {
    type,
    props: { children, ...attrs },
  } = myElement;
  const element = document.createElement(type);

  for (const attrName in attrs) {
    const _attrName = attrName === 'className' ? 'class' : attrName;
    element.setAttribute(_attrName, attrs[attrName]);
  }

  for (const child of children) {
    element.appendChild(render(child));
  }

  return element;
}
```



# 143.Virtual-DOM-IV-JSX-1.md

### Problem Description

If you are using React, you must be familiar with [JSX](https://facebook.github.io/jsx/).

With JSX syntax support, transpilers are able to understand below non-standard code directly in JavaScript.

```JSX
<p>this is <button className="button">button</button></p>
```

Then it is transpiled to standard JavaScript function calls.

```js
React.createElement(
  'p',
  null,
  ' this is ',
  React.createElement('button', { className: 'button' }, 'button'),
  ' '
);
```

> have a try at [TypeScript Playground](https://www.typescriptlang.org/play?#code/DwBwfABALgFglgZwoiwBGBXKUD2A7CAYwBsBDBBAOVIFsBTAXgCJNt8mxXc9gB6L-JD7ggA)

To illustrate how the transpilation works, let's start with a simple example.

```JSX
<a>bfe.dev</a>
```

First the parser will create an AST(Abstract Syntax Tree) from the code.

Open above code [in AST Explorer](https://astexplorer.net/#/gist/46044fc473a92974cd8f933efc7635f6/8a876a4240ecf38d64c0e0af3c693a1c54d80525), you can see the AST in the right pannel, roughly something like this:

```js
expression: JSXElement {
  openingElement: JSXOpeningElement {
    name: JSXIdentifier {
      name: "a"
    }
  }
  closingElement: JSXClosingElement {
    name: JSXIdentifier {
      name: "a"
    }
  }
  children: [
    JSXText {
      value: "bfe.dev"
    }
  ]
}
```

Obviously above AST follows the [JSX Spec](https://facebook.github.io/jsx/):

```
JSXElement:
  JSXOpeningElement JSXChildren? JSXClosingElement

JSXOpeningElement:
  < JSXElementName JSXAttributes? >

JSXChildren:
  JSXChild JSXChildren?

JSXClosingElement:
  < / JSXElementName >

JSXChild:
  JSXText
  JSXElement
  { JSXChildExpression? }
```

With the above AST, it is fairly easy to generate code, we only need to traverse the AST and insert `React.createElement`:

```js
React.createElement(
  'p',
  null,
  ' this is ',
  React.createElement('button', { className: 'button' }, 'button'),
  ' '
);
```

Also instead of React method, we could use `h()` defined in [140. Virtual DOM III - Functional Component](https://bigfrontend.dev/problem/virtual-DOM-III-Functional-Component) instead.

```js
h('p', null, ' this is ', h('button', { className: 'button' }, 'button'), ' ');
```

**Now, please create your own parse() and generate() to transpile JSX Element code.**

1. please generate code which uses `h()`, `h()` is bundled with your code.
2. Goal of this problem is not to recreate the full parser, so only need to support the minimum spec below:

```
JSXElement:
   JSXOpeningElement JSXChildren? JSXClosingElement

JSXOpeningElement:
  < JSXElementName >

JSXChildren:
  JSXChild

JSXClosingElement:
  < / JSXElementName >

JSXChild:
  JSXText
```

- you can choose not to follow the naming
- there is no newlines in the input, you can ignore the [whitespace rules](https://github.com/facebook/react/pull/480#issuecomment-31296039)
- all input tags are smallcase HTML tags

3.  for simplicity, the AST creating process with `parse()` won't be tested, rather `parse()` and `generate()` are tested together like this:

```js
const result = eval(generate(parse('<a>bfe.dev</a>')));
expect(result).toEqual(h('a', null, 'bfe.dev'));
```

4. An error should be thrown if code is not valid JSXElement, for example, the JSXOpeningElement and JSXClosingElement might not be matched.

> The test cases only cover some of the common errors.

#

### Solution with regular expression

```js
/**
 * @param {code} string
 * @returns {any} AST
 */
function parse(code) {
  const regex = /^\s*<\s*(\w+)\s*>([^<>]*)<\s*\/\s*(\w+)\s*>\s*$/;
  const match = code.match(regex);
  if (!match) {
    throw new Error();
  }

  const openingEl = match[1];
  const closingEl = match[3];
  if (openingEl !== closingEl) {
    throw new Error();
  }

  return {
    openingElement: {
      name: openingEl,
    },
    closingElement: {
      name: closingEl,
    },
    children: match[2] ? [match[2]] : [],
  };
}

/**
 * @param {any} your AST
 * @returns {string}
 */
function generate(ast) {
  return {
    type: ast.openingElement.name,
    props: {
      children: ast.children,
    },
  };
}
```

#

### Solution without regular expression

```js
/**
 * @param {code} string
 * @returns {any} AST
 */
function parse(code) {
  code = code.trim();
  if (code[0] !== '<' || code[code.length - 1] !== '>') {
    return;
  }

  const splits1 = code.split('<').length;
  const splits2 = code.split('>').length;
  if (splits1 !== splits2) {
    return;
  }
  if (splits1 <= 2) {
    return;
  }

  // Parse opening element
  let i = 1;
  let openingEl = '';
  while (code[i] !== '>') {
    if (code[i] === ' ') {
      i++;
      continue;
    }

    openingEl += code[i];
    i++;
  }

  // Parse closing element
  let j = code.length - 2;
  let closingEl = '';
  while (code[j] !== '<') {
    if (code[j] === ' ') {
      j--;
      continue;
    }

    closingEl = code[j] + closingEl;
    j--;
  }

  if (closingEl[0] !== '/') {
    return;
  }

  closingEl = closingEl.slice(1);
  if (openingEl !== closingEl) {
    throw new Error();
  }

  // Parse child
  const child = code.slice(i + 1, j);

  return {
    openingElement: {
      name: openingEl,
    },
    closingElement: {
      name: closingEl,
    },
    children: child ? [child] : [],
  };
}

/**
 * @param {any} your AST
 * @returns {string}
 */
function generate(ast) {
  return {
    type: ast.openingElement.name,
    props: {
      children: ast.children,
    },
  };
}
```



# 15.implement-a-simple-DOM-wrapper-to-support-method-chaining-like-jQuery.md

### Problem Description

I believe you've used jQuery before, we often chain the jQuery methods together to accomplish our goals.

For example, below chained call turns button into a black button with white text.

```js
$('#button')
  .css('color', '#fff')
  .css('backgroundColor', '#000')
  .css('fontWeight', 'bold');
```

The chaining makes the code simple to read, could you create a simple wrapper `$` to make above code work as expected?

The wrapper only needs to have `css(propertyName: string, value: any)`

#

### Solution

```js
/**
 * @param {HTMLElement} el - element to be wrapped
 */
function $(el) {
  return {
    css(propertyName, value) {
      el.style[propertyName] = value;
      return this;
    },
  };
}
```



# 151.implement-Array-prototype-map.md

### Problem Description

Please implement your own [Array.prototype.map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map).

```js
[1, 2, 3].myMap((num) => num * 2);
// [2,4,6]
```

#

### Understanding the problem

Write a prototype method for `Array` that returns a new array populated with the result of calling a provided function on every element in the calling array.

#

### Approach

Create an empty array to store the result. Iterate through every items in the calling array. At each item, call the provided callback and pass the item, its index and the calling array as arguments to the callback; store the return value in the result array.

🙋‍♀️🙋‍♂️ In the initial attempt, I didn't handle the following cases:

1. Besides the callback function, our function can also take in a second argument, the optional `thisArg`, when it is provided, the callback function should be executed with the provided context.
2. The callback might alter the length of the calling array, resulting in an infinite loop.
3. The calling array is a sparse array. A sparse array is an array in which indices are not contiguous. For instance, `[1, 3]` is a sparse array and the indices are `0, 2`, index `1` is not in the array. Our function should ignore indices that are not present in the calling array.

### Solution

```js
Array.prototype.myMap = function (callback, thisArg) {
  // Store the length of the original array to avoid potential infinity loop
  // when the length of the calling array is altered on the fly.
  const length = this.length;
  // Initialize the resulting array to be the same size as the calling array.
  const result = new Array(length);

  for (let i = 0; i < length; i++) {
    // Ensure index is in the array. myMap should ignore
    // indices that are not in the array.
    if (i in this) {
      // Execute the callback with proper context and store its return value in the
      // result array.
      result[i] = callback.call(thisArg || this, this[i], i, this);
    }
  }

  return result;
};
```



# 16.create-an-Event-Emitter.md

### Problem Description

There is [Event Emitter in Node.js](https://nodejs.org/api/events.html#events_class_eventemitter), Facebook once had [its own implementation](https://github.com/facebookarchive/emitter) but now it is archived.

You are asked to create an Event Emitter Class

```js
const emitter = new Emitter();
```

It should support event subscribing

```js
const sub1 = emitter.subscribe('event1', callback1);
const sub2 = emitter.subscribe('event2', callback2);

// same callback could subscribe
// on same event multiple times
const sub3 = emitter.subscribe('event1', callback1);
```

`emit(eventName, ...args)` is used to trigger the callbacks, with args relayed

```js
emitter.emit('event1', 1, 2);
// callback1 will be called twice
```

Subscription returned by `subscribe()` has a `release()` method that could be used to unsubscribe

```js
sub1.release();
sub3.release();
// now even if we emit 'event1' again,
// callback1 is not called anymore
```

#

### Solution

```js
class EventEmitter {
  constructor() {
    this.callbacks = new Map();
  }

  subscribe(eventName, callback) {
    if (!this.callbacks.has(eventName)) {
      this.callbacks.set(eventName, [callback]);
    } else {
      const cbs = this.callbacks.get(eventName);
      cbs.push(callback);
    }
    return {
      release: () => {
        const cbs = this.callbacks.get(eventName);
        const cbIndex = cbs.indexOf(callback);
        cbs.splice(cbIndex, 1);
      },
    };
  }

  emit(eventName, ...args) {
    const cbs = this.callbacks.get(eventName);
    if (!cbs.length) {
      return;
    }

    for (const cb of cbs) {
      cb.apply(this, args);
    }
  }
}
```



# 17.create-a-simple-store-for-DOM-element.md

### Problem Description

We have `Map` in es6, so we could use any data as key, such as DOM element.

```js
const map = new Map();
map.set(domNode, somedata);
```

What if we need to support old JavaScript env like es5, how would you create your own Node Store as above?

You are asked to implement a Node Store, which supports DOM element as key.

```js
class NodeStore {
  set(node, value) {}

  get(node) {}

  has(node) {}
}
```

**note**

`Map` is disabled when judging your code, it is against the goal of practicing.

You can create a simple general `Map` polyfill. Or since you are asked to support specially for DOM element, what is special about DOM element?

What is the Time / Space cost of your solution?

#

### Solution

```js
class NodeStore {
  constructor() {
    this.nodes = {};
  }
  /**
   * @param {Node} node
   * @param {any} value
   */
  set(node, value) {
    node.__id__ = Symbol();
    this.nodes[node.__id__] = value;
  }
  /**
   * @param {Node} node
   * @return {any}
   */
  get(node) {
    return this.nodes[node.__id__];
  }

  /**
   * @param {Node} node
   * @return {Boolean}
   */
  has(node) {
    // coerce to boolean value
    return !!this.nodes[node.__id__];
  }
}
```



# 18.improve-a-function.md

### Problem Description

```js
// Given an input of array,
// which is made of items with >= 3 properties

let items = [
  {color: 'red', type: 'tv', age: 18},
  {color: 'silver', type: 'phone', age: 20},
  {color: 'blue', type: 'book', age: 17}
]

// an exclude array made of key value pair
const excludes = [
  {k: 'color', v: 'silver'},
  {k: 'type', v: 'tv'},
  ...
]

function excludeItems(items, excludes) {
  excludes.forEach( pair => {
    items = items.filter(item => item[pair.k] === item[pair.v])
  })

  return items
}
```

1. What does this function `excludeItems` do?
2. Is this function working as expected ?
3. What is the time complexity of this function?
4. How would you optimize it ?

**note**

we only judge by the result, not the time cost. please submit the best approach you can.

#

### Solution

Answers to the questions:

1. The function `excludeItems` returns items that match the conditions in `excludes` array.
2. No. Because the function is called `excludesItems`, it should exclude items that match the conditions.
3. O(n \* m)
4. We should also handle duplicate keys in `excludes` array.

```js
/**
 * @param {object[]} items
 * @param { Array< {k: string, v: any} >} excludes
 * @return {object[]}
 */
function excludeItems(items, excludes) {
  const excludesMap = new Map();

  for (const { k, v } of excludes) {
    if (!excludesMap.has(k)) {
      excludesMap.set(k, new Set());
    }
    excludesMap.get(k).add(v);
  }

  return items.filter((item) =>
    Object.keys(item).every(
      // Time complexity of V8's Set.has() is practically O(1).
      (key) => !excludesMap.has(key) || !excludesMap.get(key).has(item[key])
    )
  );
```

#

### Reference

https://www.youtube.com/watch?v=EC_M8LlguC0&ab_channel=JSer



# 19.find-corresponding-node-in-two-identical-DOM-tree.md

### Problem Description

Given two same DOM tree **A**, **B**, and an Element **a** in **A**, find the corresponding Element **b** in **B**.

By **corresponding**, we mean **a** and **b** have the same relative position to their DOM tree root.

_follow up_

This could a problem on general Tree structure with only `children`.

Could you solve it recursively and iteratively?

Could you solve this problem both with special DOM api for better performance?

What are the time cost for each solution?

#

### Recursive Solution

```js
/**
 * @param {HTMLElement} rootA
 * @param {HTMLElement} rootB - rootA and rootB are clone of each other
 * @param {HTMLElement} nodeA
 */
const findCorrespondingNode = (rootA, rootB, target) => {
  if (target === rootA) {
    return rootB;
  }

  const childNodesA = Array.from(rootA.childNodes);
  const childNodesB = Array.from(rootB.childNodes);

  for (let i = 0; i < childNodesA.length; i++) {
    const b = findCorrespondingNode(childNodesA[i], childNodesB[i], target);
    if (b) {
      return b;
    }
  }

  return;
};
```

#

### Iterative Solution

```js
/**
 * @param {HTMLElement} rootA
 * @param {HTMLElement} rootB - rootA and rootB are clone of each other
 * @param {HTMLElement} nodeA
 */
const findCorrespondingNode = (rootA, rootB, target) => {
  let stackA = [rootA];
  let stackB = [rootB];

  while (stackA.length > 0) {
    const nodeA = stackA.pop();
    const nodeB = stackB.pop();

    if (nodeA === target) {
      return nodeB;
    }

    stackA.push(...nodeA.childNodes);
    stackB.push(...nodeB.childNodes);
  }

  return;
};
```

#

### Solution with DOM API `parentNode`

```js
/**
 * @param {HTMLElement} rootA
 * @param {HTMLElement} rootB - rootA and rootB are clone of each other
 * @param {HTMLElement} nodeA
 */
const findCorrespondingNode = (rootA, rootB, target) => {
  const path = [];
  let node = target;
  while (node !== rootA) {
    const parentNode = node.parentNode;
    const childNodes = Array.from(parentNode.childNodes);
    path.push(childNodes.indexOf(node));
    node = parentNode;
  }

  return path.reduceRight((node, index) => node.childNodes[index], rootB);
};
```

#

### Reference

[Problem Discuss](https://bigfrontend.dev/problem/find-corresponding-node-in-two-identical-DOM-tree/discuss)



# 2.implement-curry-with-placeholder-support.md




# 20.detect-data-type-in-JavaScript.md

### Problem Description

This is an easy problem.

For [all the basic data types](https://javascript.info/types) in JavaScript, how could you write a function to detect the type of arbitrary data?

Besides basic types, you need to also handle also commonly used complex data type including `Array`, `ArrayBuffer`, `Map`, `Set`, `Date` and `Function`

The goal is not to list up all the data types but to show us how to solve the problem when we need to.

The type should be lowercase

```js
detectType(1); // 'number'
detectType(new Map()); // 'map'
detectType([]); // 'array'
detectType(null); // 'null'

// more in judging step
```

#

### Solution

```js
const dataTypes = new Map([
  [Number, 'number'],
  [String, 'string'],
  [Boolean, 'boolean'],
  [Array, 'array'],
  [ArrayBuffer, 'arraybuffer'],
  [Date, 'date'],
  [Map, 'map'],
  [Set, 'set'],
]);

/**
 * @param {any} data
 * @return {string}
 */
function detectType(data) {
  if (typeof data !== 'object') {
    return typeof data;
  }

  if (data === null) {
    return 'null';
  }

  for (const [type, name] of dataTypes.entries()) {
    if (data instanceof type) {
      return name;
    }
  }

  return 'object';
}
```



# 21.implement-JSON-stringify.md

### Problem Description

I believe you've used `JSON.stringify()` before, do you know the details of how it handles arbitrary data?

Please have a guess on the details and then take a look at the [explanation on MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify), it is actually pretty complex.

In this problem, you are asked to implement your own version of `JSON.stringify()`.

In a real interview, you are not expected to cover all the cases, just decide the scope with interviewer. But for a goal of practicing, your code here will be tested against a lot of data types. Please try to cover as much as you can.

Attention to the circular reference.

**note**

`JSON.stringify()` support two more parameters which is not required here.

#

### Solution

```js
/**
 * @param {any} data
 * @return {string}
 */
function stringify(data) {
  const typeOfData = detectDataType(data);

  if (typeOfData === 'array') {
    return stringifyArr(data);
  }

  if (typeOfData === 'object' || typeOfData === 'map') {
    return stringifyObj(data);
  }

  return _stringify(typeOfData, data);
}

function stringifyObj(data) {
  let stringifiedData = [];

  for (const key of Object.keys(data)) {
    const val = data[key];
    const typeOfVal = detectDataType(val);

    if (
      typeOfVal === 'symbol' ||
      typeOfVal === 'function' ||
      typeOfVal === 'undefined'
    ) {
      continue;
    }

    let stringifiedKey = `\"${key}\":`;

    switch (typeOfVal) {
      case 'array':
        stringifiedKey += stringifyArr(val);
        break;
      case 'object':
      case 'map':
        stringifiedKey += stringifyObj(val);
        break;
      default:
        stringifiedKey += _stringify(typeOfVal, val);
    }

    stringifiedData.push(stringifiedKey);
  }

  return `{${stringifiedData.join(',')}}`;
}

function stringifyArr(data) {
  let stringifiedData = [];

  for (const [index, val] of data.entries()) {
    if (isNaN(index)) {
      continue;
    }

    const typeOfVal = detectDataType(val);

    switch (typeOfVal) {
      case 'array':
        stringifiedData.push(stringifyArr(val));
        break;
      case 'object':
      case 'map':
        stringifiedData.push(stringifyObj(val));
        break;
      default:
        stringifiedData.push(_stringify(typeOfVal, val));
    }
  }

  return `[${stringifiedData.join(',')}]`;
}

function _stringify(typeOfData, data) {
  switch (typeOfData) {
    case 'string':
      return `\"${data}\"`;
    case 'number':
    case 'boolean':
      return String(data);
    case 'function':
      return undefined;
    case 'date':
      return `"${data.toISOString()}"`;
    case 'set':
    case 'map':
    case 'weakSet':
    case 'weakMap':
      return '{}';
    case 'bigint':
      throw new Error("TypeError: BigInt value can't be serialized in JSON");
    default:
      return 'null';
  }
}

const dataTypes = new Map([
  [Number, 'number'],
  [String, 'string'],
  [Boolean, 'boolean'],
  [Array, 'array'],
  [ArrayBuffer, 'arraybuffer'],
  [Date, 'date'],
  [Set, 'set'],
  [Map, 'map'],
  [WeakSet, 'weakSet'],
  [WeakMap, 'weakMap'],
]);

function detectDataType(data) {
  if (typeof data === 'number' && isNaN(data)) {
    return 'NaN';
  }

  if (data === Infinity) {
    return 'infinity';
  }

  if (typeof data !== 'object') {
    return typeof data;
  }

  if (data === null) {
    return 'null';
  }

  for (const [type, name] of dataTypes.entries()) {
    if (data instanceof type) {
      return name;
    }
  }

  return 'object';
}
```



# 22.implement-JSON-parse.md

### Problem Description

This is a follow-up on [21. implement JSON.stringify()](https://bigfrontend.dev/problem/implement-JSON-stringify).

Believe you are already familiar with `JSON.parse()`, could you implement your own version?

In case you are not sure about the spec, [MDN here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse) might help.

`JSON.parse()` support a second parameter `reviver`, you can ignore that.

#

### Solution 1

```js
/**
 * @param {string} str
 * @return {object | Array | string | number | boolean | null}
 */
function parse(str) {
  const parsed = eval('(' + str + ')');
  if (str !== JSON.stringify(parsed)) {
    throw new Error();
  }

  return parsed;
}
```

#

### Solution 2

```js
/**
 * @param {string} str
 * @return {object | Array | string | number | boolean | null}
 */
function parse(str) {
  const dataType = detectDataType(str);

  if (dataType === 'object') return parseObj(str);

  if (dataType === 'array') return parseArr(str);

  return parsePrimitive(str);
}

function parseObj(str) {
  const obj = {};

  str = str.slice(1, -1);
  if (str.endsWith(':')) throw new Error();

  while (str.length > 0) {
    str = skipLeadingSpace(str);

    const regex = /^"(.+?)"\s?:/;
    const matchedKey = str.match(regex);
    if (!matchedKey) throw new Error();

    const key = matchedKey[1];

    let rest = str.slice(matchedKey[0].length);
    rest = skipLeadingSpace(rest);

    let matchedVal;
    let val;
    if ((matchedVal = rest.match(/^({.+})\s?,?/))) {
      val = matchedVal[1];
      obj[key] = parseObj(val);
    } else if ((matchedVal = rest.match(/^(\[.+\])\s?,?/))) {
      val = matchedVal[1];
      obj[key] = parseArr(val);
    } else if ((matchedVal = rest.match(/^(\w+)\s?,?/u))) {
      val = matchedVal[1];
      obj[key] = parsePrimitive(val);
    } else if (
      (matchedVal = rest.match(/^("[\p{Emoji}\p{Alpha}]+.?")\s?,?/u))
    ) {
      val = matchedVal[1];
      obj[key] = parsePrimitive(val);
    } else {
      throw new Error();
    }

    str = rest.slice(matchedVal[0].length);
  }

  return obj;
}

function parseArr(str) {
  const arr = [];

  str = str.slice(1, -1);
  if (str.endsWith(',')) throw new Error();

  while (str.length > 0) {
    let item = str.match(/.+?,(?!"\w+":)/);
    if (!item) {
      item = str;
    } else {
      item = item[0].slice(0, -1);
    }
    const dataType = detectDataType(item);

    switch (dataType) {
      case 'object':
        arr.push(parseObj(item));
        break;
      case 'array':
        arr.push(parseArr(item));
        break;
      default:
        arr.push(parsePrimitive(item));
    }

    str = str.slice(item.length + 1);
  }

  return arr;
}

function parsePrimitive(str) {
  if (str.startsWith('"')) return str.slice(1, -1);

  if (!isNaN(str)) return Number(str);

  if (str === 'true') return true;

  if (str === 'false') return false;

  if (str === 'undefined') return undefined;

  return null;
}

function detectDataType(str) {
  if (str.startsWith('{') && str.endsWith('}')) {
    return 'object';
  }

  if (str.startsWith('[') && str.endsWith(']')) {
    return 'array';
  }

  return 'primitive';
}

function skipLeadingSpace(str) {
  const firstNonSpaceCharIdx = str.search(/\S/);
  if (firstNonSpaceCharIdx === -1) return '';
  return str.slice(firstNonSpaceCharIdx);
}
```



# 23.create-a-sum.md

### Problem Description

Create a `sum()`, which makes following possible

```js
const sum1 = sum(1);
sum1(2) == 3; // true
sum1(3) == 4; // true
sum(1)(2)(3) == 6; // true
sum(5)(-1)(2) == 6; // true
```

#

### Solution

```js
/**
 * @param {number} num
 */
function sum(num) {
  // Declare a function which will be returned by
  // the function sum(), so we can take a new number.
  const func = function (newNum) {
    // Add the new number to the previous one, call sum()
    // and pass the result as argument, so we can take a
    // new number again.
    return sum(num + newNum);
  };

  // Override func's native valueOf function with our custom one that
  // returns the current num, so we can do 'sum(1)(2)(3) == 6'.
  // When encountering an object where a primitive value is expected,
  // JavaScript calls valueOf method to convert the object (in js, functions are objects )
  // to a primitive value.
  // Besides valueOf, JavaScript also has toString to do such 'type coercion'.
  // sum(1)(2)(3) === 6 will result in false, because '===' does not perform
  // type conversion.
  func.valueOf = function () {
    return num;
  };

  return func;
}
```

#

### Reference

[Problem Discuss](https://bigfrontend.dev/problem/create-a-sum/discuss)



# 24.create-a-Priority-Queue-in-JavaScript.md

### Problem Description

[Priority Queue](https://storm.cis.fordham.edu/~yli/documents/CISC2200Spring15/Graph.pdf) is a commonly used data structure in algorithm problem. Especially useful for **Top K** problem with a huge amount of input data, since it could avoid sorting the whole but keep a fixed-length sorted portion of it.

Since there is no built-in Priority Queue in JavaScript, in a real interview, you should tell interview saying that "Suppose we already have a Priority Queue Class I can use", there is no time for you to write a Priority Queue from scratch.

But it is a good coding problem to practice, so please implement a Priority Queue with following interface

```js
class PriorityQueue {
  // compare is a function defines the priority, which is the order
  // the elements closer to first element is sooner to be removed.
  constructor(compare) {}

  // add a new element to the queue
  // you need to put it in the right order
  add(element) {}

  // remove the head element and return
  poll() {}

  // get the head element
  peek() {}

  // get the amount of items in the queue
  size() {}
}
```

Here is an example to make it clearer

```js
const pq = new PriorityQueue((a, b) => a - b);
// (a, b) => a - b means
// smaller numbers are closer to index:0
// which means smaller number are to be removed sooner

pq.add(5);
// now 5 is the only element

pq.add(2);
// 2 added

pq.add(1);
// 1 added

pq.peek();
// since smaller number are sooner to be removed
// so this gives us 1

pq.poll();
// 1
// 1 is removed, 2 and 5 are left

pq.peek();
// 2 is the smallest now, this returns 2

pq.poll();
// 2
// 2 is removed, only 5 is left
```

#

### Solution

```js
class PriorityQueue {
  /**
   * @param {(a: any, b: any) => -1 | 0 | 1} compare -
   * compare function, similar to parameter of Array.prototype.sort
   */
  constructor(compare) {
    this.compare = compare;
    this.heap = [];
  }

  /**
   * return {number} amount of items
   */
  size() {
    return this.heap.length;
  }

  /**
   * returns the head element
   */
  peek() {
    return this.heap[0];
  }

  /**
   * @param {any} value - new value to add
   */
  add(element) {
    this.heap.push(element);

    const heapSize = this.size();
    if (heapSize > 1) {
      this.heapifyUp(heapSize - 1);
    }
  }

  /**
   * remove the head element
   * @return {any} the head element
   */
  poll() {
    const heapSize = this.size();
    if (heapSize <= 1) {
      return this.heap.pop();
    }

    this.swap(0, heapSize - 1);
    const head = this.heap.pop();
    this.heapifyDown(0);
    return head;
  }

  heapifyUp(index) {
    const parentIndex = Math.floor((index - 1) / 2);

    if (parentIndex < 0) {
      return;
    }

    const comparison = this.compare(this.heap[index], this.heap[parentIndex]);
    if (comparison < 0) {
      this.swap(index, parentIndex);
      this.heapifyUp(parentIndex);
    }
  }

  heapifyDown(parentIndex) {
    const leftIndex = 2 * parentIndex + 1;
    const rightIndex = 2 * parentIndex + 2;
    let swappableIndex = parentIndex;
    const heapSize = this.size();
    let comparison = 0;

    if (leftIndex < heapSize) {
      comparison = this.compare(
        this.heap[leftIndex],
        this.heap[swappableIndex]
      );
      if (comparison < 0) {
        swappableIndex = leftIndex;
      }
    }

    if (rightIndex < heapSize) {
      comparison = this.compare(
        this.heap[rightIndex],
        this.heap[swappableIndex]
      );
      if (comparison < 0) {
        swappableIndex = rightIndex;
      }
    }

    if (swappableIndex !== parentIndex) {
      this.swap(parentIndex, swappableIndex);
      this.heapifyDown(swappableIndex);
    }
  }

  swap(i, j) {
    [this.heap[i], this.heap[j]] = [this.heap[j], this.heap[i]];
  }
}
```



# 25.reorder-array-with-new-indexes.md

### Problem Description

Suppose we have an array of items - `A`, and another array of indexes in numbers - `B`

```js
const A = ['A', 'B', 'C', 'D', 'E', 'F'];
const B = [1, 5, 4, 3, 2, 0];
```

You need to reorder A, so that the A[i] is put at index of B[i], which means B is the new index for each item of A.

For above example A should be modified inline to following

```js
['F', 'A', 'E', 'D', 'C', 'B'];
```

The input are always valid.

**follow-up**

It is fairly easy to do this by using extra `O(n)` space, could you solve it with `O(1)` space?

#

### Solution

```js
/**
 * @param {any[]} items
 * @param {number[]} newOrder
 * @return {void}
 */
function sort(items, newOrder) {
  for (let i = 0; i < newOrder.length; i++) {
    const newIndex = newOrder[i];
    if (newIndex !== i) {
      swap(newOrder, newIndex, i);
      swap(items, newIndex, i);
    }
  }
}

function swap(arr, i, j) {
  [arr[i], arr[j]] = [arr[j], arr[i]];
}
```



# 26.implement-Object-assign.md

### Problem Description

_The `Object.assign()` method copies all enumerable own properties from one or more source objects to a target object. It returns the target object._ (source: [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign))

It is widely used, Object Spread operator actually is internally the same as `Object.assign()` ([source](https://github.com/tc39/proposal-object-rest-spread/blob/master/Spread.md)). Following 2 lines of code are totally the same.

```js
let aClone = { ...a };
let aClone = Object.assign({}, a);
```

This is an easy one, could you implement `Object.assign()` with your own implementation?

#

### Solution

```js
/**
 * @param {any} target
 * @param {any[]} sources
 * @return {object}
 */
function objectAssign(target, ...sources) {
  if (!target) {
    throw new Error();
  }

  if (typeof target !== 'object') {
    const constructor = Object.getPrototypeOf(target).constructor;
    target = new constructor(target);
  }

  for (const source of sources) {
    if (!source) {
      continue;
    }

    const keys = [
      ...Object.keys(source),
      ...Object.getOwnPropertySymbols(source),
    ];
    for (const key of keys) {
      const descriptor = Object.getOwnPropertyDescriptor(target, key);
      if (descriptor && !descriptor.configurable) {
        throw new Error();
      }

      target[key] = source[key];
    }
  }

  return target;
}
```



# 27.implement-completeAssign.md

### Problem Description

This is a follow-up on [26. implement Object.assign()](https://bigfrontend.dev/problem/implement-object-assign).

`Object.assign()` assigns the enumerable properties, so getters are not copied, non-enumerable properties are ignored.

Suppose we have following source object.

```js
const source = Object.create(
  {
    a: 3, // prototype
  },
  {
    b: {
      value: 4,
      enumerable: true, // enumerable data descriptor
    },
    c: {
      value: 5, // non-enumerable data descriptor
    },
    d: {
      // non-enumerable accessor descriptor
      get: function () {
        return this.\_d;
      },
      set: function (value) {
        this.\_d = value;
      },
    },
    e: {
      // enumerable accessor descriptor
      get: function () {
        return this.\_e;
      },
      set: function (value) {
        this.\_e = value;
      },
      enumerable: true,
    },
  }
);
```

If we call `Object.assign()` with source of above, we get:

```js
Object.assign({}, source);

// {b: 4, e: undefined}
// e is undefined because `this._e` is undefined
```

Rather than above result, could you implement a `completeAssign()` which have the same parameters as `Object.assign()` but fully copies the data descriptors and accessor descriptors?

In case you are not familiar with the descriptors, [this page from MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) might help.

This problem is solely checking your understanding of how property descriptors work.

#

### Solution

```js
function completeAssign(target, ...sources) {
  if (!target) {
    throw new Error();
  }

  if (typeof target !== 'object') {
    const constructor = Object.getPrototypeOf(target).constructor;
    target = new constructor(target);
  }

  for (const source of sources) {
    if (!source) {
      continue;
    }

    const descriptors = Object.getOwnPropertyDescriptors(source);
    const keys = [
      ...Object.getOwnPropertyNames(source),
      ...Object.getOwnPropertySymbols(source),
    ];
    for (const key of keys) {
      Object.defineProperty(target, key, descriptors[key]);
    }
  }

  return target;
}
```



# 28.implement-clearAllTimeout.md

### Problem Description

`window.setTimeout()` could be used to schedule some task in the future.

Could you implement `clearAllTimeout()` to clear all the timers? This might be useful when we want to clear things up before page transition.

For example

```js
setTimeout(func1, 10000);
setTimeout(func2, 10000);
setTimeout(func3, 10000);
// all 3 functions are scheduled 10 seconds later

clearAllTimeout();
// all scheduled tasks are cancelled.
```

**note**

You need to keep the interface of `window.setTimeout` and `window.clearTimeout` the same, but you could replace them with new logic

#

### Solution

```js
const timerCache = new Set();
const originalSetTimeout = window.setTimeout;

window.setTimeout = (cb, delay) => {
  const timer = originalSetTimeout(cb, delay);
  timerCache.add(timer);
  return timer;
};

/**
 * cancel all timer from window.setTimeout
 */
function clearAllTimeout() {
  for (const timer of timerCache) {
    clearTimeout(timer);
  }
}
```



# 29.implement-async-helper-sequence.md

### Problem Description

This problem is similar to [11. what is Composition? create a pipe()](https://bigfrontend.dev/problem/what-is-composition-create-a-pipe).

You are asked to implement an async function helper, `sequence()` which chains up async functions, like what `pipe()` does.

All async functions have following interface

```ts
type Callback = (error: Error, data: any) => void;

type AsyncFunc = (callback: Callback, data: any) => void;
```

Your `sequence()` should **accept AsyncFunc array**, and **chain them up by passing new data to the next AsyncFunc through data in Callback.**

Suppose we have an async func which just multiple a number by 2

```js
const asyncTimes2 = (callback, num) => {
  setTimeout(() => callback(null, num \* 2), 100)
}
```

Your `sequence()` should be able to accomplish this

```js
const asyncTimes4 = sequence([asyncTimes2, asyncTimes2]);

asyncTimes4((error, data) => {
  console.log(data); // 4
}, 1);
```

Once an error occurs, it should trigger the last callback without triggering the uncalled functions.

**Follow up**

Can you solve it with and without Promise?

#

### Solution with Promise

```js
/*
type Callback = (error: Error, data: any) => void

type AsyncFunc = (
   finalCallback: Callback,
   data: any
) => void

*/

/**
 * @param {AsyncFunc[]} funcs
 * @return {(finalCallback: Callback) => void}
 */
function sequence(funcs) {
  const promisifiedFuncs = funcs.map((func) => promisify(func));

  return (finalCallback, input) => {
    const finalPromise = promisifiedFuncs.reduce((promise, promisifiedFunc) => {
      return promise.then((res) => promisifiedFunc(res));
    }, Promise.resolve(input));

    finalPromise
      .then((res) => {
        finalCallback(undefined, res);
      })
      .catch((err) => {
        finalCallback(err);
      });
  };
}

function promisify(func) {
  return (input) =>
    new Promise((resolve, reject) => {
      func((err, data) => {
        if (err) {
          reject(err);
        }

        resolve(data);
      }, input);
    });
}
```

#

### Solution without Promise

```js
/*
type Callback = (error: Error, data: any) => void

type AsyncFunc = (
   finalCallback: Callback,
   data: any
) => void

*/

/**
 * @param {AsyncFunc[]} funcs
 * @return {(finalCallback: Callback) => void}
 */
function sequence(funcs) {
  let i = 0;

  const sequenced = (finalCallback, input) => {
    if (i === funcs.length) {
      finalCallback(undefined, input);
      return;
    }

    const func = funcs[i];
    func((err, data) => {
      if (err) {
        finalCallback(err);
        return;
      }
      i++;
      sequenced(finalCallback, data);
    }, input);
  };
  return sequenced;
}
```



# 3.implement-Array.prototype.flat.md

### Problem Description

There is already `Array.prototype.flat()` in JavaScript (ES2019), which reduces the nesting of Array.


```js
const arr = [1, [2], [3, [4]]];

flat(arr);
// [1, 2, 3, [4]]

flat(arr, 1);
// [1, 2, 3, [4]]

flat(arr, 2);
// [1, 2, 3, 4]
```

### Recursive Solution

```js
/**
 * @param { Array } arr
 * @param { number } depth
 */
function flat(arr, depth = 1) {
  let flatArray = [];
  for (const item of arr) {
    if (Array.isArray(item) && depth > 0) {
      flatArray = flatArray.concat(flat(item, depth - 1));
    } else {
      flatArray.push(item);
    }
  }
  return flatArray;
}
```

#

### Recursive Solution with Reduce

```js
/**
 * @param { Array } arr
 * @param { number } depth
 */
function flat(arr, depth = 1) {
  return arr.reduce(
    (acc, item) =>
      Array.isArray(item) && depth > 0
        ? acc.concat(flat(item, depth - 1))
        : [...acc, item],
    []
  );
}
```

#

### Iterative Solution with Stack

```js
/**
 * @param { Array } arr
 * @param { number } depth
 */
function flat(arr, depth = 1) {
  const flatArray = [];
  let stack = [...arr.map((item) => [item, depth])];

  while (stack.length > 0) {
    const [item, depth] = stack.pop();
    if (Array.isArray(item) && depth > 0) {
      stack.push(...item.map((el) => [el, depth - 1]));
    } else {
      flatArray.push(item);
    }
  }

  return flatArray.reverse();
}
```



# 30.implement-async-helper-parallel.md

### Problem Description

This problem is related to [29. implement async helper - sequence()](https://bigfrontend.dev/problem/implement-async-helper-sequence).

You are asked to implement an async function helper, `parallel()` which works like `Promise.all()`. Different from `sequence()`, the async function doesn't wait for each other, rather they are all triggered together.

All async functions have following interface

```js
type Callback = (error: Error, data: any) => void;

type AsyncFunc = (callback: Callback, data: any) => void;
```

Your `parallel()` should **accept AsyncFunc array**, and return a new function which triggers its own callback when all async functions are done or an error occurs.

Suppose we have an 3 async functions

```js
const async1 = (callback) => {
  callback(undefined, 1);
};

const async2 = (callback) => {
  callback(undefined, 2);
};

const async3 = (callback) => {
  callback(undefined, 3);
};
```

Your `parallel()` should be able to accomplish this

```js
const all = parallel([async1, async2, async3]);

all((error, data) => {
  console.log(data); // [1, 2, 3]
}, 1);
```

When error occurs, only first error is passed down to the last. Later errors or data are ignored.

#

### Solution

```js
/*
type Callback = (error: Error, data: any) => void

type AsyncFunc = (
   callback: Callback,
   data: any
) => void

*/

/**
 * @param {AsyncFunc[]} funcs
 * @return {(callback: Callback) => void}
 */
function parallel(funcs) {
  return (finalCallback, input) => {
    const results = Array(funcs.length);
    let count = 0;
    let hasError = false;

    funcs.forEach((func, i) => {
      func((err, data) => {
        if (hasError) {
          return;
        }

        if (err) {
          hasError = true;
          finalCallback(err, undefined);
          return;
        }

        results[i] = data;
        count++;

        if (count === funcs.length) {
          finalCallback(undefined, results);
        }
      }, input);
    });
  };
}
```



# 31.implement-async-helper-race.md

### Problem Description

This problem is related to [30. implement async helper - parallel()](https://bigfrontend.dev/problem/implement-async-helper-parallel).

You are asked to implement an async function helper, `race()` which works like [Promise.race()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/race). Different from `parallel()` that waits for all functions to finish, `race()` will finish when any function is done or run into error.

All async functions have following interface

```ts
type Callback = (error: Error, data: any) => void;

type AsyncFunc = (callback: Callback, data: any) => void;
```

Your `race()` should **accept AsyncFunc array**, and return a new function which triggers its own callback when **any** async function is done or an error occurs.

Suppose we have an 3 async functions

```js
const async1 = (callback) => {
  setTimeout(() => callback(undefined, 1), 300);
};

const async2 = (callback) => {
  setTimeout(() => callback(undefined, 2), 100);
};

const async3 = (callback) => {
  setTimeout(() => callback(undefined, 3), 200);
};
```

Your `race()` should be able to accomplish this

```js
const first = race([async1, async2, async3]);

first((error, data) => {
  console.log(data); // 2, since 2 is the first to be given
}, 1);
```

When error occurs, only first error is passed down to the last. Later errors or data are ignored.

#

### Solution

```js
/*
type Callback = (error: Error, data: any) => void

type AsyncFunc = (
   callback: Callback,
   data: any
) => void

*/

/**
 * @param {AsyncFunc[]} funcs
 * @return {(callback: Callback) => void}
 */
function race(funcs) {
  return (finalCallback, input) => {
    let isExecuted = false;
    for (const func of funcs) {
      func((err, data) => {
        if (!isExecuted) {
          finalCallback(err, data);
          isExecuted = true;
        }
      }, input);
    }
  };
}
```



# 32.implement-Promise.all.md

### Problem Description

> The Promise.all() method takes an iterable of promises as an input, and returns a single Promise that resolves to an array of the results of the input promises

source - [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)

Could you write your own `all()` ? which should works the same as `Promise.all()`

#

### Solution

```js
/**
 * @param {Array<any>} promises - notice input might have non-Promises
 * @return {Promise<any[]>}
 */
function all(promises) {
  if (promises.length === 0) {
    return Promise.resolve([]);
  }

  const results = Array(promises.length);
  let count = 0;

  return new Promise((resolve, reject) => {
    promises.forEach((promise, i) => {
      if (!(promise instanceof Promise)) {
        promise = Promise.resolve(promise);
      }

      promise
        .then((res) => {
          results[i] = res;
          count++;

          if (count === promises.length) {
            resolve(results);
          }
        })
        .catch((err) => {
          reject(err);
        });
    });
  });
}
```



# 33.implement-Promise.allSettled.md

### Problem Description

> The Promise.allSettled() method returns a promise that resolves after all of the given promises have either fulfilled or rejected, with an array of objects that each describes the outcome of each promise.

from [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/allSettled)

Different from `Promise.all()` which rejects right away once an error occurs, `Promise.allSettled()` waits for all promises to settle.

Now can you implement your own `allSettled()`?

#

### Solution

```js
/**
 * @param {Array<any>} promises - notice that input might contains non-promises
 * @return {Promise<Array<{status: 'fulfilled', value: any} | {status: 'rejected', reason: any}>>}
 */
function allSettled(promises) {
  if (promises.length === 0) {
    return Promise.resolve([]);
  }

  const results = Array(promises.length);
  let numOfSettledPromise = 0;

  return new Promise((resolve, reject) => {
    promises.forEach((promise, i) => {
      if (!(promise instanceof Promise)) {
        promise = Promise.resolve(promise);
      }

      promise.then(
        (value) => {
          results[i] = {
            status: 'fulfilled',
            value,
          };

          numOfSettledPromise++;
          if (numOfSettledPromise === promises.length) {
            resolve(results);
          }
        },
        (reason) => {
          results[i] = {
            status: 'rejected',
            reason,
          };

          numOfSettledPromise++;
          if (numOfSettledPromise === promises.length) {
            resolve(results);
          }
        }
      );
    });
  });
}
```



# 34.implement-Promise.any.md

### Problem Description

> Promise.any() takes an iterable of Promise objects and, as soon as one of the promises in the iterable fulfils, returns a single promise that resolves with the value from that promise

from [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/any)

Can you implement a `any()` to work the same as `Promise.any()`?

**note**

`AggregateError` is not supported in Chrome yet, but you can still use it in your code since we will add the Class into your code. Do something like following:

```js
new AggregateError('No Promise in Promise.any was resolved', errors);
```

#

### Solution

```js
/**
 * @param {Array<Promise>} promises
 * @return {Promise}
 */
function any(promises) {
  if (promises.length === 0) {
    return Promise.resolve();
  }

  let isFulfilled = false;
  const errors = Array(promises.length);
  let numOfErrors = 0;

  return new Promise((resolve, reject) => {
    promises.forEach((promise, i) => {
      if (!(promise instanceof Promise)) {
        promise = Promise.resolve(promise);
      }

      promise.then(
        (value) => {
          if (isFulfilled) {
            return;
          }

          resolve(value);
          isFulfilled = true;
        },
        (reason) => {
          errors[i] = reason;
          numOfErrors++;

          if (numOfErrors === promises.length) {
            reject(
              new AggregateError(
                'No Promise in Promise.any was resolved',
                errors
              )
            );
          }
        }
      );
    });
  });
}
```



# 35.implement-Promise.race.md

### Problem Description

This problem is similar to [31. implement async helper - race()](https://bigfrontend.dev/problem/implement-async-helper-race), but with Promise.

> The Promise.race() method returns a promise that fulfills or rejects as soon as one of the promises in an iterable fulfills or rejects, with the value or reason from that promise. [source: MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/race)

Can you create a `race()` which works the same as `Promise.race()`?

#

### Solution

```js
/**
 * @param {Array<Promise>} promises
 * @return {Promise}
 */
function race(promises) {
  return new Promise((resolve, reject) => {
    promises.forEach((promise) => {
      promise.then(
        (value) => {
          resolve(value);
        },
        (reason) => {
          reject(reason);
        }
      );
    });
  });
}
```



# 36.create-a-fake-timer-setTimeout.md

### Problem Description

`setTimeout` adds task in to a task queue to be handled later, the time actually is no accurate. ([Event Loop](https://javascript.info/event-loop)).

This is OK in general web application, but might be problematic in test.

For example, at [5. implement throttle() with leading & trailing option](https://bigfrontend.dev/problem/implement-throttle-with-leading-and-trailing-option) we need to test the timer with more accurate approach.

Could you implement your own `setTimeout()` and `clearTimeout()` to be sync? so that they have **accurate timing** for test. This is what [FakeTimes](https://github.com/sinonjs/fake-timers) are for.

By "accurate", it means **suppose all functions cost no time**, we start our function at time `0`, then `setTimeout(func1, 100)` would schedule `func1` exactly at `100`.

You need to replace `Date.now()` as well to provide the time.

```js
class FakeTimer {
  install() {
    // setTimeout(), clearTimeout(), and Date.now()
    // are replaced
  }

  uninstall() {
    // restore the original APIs
    // setTimeout(), clearTimeout() and Date.now()
  }

  tick() {
    // run all the schedule functions in order
  }
}
```

Your code is tested like this

```js
const fakeTimer = new FakeTimer();
fakeTimer.install();

const logs = [];
const log = (arg) => {
  logs.push([Date.now(), arg]);
};

setTimeout(() => log('A'), 100);
// log 'A' at 100

const b = setTimeout(() => log('B'), 110);
clearTimeout(b);
// b is set but cleared

setTimeout(() => log('C'), 200);

expect(logs).toEqual([
  [100, 'A'],
  [200, 'C'],
]);

fakeTimer.uninstall();
```

**Note**

Only `Date.now()` is used when judging your code, you can ignore other time related apis.

#

### Solution

```js
class FakeTimer {
  constructor() {
    this.taskQueue = [];
    this.currTime = 0;
  }

  install() {
    // replace window.setTimeout, window.clearTimeout, Date.now
    // with your implementation
    this.windowSetTimeout = window.setTimeout;
    this.windowClearTimeout = window.clearTimeout;
    this.dateNow = Date.now;

    window.setTimeout = (cb, wait) => {
      // accumulate the time for nested setTimeout calls.
      wait += this.currTime;

      const task = {
        id: this.taskQueue.length - 1,
        cb,
        wait,
      };
      this.taskQueue.push(task);
      this.taskQueue.sort((taskA, taskB) => taskB.wait - taskA.wait);

      return task.id;
    };

    window.clearTimeout = (id) => {
      const taskIndex = this.taskQueue.findIndex((task) => task.id === id);
      this.taskQueue.splice(taskIndex, 1);
    };

    Date.now = () => this.currTime;
  }

  uninstall() {
    // restore the original implementation of
    // window.setTimeout, window.clearTimeout, Date.now
    window.setTimeout = this.windowSetTimeout;
    window.clearTimeout = this.windowClearTimeout;
    Date.now = this.dateNow;
  }

  tick() {
    // run the scheduled functions without waiting
    while (this.taskQueue.length > 0) {
      const { cb, wait } = this.taskQueue.pop();
      this.currTime = wait;
      cb();
    }
  }
}
```



# 37.implement-Binary-Search-unique.md

### Problem Description

Even in Front-End review, basic algorithm technique like [Binary Search](https://en.wikipedia.org/wiki/Binary_search_algorithm) are likely to be asked.

You are given an **unique & ascending** array of integers, please search for its index with Binary Search.

If not found, return `-1`

#

### Iterative Solution

```js
/**
 * @param {number[]} arr - ascending unique array
 * @param {number} target
 * @return {number}
 */
function binarySearch(arr, target) {
  let start = 0;
  let end = arr.length - 1;

  while (start <= end) {
    const mid = Math.floor((start + end) / 2);

    if (target === arr[mid]) {
      return mid;
    }

    if (target < arr[mid]) {
      end = mid - 1;
    } else {
      start = mid + 1;
    }
  }

  return -1;
}
```

#

### Recursive Solution

```js
/**
 * @param {number[]} arr - ascending unique array
 * @param {number} target
 * @return {number}
 */
function binarySearch(arr, target) {
  return recursiveBinarySearch(arr, target, 0, arr.length - 1);
}

function recursiveBinarySearch(arr, target, start, end) {
  if (start > end) {
    return -1;
  }

  const mid = Math.floor((start + end) / 2);

  if (arr[mid] === target) {
    return mid;
  }

  if (arr[mid] > target) {
    return recursiveBinarySearch(arr, target, start, mid - 1);
  }

  return recursiveBinarySearch(arr, target, mid + 1, end);
}
```



# 38.implement-jest.spyOn.md

### Problem Description

If you did unit test before, you must be familiar with `Spy`.

You are asked to create a `spyOn(object, methodName)`, which works the same as [jest.spyOn()](https://jestjs.io/docs/en/jest-object#jestspyonobject-methodname).

To make it simple, here are the 2 requirements of `spyOn`

1. original method should be called when spied one is called
2. spy should have a `calls` array, which holds all the arguments in each call.

Code to explain everything.

```js
const obj = {
  data: 1,
  increment(num) {
    this.data += num;
  },
};

const spy = spyOn(obj, 'increment');

obj.increment(1);

console.log(obj.data); // 2

obj.increment(2);

console.log(obj.data); // 4

console.log(spy.calls);
// [ [1], [2] ]
```

#

### Solution

```js
/**
 * @param {object} obj
 * @param {string} methodName
 */
function spyOn(obj, methodName) {
  const spy = {
    calls: [],
  };

  const originalMethod = obj[methodName];
  if (!originalMethod || typeof originalMethod !== 'function') {
    throw new Error();
  }

  obj[methodName] = (...args) => {
    spy.calls.push(args);
    originalMethod.apply(obj, args);
  };

  return spy;
}
```



# 39.implement-range.md

### Problem Description

Can you create a `range(from, to)` which makes following work?

```js
for (let num of range(1, 4)) {
  console.log(num);
}
// 1
// 2
// 3
// 4
```

This is a simple one, could you think **more fancy approaches other than for-loop**?

Notice that you are not required to return an array, but something iterable would be fine.

#

### Solution

```js
/**
 * @param {integer} from
 * @param {integer} to
 */
function range(from, to) {
  // The iterator object that will be returned
  // when calling the Symbol.iterator method of an object.
  // The iterator object has a method named next which
  // generates values for the iteration.
  const iterator = {
    from: from,
    to: to,
    next() {
      if (this.from > this.to) {
        return { done: true };
      }

      const value = { value: this.from, done: false };
      this.from++;
      return value;
    },
  };

  // Return an object that has a method named
  // Symbol.iterator for the for...of to work.
  // When for..of starts, it calls that method once.
  return {
    [Symbol.iterator]() {
      return iterator;
    },
  };
}
```

#

### Reference

[Iterables](https://javascript.info/iterable)



# 4.implement-basic-throttle.md




# 40.implement-Bubble-Sort.md

### Problem Description

Even for Front-End Engineer, it is a must to understand how basic sorting algorithms work.

Now you are asked to implement [Bubble Sort](https://en.wikipedia.org/wiki/Bubble_sort), which sorts an integer array in ascending order.

Do it **in-place**, no need to return anything.

**Follow-up**

What is time cost for average / worst case ? Is it stable?

#

### Solution

```js
/**
 * @param {number[]} arr
 */
function bubbleSort(arr) {
  let hasNoSwaps;
  for (let i = arr.length; i >= 0; i--) {
    hasNoSwaps = true;
    for (let j = 0; j < i - 1; j++) {
      if (arr[j] > arr[j + 1]) {
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
        hasNoSwaps = false;
      }
    }
    if (hasNoSwaps) {
      break;
    }
  }
}
```



# 41.implement-Merge-Sort.md

### Problem Description

Even for Front-End Engineer, it is a must to understand how basic sorting algorithms work.

Now you are asked to implement [Merge Sort](https://en.wikipedia.org/wiki/Merge_sort), which sorts an integer array in ascending order.

Do it **in-place**, no need to return anything.

**Follow-up**

What is time cost for average / worst case ? Is it stable?

#

### Solution

```js
/**
 * @param {number[]} arr
 */
function mergeSort(arr) {
  mergeSortImpl(arr, 0, arr.length - 1);
}

function mergeSortImpl(arr, start, end) {
  if (start >= end) {
    return;
  }

  const mid = Math.floor((start + end) / 2);
  mergeSortImpl(arr, start, mid);
  mergeSortImpl(arr, mid + 1, end);
  merge(arr, start, mid, end);
}

function merge(arr, start, mid, end) {
  const lowHalf = [];
  const highHalf = [];

  let k = start;
  let i;
  let j;
  for (i = 0; k <= mid; i++, k++) {
    lowHalf[i] = arr[k];
  }

  for (j = 0; k <= end; j++, k++) {
    highHalf[j] = arr[k];
  }

  k = start;
  i = 0;
  j = 0;

  while (i < lowHalf.length && j < highHalf.length) {
    if (lowHalf[i] < highHalf[j]) {
      arr[k] = lowHalf[i];
      i++;
    } else {
      arr[k] = highHalf[j];
      j++;
    }
    k++;
  }

  while (i < lowHalf.length) {
    arr[k] = lowHalf[i];
    i++;
    k++;
  }

  while (j < highHalf.length) {
    arr[k] = highHalf[j];
    j++;
    k++;
  }
}
```



# 42.implement-Insertion-Sort.md

### Problem Description

Even for Front-End Engineer, it is a must to understand how basic sorting algorithms work.

Now you are asked to implement [Insertion Sort](https://en.wikipedia.org/wiki/Insertion_sort), which sorts an integer array in ascending order.

Do it **in-place**, no need to return anything.

**Follow-up**

What is time cost for average / worst case ? Is it stable?

#

### Solution

```js
/**
 * @param {number[]} arr
 */
function insertionSort(arr) {
  for (let i = 1; i < arr.length; i++) {
    let currentVal = arr[i];
    let j = i - 1;
    while (j >= 0 && currentVal < arr[j]) {
      arr[j + 1] = arr[j];
      j--;
    }
    arr[j + 1] = currentVal;
  }
}
```



# 43.implement-Quick-Sort.md

### Problem Description

Even for Front-End Engineer, it is a must to understand how basic sorting algorithms work.

Now you are asked to implement [Quick Sort](https://en.wikipedia.org/wiki/Quicksort), which sorts an integer array in ascending order.

Do it **in-place**, no need to return anything.

**Follow-up**

What is time cost for average / worst case ? Is it stable?

#

### Solution

```js
/**
 * @param {number[]} arr
 */
function quickSort(arr) {
  quickSortImpl(arr, 0, arr.length - 1);
}

function quickSortImpl(arr, low, high) {
  if (low >= high) {
    return;
  }

  const pivotIndex = partition(arr, low, high);
  quickSortImpl(arr, low, pivotIndex - 1);
  quickSortImpl(arr, pivotIndex + 1, high);
}

function partition(arr, low, high) {
  const pivot = arr[high];
  let i = low;
  for (let j = low; j < high; j++) {
    if (arr[j] < pivot) {
      [arr[j], arr[i]] = [arr[i], arr[j]];
      i++;
    }
  }

  [arr[i], arr[high]] = [arr[high], arr[i]];
  return i;
}
```



# 44.implement-Selection-Sort.md

### Problem Description

Even for Front-End Engineer, it is a must to understand how basic sorting algorithms work.

Now you are asked to implement [Selection sort](https://en.wikipedia.org/wiki/Selection_sort), which sorts an integer array in ascending order.

Do it **in-place**, no need to return anything.

**Follow-up**

What is time cost for average / worst case ? Is it stable?

#

### Solution

```js
/**
 * @param {number[]} arr
 */
function selectionSort(arr) {
  for (let i = 0; i < arr.length; i++) {
    let smallestIndex = i;
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[j] < arr[smallestIndex]) {
        smallestIndex = j;
      }
    }
    if (smallestIndex !== i) {
      [arr[smallestIndex], arr[i]] = [arr[i], arr[smallestIndex]];
    }
  }
}
```



# 45.find-the-K-th-largest-element-in-an-unsorted-array.md

### Problem Description

You are given an unsorted array of numbers, which might have duplicates, find the K-th largest element.

The naive approach would be sort it first, but it costs `O(nlogn)`, could you find a better approach?

Maybe you can recall what is happening in [Quick Sort](https://bigfrontend.dev/problem/implement-Quick-Sort) or [Priority Queue](https://bigfrontend.dev/problem/create-a-priority-queue-in-JavaScript)

#

### Solution with Max Heap (O(n + kLogn))

```js
class MaxHeap {
  constructor() {
    this.heap = [];
  }

  add(element) {
    this.heap.push(element);

    const heapSize = this.size();
    if (heapSize === 1) {
      return;
    }

    this.heapifyUp(heapSize - 1);
  }

  extractMax() {
    const heapSize = this.size();
    if (heapSize <= 1) {
      return this.heap.pop();
    }

    this.swap(0, heapSize - 1);
    const head = this.heap.pop();
    this.heapifyDown(0);
    return head;
  }

  size() {
    return this.heap.length;
  }

  heapifyUp(index) {
    const parentIndex = Math.floor((index - 1) / 2);

    if (parentIndex < 0) {
      return;
    }

    if (this.heap[index] > this.heap[parentIndex]) {
      this.swap(index, parentIndex);
      this.heapifyUp(parentIndex);
    }
  }

  heapifyDown(parentIndex) {
    const leftIndex = parentIndex * 2 + 1;
    const rightIndex = parentIndex * 2 + 2;
    let largestIndex = parentIndex;
    const heapSize = this.size();

    if (
      leftIndex < heapSize &&
      this.heap[leftIndex] > this.heap[largestIndex]
    ) {
      largestIndex = leftIndex;
    }

    if (
      rightIndex < heapSize &&
      this.heap[rightIndex] > this.heap[largestIndex]
    ) {
      largestIndex = rightIndex;
    }

    if (largestIndex !== parentIndex) {
      this.swap(largestIndex, parentIndex);
      this.heapifyDown(largestIndex);
    }
  }

  swap(i, j) {
    [this.heap[i], this.heap[j]] = [this.heap[j], this.heap[i]];
  }
}

/**
 * @param {number[]} arr
 * @param {number} k
 */
function findKThLargest(arr, k) {
  const maxHeap = new MaxHeap();

  for (const el of arr) {
    maxHeap.add(el);
  }

  const largest = [];
  for (let i = 0; i < k; i++) {
    largest.push(maxHeap.extractMax());
  }

  return largest[largest.length - 1];
}
```

#

### Solution with randomized Quick Select (expected O(n))

```js
/**
 * @param {number[]} arr
 * @param {number} k
 */
function findKThLargest(arr, k) {
  return quickSelect(arr, 0, arr.length - 1, k - 1);
}

function quickSelect(arr, lo, hi, k) {
  if (lo >= hi) {
    return arr[lo];
  }

  const pivotIndex = randomizedPartition(arr, lo, hi);
  if (pivotIndex === k) {
    return arr[pivotIndex];
  }
  if (pivotIndex < k) {
    return quickSelect(arr, pivotIndex + 1, hi, k);
  }

  return quickSelect(arr, lo, pivotIndex - 1, k);
}

function randomizedPartition(arr, lo, hi) {
  const index = Math.floor(Math.random() * (hi - lo + 1)) + lo;
  [arr[index], arr[hi]] = [arr[hi], arr[index]];
  return partition(arr, lo, hi);
}

function partition(arr, lo, hi) {
  const pivot = arr[hi];
  let i = lo;
  for (let j = lo; j < hi; j++) {
    if (arr[j] > pivot) {
      [arr[i], arr[j]] = [arr[j], arr[i]];
      i++;
    }
  }
  [arr[i], arr[hi]] = [arr[hi], arr[i]];
  return i;
}
```



# 46.implement-once.md

### Problem Description

[\_.once(func)](https://lodash.com/docs/4.17.15#once) is used to force a function to be called only once, later calls only returns the result of first call.

Can you implement your own `once()`?

```js
function func(num) {
  return num;
}

const onced = once(func);

onced(1);
// 1, func called with 1

onced(2);
// 1, even 2 is passed, previous result is returned
```

#

### Solution

```js
/**
 * @param {Function} func
 * @return {Function}
 */
function once(func) {
  let result;
  let isExecuted = false;
  return function (...args) {
    if (!isExecuted) {
      result = func.apply(this, args);
      isExecuted = true;
    }
    return result;
  };
}
```



# 47.reverse-a-linked-list.md

### Problem Description

Another basic algorithm even for Front End developers.

You are asked to **reverse a linked list**.

Suppose we have Node interface like this

```js
class Node {
  new(val: number, next: Node);
  val: number
  next: Node
}
```

We can then chain nodes together to create a linked list.

```js
const Three = new Node(3, null);
const Two = new Node(2, Three);
const One = new Node(1, Two);

//now we have a linked list
// 1 → 2 → 3
```

Now how can you reverse it to 3 → 2 → 1 ? you can modify the `next` property of each node, but not the `val`.

**Follow up**

Could you solve it with and without recursion?

#

### Iterative Solution

```js
/**
 * class Node {
 *  new(val: number, next: Node);
 *    val: number
 *    next: Node
 * }
 */

/**
 * @param {Node} head
 * @return {Node}
 */
const reverseLinkedList = (head) => {
  let prevNode = null;
  let currNode = head;

  while (currNode) {
    const nextNode = currNode.next;
    currNode.next = prevNode;
    prevNode = currNode;
    currNode = nextNode;
  }

  return prevNode;
};
```

#

### Recursive Solution

```js
/**
 * class Node {
 *  new(val: number, next: Node);
 *    val: number
 *    next: Node
 * }
 */

/**
 * @param {Node} node
 * @return {Node}
 */
const reverseLinkedList = (node) => {
  if (!node || !node.next) {
    return node;
  }

  const rest = node.next;
  const newHead = reverseLinkedList(rest);
  rest.next = node;
  node.next = null;
  return newHead;
};
```



# 48.search-first-index-with-Binary-Search-duplicate-array.md

### Problem Description

This is a variation of [37. implement Binary Search (unique)](https://bigfrontend.dev/problem/implement-Binary-Search-Unique).

Your are given a sorted ascending array of number, but **might have duplicates**, you are asked to return the **first index** of a target number.

If not found return -1.

#

### Solution

```js
/**
 * @param {number[]} arr - ascending array with duplicates
 * @param {number} target
 * @return {number}
 */
function firstIndex(arr, target) {
  let startIndex = 0;
  let endIndex = arr.length - 1;
  let index = -1;

  while (startIndex <= endIndex) {
    const midIndex = Math.floor((startIndex + endIndex) / 2);
    if (target > arr[midIndex]) {
      startIndex = midIndex + 1;
      continue;
    }

    if (arr[midIndex] === target) {
      index = midIndex;
    }

    if (arr[midIndex] === target && arr[midIndex - 1] !== target) {
      break;
    }

    endIndex = midIndex - 1;
  }

  return index;
}
```



# 49.search-last-index-with-Binary-Search-duplicate-array.md

### Problem Description

This is a variation of [37. implement Binary Search (unique)](https://bigfrontend.dev/problem/implement-Binary-Search-Unique).

Your are given a sorted ascending array of number, but **might have duplicates**, you are asked to return the **last index** of a target number.

If not found return -1.

#

### Solution

```js
/**
 * @param {number[]} arr - ascending array with duplicates
 * @param {number} target
 * @return {number}
 */
function lastIndex(arr, target) {
  let startIndex = 0;
  let endIndex = arr.length - 1;
  let index = -1;

  while (startIndex <= endIndex) {
    const midIndex = Math.floor((startIndex + endIndex) / 2);

    if (target < arr[midIndex]) {
      endIndex = midIndex - 1;
      continue;
    }

    if (arr[midIndex] === target) {
      index = midIndex;
    }

    if (arr[midIndex] === target && arr[midIndex + 1] !== target) {
      break;
    }

    startIndex = midIndex + 1;
  }

  return index;
}
```



# 5.implement-throttle-with-leading-and-trailing-option.md




# 50.search-element-right-before-target-with-Binary-Search-duplicate-array.md

### Problem Description

This is a variation of [37. implement Binary Search (unique)](https://bigfrontend.dev/problem/implement-Binary-Search-Unique).

You are given a sorted ascending array of number, but **might have duplicates**, you are asked to return the **element right before first appearance** of a target number.

If not found return `undefined`.

#

### Solution

```js
/**
 * @param {number[]} arr - ascending array with duplicates
 * @param {number} target
 * @return {number}
 */
function elementBefore(arr, target) {
  let startIndex = 0;
  let endIndex = arr.length - 1;

  while (startIndex <= endIndex) {
    const midIndex = Math.floor((startIndex + endIndex) / 2);
    if (arr[midIndex] === target && arr[midIndex - 1] !== target) {
      return arr[midIndex - 1];
    }

    if (target > arr[midIndex]) {
      startIndex = midIndex + 1;
    } else {
      endIndex = midIndex - 1;
    }
  }

  return undefined;
}
```



# 51.search-element-right-after-target-with-Binary-Search-duplicate-array.md

### Problem Description

This is a variation of [37. implement Binary Search (unique)](https://bigfrontend.dev/problem/implement-Binary-Search-Unique).

Your are given a sorted ascending array of number, but **might have duplicates**, you are asked to return the **element right after last appearance** of a target number.

If not found return `undefined`.

### Solution

```js
/**
 * @param {number[]} arr - ascending array with duplicates
 * @param {number} target
 * @return {number}
 */
function elementAfter(arr, target) {
  let startIndex = 0;
  let endIndex = arr.length - 1;

  while (startIndex <= endIndex) {
    const midIndex = Math.floor((startIndex + endIndex) / 2);

    if (arr[midIndex] === target && arr[midIndex + 1] !== target) {
      return arr[midIndex + 1];
    }

    if (target < arr[midIndex]) {
      endIndex = midIndex - 1;
    } else {
      startIndex = midIndex + 1;
    }
  }

  return undefined;
}
```



# 52.create-a-middleware-system.md

### Problem Description

Have you ever used [Express Middleware](http://expressjs.com/en/guide/using-middleware.html#using-middleware) before?

Middleware functions are functions with fixed interface that could be chained up like following two functions.

```js
app.use(
  '/user/:id',
  function (req, res, next) {
    next();
  },
  function (req, res, next) {
    next(new Error('sth wrong'));
  }
);
```

You are asked to create simplified Middleware system:

```js
type Request = object;

type NextFunc = (error?: any) => void;

type MiddlewareFunc = (req: Request, next: NextFunc) => void;

type ErrorHandler = (error: Error, req: Request, next: NextFunc) => void;

class Middleware {
  use(func: MiddlewareFunc | ErrorHandler) {
    // do any async operations
    // call next() to trigger next function
  }
  start(req: Request) {
    // trigger all functions with a req object
  }
}
```

Now we can do something similar with Express

```js
const middleware = new Middleware();

middleware.use((req, next) => {
  req.a = 1;
  next();
});

middleware.use((req, next) => {
  req.b = 2;
  next();
});

middleware.use((req, next) => {
  console.log(req);
});

middleware.start({});
// {a: 1, b: 2}
```

Notice that `use()` could also accept an ErrorHandler which has 3 arguments. The error handler is triggered if `next()` is called with an extra argument or uncaught error happens, like following.

```js
const middleware = new Middleware();

// throw an error at first function
middleware.use((req, next) => {
  req.a = 1;
  throw new Error('sth wrong');
  // or `next(new Error('sth wrong'))`
});

// since error occurs, this is skipped
middleware.use((req, next) => {
  req.b = 2;
});

// since error occurs, this is skipped
middleware.use((req, next) => {
  console.log(req);
});

// since error occurs, this is called
middleware.use((error, req, next) => {
  console.log(error);
  console.log(req);
});

middleware.start({});
// Error: sth wrong
// {a: 1}
```

#

### Solution

```js
class Middleware {
  constructor() {
    this.middlewareFuncs = [];
    this.middlewareFuncIndex = 0;
    this.errorHandlers = [];
    this.errorHandlerIndex = 0;
    this.next = this.next.bind(this);
  }

  /**
   * @param {MiddlewareFunc} func
   */
  use(func) {
    if (func.length === 3) {
      this.errorHandlers.push(func);
    } else {
      this.middlewareFuncs.push(func);
    }
  }

  /**
   * @param {Request} req
   */
  start(req) {
    this.req = req;
    this.next();
  }

  next(error) {
    try {
      let func;
      if (error) {
        func = this.errorHandlers[this.errorHandlerIndex++];
        func(error, this.req, this.next);
      } else {
        func = this.middlewareFuncs[this.middlewareFuncIndex++];
        func(this.req, this.next);
      }
    } catch (err) {
      const errorHandler = this.errorHandlers[this.errorHandlerIndex++];
      errorHandler(err, this.req, this.next);
    }
  }
}
```



# 53.write-your-own-extends-in-es5.md

### Problem Description

I believe you've used [extends](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/extends) keyword in you JavaScript programs before.

Could you implement a `myExtends()` function in ES5 to mimic the behavior of `extends`?

`myExtends()` takes a SubType and SuperType, and return a new type.

```js
const InheritedSubType = myExtends(SuperType, SubType);

const instance = new InheritedSubType();

// above should work (almost) the same as follows

class SubType extends SuperType {}
const instance = new SubType();
```

To solve this problem, you need to fully understand what is [Inheritance](https://javascript.info/class-inheritance)

**note**

Your code will be test against following SuperType and SubType

```js
function SuperType(name) {
  this.name = name;
  this.forSuper = [1, 2];
  this.from = 'super';
}
SuperType.prototype.superMethod = function () {};
SuperType.prototype.method = function () {};
SuperType.staticSuper = 'staticSuper';

function SubType(name) {
  this.name = name;
  this.forSub = [3, 4];
  this.from = 'sub';
}

SubType.prototype.subMethod = function () {};
SubType.prototype.method = function () {};
SubType.staticSub = 'staticSub';
```

#

### ES3 Solution

```js
const myExtends = (SuperType, SubType) => {
  const constructor = function (...args) {
    // Call the original Constructors with the constructor
    // instance as their context
    SuperType.apply(this, args);
    SubType.apply(this, args);

    // instance prototype chain
    this.__proto__ = SubType.prototype;
  };

  // constructor prototype chain
  SubType.prototype.__proto__ = SuperType.prototype;
  constructor.prototype.__proto__ = SubType.prototype;

  // Inherit static properties
  constructor.__proto__ = SuperType;

  return constructor;
};
```

#

### ES5 Solution

```js
const myExtends = (SuperType, SubType) => {
  const constructor = function (...args) {
    // Call the original Constructors with the constructor
    // instance as their context
    SuperType.apply(this, args);
    SubType.apply(this, args);
    Object.setPrototypeOf(this, SubType.prototype);
  };

  Object.setPrototypeOf(SubType.prototype, SuperType.prototype);
  Object.setPrototypeOf(constructor.prototype, SubType.prototype);

  // Inherit static properties
  Object.setPrototypeOf(constructor, SuperType);
  return constructor;
};
```

#

### Reference

[Problem Discuss](https://bigfrontend.dev/problem/53/discuss/1669)



# 54.flatten-Thunk.md

### Problem Description

Suppose we have a Callback type

```js
type Callback = (error: Error, result: any | Thunk) => void;
```

A Thunk is a function that take a Callback as parameter

```js
type Thunk = (callback: Callback) => void;
```

Like following three thunks

```js
const func1 = (cb) => {
  setTimeout(() => cb(null, 'ok'), 10);
};

const func2 = (cb) => {
  setTimeout(() => cb(null, func1), 10);
};

const func3 = (cb) => {
  setTimeout(() => cb(null, func2), 10);
};
```

in above example, three functions are kind of chained up, func3 → func2 → func1, but it don't work without some glue.

OK, now you are asked to implement a `flattenThunk()` which glue them up and returns a new thunk.

```js
flattenThunk(func3)((error, data) => {
  console.log(data); // 'ok'
});
```

**note**

Once error occurs, the rest uncalled functions should be skipped

#

### Solution

```js
/**
 * @param {Thunk} thunk
 * @return {Thunk}
 */
function flattenThunk(thunk) {
  let finalCallback;

  const callback = (error, result) => {
    if (error) {
      finalCallback(error, undefined);
      return;
    }

    if (typeof result === 'function') {
      result(callback);
      return;
    }

    finalCallback(undefined, result);
  };

  return (finalCb) => {
    finalCallback = finalCb;
    thunk(callback);
  };
}
```



# 55.highlight-keywords-in-HTML-string.md

### Problem Description

Suppose you are implementing an auto-complete in search input.

When keywords are typed, you need to **highlight the keywords**, how would you do that?

To simplify things, you need to create a function `highlightKeywords(html:string, keywords: string[])`, which wraps the keywords in html string, with `<em>` tag.

Here is an example.

```js
highlightKeywords('Hello FrontEnd Lovers', ['Hello', 'Front', 'JavaScript']);
// '<em>Hello</em> <em>Front</em>End Lovers'
```

Pay attention to the overlapping and adjacent case. You should use the least tags as possible.

```js
highlightKeywords('Hello FrontEnd Lovers', ['Front', 'End', 'JavaScript']);
// 'Hello <em>FrontEnd</em> Lovers'

highlightKeywords('Hello FrontEnd Lovers', ['Front', 'FrontEnd', 'JavaScript']);
// 'Hello <em>FrontEnd</em> Lovers'
```

note that `space` should not be included.

#

### Solution

```js
class TrieNode {
  constructor() {
    this.children = new Map();
    this.isEndOfWord = false;
  }
}

class Trie {
  constructor() {
    this.root = new TrieNode();
  }

  insert(word) {
    let currentNode = this.root;
    for (const char of word) {
      if (!currentNode.children.has(char)) {
        currentNode.children.set(char, new TrieNode());
      }
      currentNode = currentNode.children.get(char);
    }

    currentNode.isEndOfWord = true;
  }
}

/**
 * @param {string} html
 * @param {string[]} keywords
 */
function highlightKeywords(html, keywords) {
  const trie = new Trie();
  keywords.forEach((keyword) => {
    trie.insert(keyword);
  });

  let node = trie.root;
  let containedString = '';
  let highlightedString = '';
  for (const char of html) {
    if (!node.children.has(char) && !containedString) {
      highlightedString += char;
      continue;
    }

    if (node.children.has(char)) {
      containedString += char;
      node = node.children.get(char);
      if (node.isEndOfWord && node.children.size === 0) {
        node = trie.root;
      }
      continue;
    }

    highlightedString += `<em>${containedString}</em>${char}`;
    containedString = '';
  }

  if (containedString) {
    highlightedString += `<em>${containedString}</em>`;
  }

  return highlightedString;
}
```



# 56.call-APIs-with-pagination.md

### Problem Description

Have you ever met some APIs with pagination, and needed to recursively fetch them based on response of previous request ?

Suppose we have a `/list` API, which returns an array `items`.

```js
// fetchList is provided for you
const fetchList = (since?: number) =>
  Promise<{items: Array<{id: number}>}>
```

1. for initial request, we just fetch `fetchList`. and get the last item id from response.
2. for next page, we need to call `fetchList(lastItemId)`.
3. repeat above process.

The `/list` API only gives us 5 items at a time, with server-side filtering, it might be less than 5. But if none returned, it means nothing to fetch any more and we should stop.

You are asked to create a function that could return arbitrary amount of items.

```js
const fetchListWithAmount = (amount: number = 5) { }
```

**note**

You can achieve this by regular loop, even fancier solutions with [async iterators or async generators](https://javascript.info/async-iterators-generators). You should try them all.

#

### Solution

```js
// fetchList is provided for you
// const fetchList = (since?: number) =>
//   Promise<{items: Array<{id: number}>}>

// you can change this to generator function if you want
const fetchListWithAmount = async (amount = 5) => {
  const items = [];
  let lastItemId = null;

  while (items.length <= amount) {
    const response = lastItemId
      ? await fetchList(lastItemId)
      : await fetchList();
    if (!response || !response.items.length) {
      break;
    }
    items.push(...response.items);
    lastItemId = items[items.length - 1].id;
  }

  return items.slice(0, amount);
};
```

#

### Solution with async iterators

```js
// fetchList is provided for you
// const fetchList = (since?: number) =>
//   Promise<{items: Array<{id: number}>}>

// you can change this to generator function if you want
const fetchListWithAmount = async (amount = 5) => {
  const items = [];

  for await (const newItems of fetchPaginated(amount)) {
    items.push(...newItems);
  }

  return items.slice(0, amount);
};

function fetchPaginated(amount) {
  const iterator = {
    amount,
    itemsAmount: 0,
    lastItemId: null,
    async next() {
      if (this.itemsAmount > this.amount) {
        return { done: true };
      }

      const response = this.lastItemId
        ? await fetchList(this.lastItemId)
        : await fetchList();
      if (!response || !response.items.length) {
        return { done: true };
      }

      const result = response.items;
      this.itemsAmount += result.length;
      this.lastItemId = result[result.length - 1].id;

      return {
        done: false,
        value: result,
      };
    },
  };

  return {
    [Symbol.asyncIterator]() {
      return iterator;
    },
  };
}
```

#

### Solution with async generators

```js
// fetchList is provided for you
// const fetchList = (since?: number) =>
//   Promise<{items: Array<{id: number}>}>

// you can change this to generator function if you want
const fetchListWithAmount = async (amount = 5) => {
  const items = [];

  for await (const newItems of fetchPaginated(amount)) {
    items.push(...newItems);
  }

  return items.slice(0, amount);
};

async function* fetchPaginated(amount) {
  let itemAmount = 0;
  let lastItemId = null;
  while (itemAmount <= amount) {
    const response = lastItemId
      ? await fetchList(lastItemId)
      : await fetchList();
    if (!response || !response.items.length) {
      break;
    }
    const items = response.items;
    itemAmount += items.length;
    lastItemId = items[items.length - 1].id;
    yield items;
  }
}
```



# 57.create-an-Observable.md

### Problem Description

Have you ever used [RxJS](https://rxjs-dev.firebaseapp.com/guide/overview) before? The most important concept in it is [Observable](https://rxjs-dev.firebaseapp.com/guide/observable) and [Observer](https://rxjs-dev.firebaseapp.com/guide/observer).

Observable defines how values are delivered to Observer. Observer is just a set of callbacks.

Let's look at the code.

```js
const observer = {
  next: (value) => {
    console.log('we got a value', value);
  },
  error: (error) => {
    console.log('we got an error', error);
  },
  complete: () => {
    console.log('ok, no more values');
  },
};
```

Above is an Observer which is pretty clear about what it is doing.

Then we could attach this Observer to some Observable. Observable feeds this observer with values or errors.

```js
const observable = new Observable((subscriber) => {
  subscriber.next(1);
  subscriber.next(2);
  setTimeout(() => {
    subscriber.next(3);
    subscriber.next(4);
    subscriber.complete();
  }, 100);
});
```

The code plainly says when is a subscriber is attached,

1. subscriber is fed with a value `1`
2. subscriber is then fed with a value `2`
3. wait 100 ms
4. subscriber is fed with `3`
5. subscriber is fed with `4`
6. no more values for subscriber

Now if we attach above `observer` to `observable`, `next` and `complete` of subscriber are called in order.(be aware that there is a delay between 2 and 3)

```js
const sub = observable.subscribe(subscriber);
// we got a value 1
// we got a value 2

// we got a value 3
// we got a value 4
// ok, no more values
```

Notice `subscribe()` return a [Subscription](https://rxjs-dev.firebaseapp.com/guide/subscription) which could be used to stop listening to the value delivery.

```js
const sub = observable.subscribe(subscriber);
setTimeout(() => {
  // ok we only subscribe for 100ms
  sub.unsubscribe();
}, 100);
```

So this is the basic idea of Observable and Observer. There will be a few more interesting follow-up problems.

**Now you are asked to implement a basic Observable class**, which makes above possible.

Some extra requirements are listed here.

1. `error` and `complete` can only be delivered once, `next/error/complete` after `error/complete` should not work
2. for a subscriber object, `next/error/complete` callback are all optional. if a function is passed as observer, it is treated as `next`.
3. should support multiple subscription

_Further Reading_

https://github.com/tc39/proposal-observable

#

### Solution

```js
class Observable {
  constructor(setup) {
    this.setup = setup;
  }

  subscribe(subscriber) {
    const originalNext =
      typeof subscriber === 'function' ? subscriber : subscriber.next;
    const originalError = subscriber.error;
    const originalComplete = subscriber.complete;

    let isUnsubscribed = false;

    subscriber.next = (value) => {
      if (isUnsubscribed) {
        return;
      }

      if (typeof originalNext === 'function') {
        originalNext(value);
      }
    };

    subscriber.error = (error) => {
      if (isUnsubscribed) {
        return;
      }

      isUnsubscribed = true;
      if (typeof originalError === 'function') {
        originalError(error);
      }
    };

    subscriber.complete = () => {
      if (isUnsubscribed) {
        return;
      }

      isUnsubscribed = true;
      if (typeof originalComplete === 'function') {
        originalComplete();
      }
    };

    this.setup(subscriber);
    return {
      unsubscribe() {
        isUnsubscribed = true;
      },
    };
  }
}
```

#

### Solution inspired by other people's accepted solutions

```js
class Observable {
  constructor(setup) {
    this.setup = setup;
  }

  subscribe(subscriber) {
    const subscriberWrapper = {
      isUnsubscribed: false,
      next(value) {
        if (this.isUnsubscribed) {
          return;
        }

        if (subscriber instanceof Function) {
          return subscriber(value);
        }

        return subscriber.next ? subscriber.next(value) : null;
      },
      error(error) {
        if (this.isUnsubscribed) {
          return;
        }

        this.unsubscribe();
        return subscriber.error ? subscriber.error(error) : null;
      },
      complete() {
        if (this.isUnsubscribed) {
          return;
        }

        this.unsubscribe();
        return subscriber.complete ? subscriber.complete() : null;
      },
      unsubscribe() {
        this.isUnsubscribed = true;
      },
    };

    this.setup(subscriberWrapper);
    return subscriberWrapper;
  }
}
```

#

### Reference

[Problem Discuss](https://bigfrontend.dev/problem/57/discuss/1200)



# 58.get-DOM-tree-height.md

### Problem Description

Height of a tree is the maximum depth from root node. Empty root node have a height of 0.

If given DOM tree, can you create a function to get the height of it?

For the DOM tree below, we have a height of 4.

```html
<div>
  <div>
    <p>
      <button>Hello</button>
    </p>
  </div>
  <p>
    <span>World!</span>
  </p>
</div>
```

Can you solve this both recursively and iteratively?

#

### Recursive Solution with DFS

```js
/**
 * @param {HTMLElement | null} tree
 * @return {number}
 */
function getHeight(tree) {
  if (tree === null) {
    return 0;
  }

  let maxHeight = 0;
  // Use .children instead of .childNodes to ignore TextNodes.
  for (const child of tree.children) {
    maxHeight = Math.max(maxHeight, getHeight(child));
  }

  return maxHeight + 1;
}
```

#

### Iterative Solution using Stack

```js
/**
 * @param {HTMLElement | null} tree
 * @return {number}
 */
function getHeight(tree) {
  if (tree === null) {
    return 0;
  }

  let maxHeight = 0;
  const stack = [[tree, 1]];

  while (stack.length > 0) {
    const [el, height] = stack.pop();
    maxHeight = Math.max(height, maxHeight);

    for (const child of el.children) {
      stack.push([child, height + 1]);
    }
  }

  return maxHeight;
}
```

#

### Iterative Solution with BFS

```js
/**
 * @param {HTMLElement | null} tree
 * @return {number}
 */
function getHeight(tree) {
  if (tree === null) {
    return 0;
  }

  let maxHeight = 0;
  const queue = [[tree, 1]];

  while (queue.length > 0) {
    const [el, height] = queue.shift();
    maxHeight = Math.max(height, maxHeight);

    for (const child of el.children) {
      queue.push([child, height + 1]);
    }
  }

  return maxHeight;
}
```



# 59.create-a-browser-history.md

### Problem Description

I believe you are very familiar about your browser you are currently visiting https://bigfrontend.dev with.

The common actions relating to history are:

1. `new BrowserHistory()` - when you open a new tab, it is set with an empty history
2. `goBack()` - go to last entry, notice the entries are kept so that forward() could get us back
3. `forward()` - go to next visited entry
4. `visit()` - when you enter a new address or click a link, this adds a new entry but truncate the entries which we could `forward()` to.

Say we start a new tab, this is the empty history.

```
[ ]
```

Then visit url A, B, C in turn.

```
[ A, B, C]
        ↑
```

We are currently at C, we could `goBack()` to B, then to A

```
[ A, B, C]
  ↑
```

`forward()` get us to B

```
[ A, B, C]
     ↑
```

Now if we visit a new url D, since we are currently at B, C is truncated.

```
[ A, B, D]
        ↑
```

You are asked to implement a `BrowserHistory` class to mimic the behavior.

#

### Solution

```js
class BrowserHistory {
  /**
   * @param {string} url
   * if url is set, it means new tab with url
   * otherwise, it is empty new tab
   */
  constructor(url) {
    // Store the url, since the method `goBack()` should
    // return the initial url if it is out of bounds.
    /** For instance,
     *  const bh = new BrowserHistory('X')
     *  bh.visit('A')
     *  bh.goBack()
     *  bh.goBack()
     *  console.log(bh.current); // should be be 'X' rather than undefined.
     */
    this.initialUrl = url;
    this.urls = url ? [url] : [];
    this.currentIndex = this.urls.length - 1;
  }
  /**
   * @param { string } url
   */
  visit(url) {
    this.currentIndex++;
    this.urls[this.currentIndex] = url;
  }

  /**
   * @return {string} current url
   */
  get current() {
    return this.currentIndex >= 0
      ? this.urls[this.currentIndex]
      : this.initialUrl;
  }

  // go to previous entry
  goBack() {
    this.currentIndex--;
  }

  // go to next visited url
  forward() {
    this.currentIndex = Math.min(this.urls.length - 1, this.currentIndex + 1);
  }
}
```



# 6.implement-basic-debounce.md

### Problem Description

Debounce is a common technique used in Web Application, in most cases using [lodash solution](https://lodash.com/docs/4.17.15#debounce) would be a good choice.

could you implement your own version of basic `debounce()`?

In case you forgot, `debounce(func, delay)` will returned a debounced function, which delays the invoke.

Here is an example.

Before debouncing we have a series of calling like

─A─B─C─ ─D─ ─ ─ ─ ─ ─E─ ─F─G

After debouncing at wait time of 3 dashes

─ ─ ─ ─ ─ ─ ─ ─ D ─ ─ ─ ─ ─ ─ ─ ─ ─ G

**notes**

1. please follow above spec. the behavior might not be exactly the same as `lodash.debounce()`

2. because `window.setTimeout` and `window.clearTimeout` are not accurate in browser environment, they are replaced to other implementation when judging your code. They still have the same interface, and internally keep track of the timing for testing purpose.

Something like below will be used to do the test.

```js
let currentTime = 0;

const run = (input) => {
  currentTime = 0;
  const calls = [];

  const func = (arg) => {
    calls.push(`${arg}@${currentTime}`);
  };

  const debounced = debounce(func, 3);
  input.forEach((call) => {
    const [arg, time] = call.split('@');
    setTimeout(() => debounced(arg), time);
  });
  return calls;
};

expect(run(['A@0', 'B@2', 'C@3'])).toEqual(['C@5']);
```

#

### Solution

```js
/**
 * @param {Function} func
 * @param {number} wait
 */
function debounce(func, wait) {
  let timer;
  return function debounced(...args) {
    if (timer) {
      clearTimeout(timer);
    }
    timer = setTimeout(() => {
      func.apply(null, args);
    }, wait);
  };
}
```

#

### Reference

- [The Difference Between Throttling and Debouncing](https://css-tricks.com/the-difference-between-throttling-and-debouncing/)
- [Debouncing and Throttling Explained Through Examples](https://css-tricks.com/debouncing-throttling-explained-examples/)
- [JavaScript Debounce Function](https://davidwalsh.name/javascript-debounce-function)



# 60.create-your-own-new-operator.md

### Problem Description

`new` [operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new) is used to create new instance objects.

Do you know exactly what `new` does?

You are asked to implement `myNew()`, which should return an object just as what `new` does but without using `new`.

Pay attention to the return type of constructor.

#

### Solution

```js
/**
 * @param {Function} constructor
 * @param {any[]} args - argument passed to the constructor
 * `myNew(constructor, ...args)` should return the same as `new constructor(...args)`
 */
const myNew = (constructor, ...args) => {
  // The `Object.create()` method creates a new empty object, using the
  // specified object as the prototype of the newly created object.
  const obj = Object.create(constructor.prototype);

  // Call constructor with obj as its context and
  // stored its return value in the variable returnValue.
  const returnValue = constructor.apply(obj, args);

  // If returnValue is an object, return returnValue, otherwise return obj.
  // Usually, constructors do not have return statement. The new operator
  // creates an object, assign it to this, and automatically returns that
  // object as a result. If a constructor has return statement and the return
  // value is an object, the object is returned instead of the newly created
  // object, otherwise the return value is ignored.
  return returnValue instanceof Object ? returnValue : obj;
};
```

#

### Reference

- [Prototype methods, objects without **proto**](https://javascript.info/prototype-methods)
- [Constructor, operator "new"](https://javascript.info/constructor-new)



# 61.create-your-own-Function.prototype.call.md

### Problem Description

[Function.prototype.call](https://tc39.es/ecma262/#sec-function.prototype.call) is very useful when we want to alter the `this` of a function.

Can you implement your own `myCall`, which returns the same result as `Function.prototype.call`?

For the [newest ECMAScript spec](https://tc39.es/ecma262/#sec-function.prototype.call), `thisArg` are not transformed. And not replaced with window in [Strict Mode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode).

Your implementation should follow above spec and do what non Strict Mode does.

`Function.prototype.cal/apply/bind` and [Reflect.apply](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/apply) should not be used.

#

### Solution

```js
Function.prototype.mycall = function (thisArg, ...args) {
  // thisArg can be null or defined.
  thisArg = thisArg || window;

  // Transform primitive value into object, so that we can add property.
  thisArg = Object(thisArg);

  // Create a unique property name.
  const fn = Symbol();

  // Assign the function that has to be called to the unique property.
  thisArg[fn] = this;

  // Call the function as a method to get the correct context.
  const returnValue = thisArg[fn](...args);

  // Delete the unique property so that the original thisArg is not affected.
  delete thisArg[fn];
  return returnValue;
};
```



# 62.implement-BigInt-addition.md

### Problem Description

Luckily we have [BigInt](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt) in JavaScript so handle big numbers.

What if we need to do it by ourselves for older browsers?

You are asked to implement a string addition function, **with all non-negative integers in string**.

```js
add('999999999999999999', '1');
// '1000000000000000000'
```

Don't use BigInt directly, it is not our goal here.

#

### Understanding the problem

We are given two non-negative numbers, `num1` and `num2` represented as string. We are asked to implement a function that is going to return the sum of the two numbers as a string. Converting the input strings directly to numbers is not allowed.

#

### Approach

We can add the two numbers digit by digit starting from the least significant digit. We would walk backwards through the two numbers simultaneously with the help of two pointers. At each step, we would add up the two digits that the two pointers are pointing at and insert the sum to the beginning of the resultant string. Since the sum of the two digits can be greater than or equal to `10`, we also need another variable to keep track of the carry. Therefore, instead of directly adding the sum of the two digits to our output string, we need to do the following:

1. First, we add the carry from the previous step to the sum of the two digits.

2. Then, we compute the carry for the next step and 'pop' the last digit off of the sum. To do this, we can use math:

   ```js
   // Compute the carry
   carry = Math.floor(sum / 10);

   // Pop the last digit off of the sum
   lastDigit = sum % 10;
   ```

3. Lastly, we add the last digit of the sum to the beginning of the resultant string.

Once we reached the beginning of both numbers, we check whether there is a carry from the last step, i.e. `carry > 0`. If that is the case, the carry needs to be added to the beginning of the resultant string before we return it. For instance, if the two numbers are `91` and `9`, when the loop terminates, the resultant string is going to be `00` and the carry is going to be `1`.

Since we are adding a digit to the beginning of the resultant string and we need to do this `max(num1.length, num2.length)` times, our algorithm would take O(N^2) time, where N is the maximum length of `num1` and `num2`. To optimize it, we could append the digits to the end of the resultant string and reverse the entire string before returning it. However, in most programming languages strings are immutable. That means every time we append a character to a string, a brand new string needs to be created, which is an O(N) operation. Calling `str += newChar` `N` times results in an O(N^2) time complexity ([In JavaScript, `+=` is optimized](https://josephmate.github.io/java/javascript/stringbuilder/2020/07/27/javascript-does-not-need-stringbuilder.html)). Therefore, a better way to collect the digits of the final sum is to use an array and store them in reverse order: `[last significant digit, ..., most significant digit]`, because inserting a value at the beginning of an array is an O(N) operation.

**Algorithm**

- Initialize a pointer `p1` to the end of `num1`.

- Initialize a pointer `p2` to the end of `num2`.

- Use a variable to keep track of the carry.

- Initialize an empty array `result` to hold the result.

- Loop backwards through the two numbers using the two pointers. The loop terminates when we reach the beginning of both numbers: `p1 >= 0 || p2 >= 0`.

  - Set `digit1` to the digit from `num1` at index `p1`. If `p1` has reached the beginning of `num1`, set `digit1` to `0`.

  - Set `digit2` to the digit from `num2` at index `p2`. If `p2` has reached the beginning of `num2`, set `digit2` to `0`.

  - Add `digit1`, `digit2` and the carry from the previous step up; store the result in a variable `sum`.

  - Update the carry: `carry = Math.floor(sum / 10)`.

  - Get the last digit of `sum` using `sum % 10` and append it to the `result` array.

- Now if the carry is still greater than `0`, push it into the `result` array.

- Reverse the `result` array. Join the reversed array into a string and return it.

### Implementation

```js
/**
 * @param {string} num1
 * @param {string} num2
 * @return {string}
 */
function add(num1, num2) {
  let p1 = num1.length - 1;
  let p2 = num2.length - 1;
  let carry = 0;
  const result = [];

  while (p1 >= 0 || p2 >= 0) {
    const digit1 = Number(num1[p1] ?? 0);
    const digit2 = Number(num2[p2] ?? 0);
    const sum = digit1 + digit2 + carry;

    carry = Math.floor(sum / 10);
    result.push(sum % 10);

    p1--;
    p2--;
  }

  if (carry > 0) result.push(carry);

  return result.reverse().join('');
}
```

### Complexity Analysis

Let N be the length of the first input number and M be the length of the second input number.

- Time Complexity: O(max(N, M)).

- Space Complexity: O(max(N, M)).



# 63.create-cloneDeep.md

### Problem Description

`Object.assign()` could be used to do shallow copy, while for recursive deep copy, [\_.cloneDeep](https://lodash.com/docs/4.17.15#cloneDeep) could be very useful.

Can you create your own `_.cloneDeep()`?

The lodash implementation actually covers a lot of data types, for simplicity, your code just need to cover

1. [primitive types](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#Primitive_values) and their wrapper Object
2. Plain Objects (Object literal) with all enumerable properties
3. Array

#

### Understanding the problem

Write a function that creates a deep clone of a value. The function should handle primitive types, Object literal and Array.

#

### Approach

First, check the data type of the source data, return the source if it is a primitive or `null`. Depending the data type of the source, either create an empty array or an empty object to store the cloned value. Get all enumerable properties of the source including all symbol properties. Loop over the properties. For each property, recursively call the function with property value as argument; create the property in the cloned object/array and set its value to the result of the recursive call.

🙋‍♀️🙋‍♂️ In the initial attempt, I didn't handle the circular reference. We can use a `WeakMap` to store the source and its copy. If the source object is already in the `WeakMap`, return its corresponding copy instead of recursing further.

### Solution

```js
// Use WeakMap that stores cloned results to handle circular reference.
const cachedResult = new WeakMap();

function cloneDeep(data) {
  if (data === null || data === undefined) {
    return data;
  }

  if (typeof data !== 'object') {
    return data;
  }

  // If the source object is already in the WeakMap,
  // its corresponding copy is returned instead of recursing
  // further.
  if (cachedResult.has(data)) {
    return cachedResult.get(data);
  }

  const clone = Array.isArray(data) ? [] : {};
  // Store the source object and its clone in the WeakMap.
  cachedResult.set(data, clone);

  const keys = [
    ...Object.getOwnPropertyNames(data),
    ...Object.getOwnPropertySymbols(data),
  ];
  for (const key of keys) {
    clone[key] = cloneDeep(data[key]);
  }

  return clone;
}
```

#

### Reference

[Javascript Deep Clone Object with Circular References](https://stackoverflow.com/questions/40291987/javascript-deep-clone-object-with-circular-references)



# 64.auto-retry-Promise-on-rejection.md

### Problem Description

For a web application, fetching API data is a common task.

But the API calls might fail because of Network problems. Usually we could show a screen for Network Error and ask users to retry.

One approach to handle this is **auto retry when network error occurs**.

You are asked to create a `fetchWithAutoRetry(fetcher, count)`, which automatically fetch again when error happens, until the maximum count is met.

For the problem here, there is no need to detect network error, you can just retry on all promise rejections.

#

### Understanding the problem

Write a function that retries fetch upon failure up to N times. The function takes two inputs, a callback function that fetches API data and returns a promise, and an integer representing maximum retry count.

#

### Approach

Fetching data is always asynchronous, so the function needs to return a promise. Within the promise, we can use a `try...catch` block to implement the auto retry. Call the `fetcher` function in the `try` block and then `resolve` the result. If an error is thrown in the `try` block, the statement in the `catch` block will be executed. So in the `catch` block, we can try to fetch the data again if the maximum count is not reached. We can use the variable `maximumRetryCount` to keep track of how many tries we have left. Every time we retry, we decrease the `maximumRetryCount` by 1. If `maximumRetryCount` is equal to 0, the returned promise gets rejected with the thrown error as its value.

### Solution

```js
/**
 * @param {() => Promise<any>} fetcher
 * @param {number} maximumRetryCount
 * @return {Promise<any>}
 */
function fetchWithAutoRetry(fetcher, maximumRetryCount) {
  return new Promise((resolve, reject) => {
    const retry = () => {
      fetcher()
        .then((result) => {
          resolve(result);
        })
        .catch((error) => {
          if (maximumRetryCount > 0) {
            maximumRetryCount--;
            retry();
          } else {
            reject(error);
          }
        });
    };

    retry();
  });
}
```



# 65.add-comma-to-number.md

### Problem Description

Given a number, please create a function to add commas as thousand separators.

```js
addComma(1); // '1'
addComma(1000); // '1,000'
addComma(-12345678); // '-12,345,678'
addComma(12345678.12345); // '12,345,678.12345'
```

Input are all valid numbers.

#

### Understanding the problem

Implement a function that format a number with commas as thousand separators.

#

### Approach 1

Use arithmetic to solve the problem. Divide the number by 1000, get the remainder and the quotient. Pad leading zeros to the remainder if needed and then add comma, store the result. If the quotient is equal to or larger than 1000, repeat the previous steps until it is smaller than 1000. After the loop, if quotient is bigger than 0, add it to the beginning of the result.
Since the `%` operator in JavaScript will gives us a negative result if the number is negative, to simplify things I can convert a negative number into a positive one with `Math.abs` at the beginning and add the negative sign back before we return the result.

🙋‍♀️🙋‍♂️ In this approach I overlooked the rounding issue with decimals. One way to handle the rounding error is to split the number into the integer part and the decimal part at the beginning.

### Solution 1

```js
/**
 * @param {number} num
 * @return {string}
 */
function addComma(num) {
  const absoluteNum = Math.abs(num);
  let quotient = Math.floor(absoluteNum);
  const decimalPart = String(absoluteNum).split('.')[1];
  let result = '';

  while (quotient >= 1000) {
    let remainderStr = String(quotient % 1000);
    if (remainderStr.length < 3) {
      remainderStr = '0'.repeat(3 - remainderStr.length) + remainderStr;
    }
    result = ',' + remainderStr + result;
    quotient = Math.floor(quotient / 1000);
  }

  if (quotient > 0 || (quotient === 0 && !result)) {
    result = String(quotient) + result;
  }

  result = decimalPart ? result + '.' + decimalPart : result;
  return num < 0 ? `-${result}` : result;
}
```

#

### Approach 2

Convert the number into a string. Split it into the integer part and the decimal part. Iterate through the integer part starting at the end and use a variable to keep track of the groups of thousands. After the loop, append the decimal part to the result if the decimal part exists.

### Solution 2

```js
/**
 * @param {number} num
 * @return {string}
 */
function addComma(num) {
  const [integer, decimalPart] = String(num).split('.');
  let result = '';

  for (let i = integer.length - 1, count = 1; i >= 0; i--, count++) {
    result = integer[i] + result;
    if (count % 3 === 0 && i > 0) {
      result = ',' + result;
    }
  }

  return decimalPart ? `${result}.${decimalPart}` : result;
}
```

#

### Approach 3

With regular expression. Convert the number into a string, and split it into the integer part and the decimal part. In the integer part, look for all digits that are followed by one or more groups of three digits to the end of the string, insert a comma after the digit.

### Solution 3

```js
/**
 * @param {number} num
 * @return {string}
 */
function addComma(num) {
  const [integer, decimalPart] = String(num).split('.');
  const result = integer.replace(/(\d)(?=(\d{3})+$)/g, '$1,');
  return decimalPart ? `${result}.${decimalPart}` : result;
}
```



# 66.remove-duplicates-from-an-array.md

### Problem Description

Given an array containing all kinds of data, please implement a function `deduplicate()` to remove the duplicates.

You should modify the array in place. Order doesn't matter.

#

### Solution

```js
/**
 * @param {any[]} arr
 */
function deduplicate(arr) {
  const map = new Map();

  for (let i = 0; i < arr.length; i++) {
    if (map.has(arr[i])) {
      arr.splice(i, 1);
      i--;
      continue;
    }
    map.set(arr[i], 1);
  }
}
```

#

### Improved Solution

```js
/**
 * @param {any[]} arr
 */
function deduplicate(arr) {
  const map = new Map();
  let numOfUniqueItems = 0;

  for (let i = 0; i < arr.length; i++) {
    if (map.has(arr[i])) {
      continue;
    }

    map.set(arr[i], 1);
    if (i > numOfUniqueItems) {
      [arr[numOfUniqueItems], arr[i]] = [arr[i], arr[numOfUniqueItems]];
    }
    numOfUniqueItems++;
  }

  arr.length = numOfUniqueItems;
}
```



# 67.create-your-own-Promise.md

### Problem Description

[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) is widely used nowadays, hard to think how I handled [Callback Hell](http://callbackhell.com/) in the old times.

Can you implement a `MyPromise` Class by yourself?

At least it should match following requirements

1. new promise: `new MyPromise((resolve, reject) => {})`
2. chaining : `MyPromise.prototype.then()` _[then handlers](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) should be called asynchronously_
3. rejection handler: `MyPromise.prototype.catch()`
4. static methods: `MyPromise.resolve()`, `MyPromise.reject()`.

This is a challenging problem. Recommend you read about Promise thoroughly first.

#

### Understanding the problem

We are asked to build our own `Promise` class that fulfills the following requirements:

1. the `new Promise` constructor accepts a callback function as an argument. The callback function receives two arguments: `resolve` and `reject`.
2. `MyPromise.prototype.then()` can be chained. `MyPromise.prototype.then()` takes in two callback functions, which should be executed asynchronously.
3. it should has a method `MyPromise.prototype.catch()` to handle rejection.
4. it should has two static methods: `MyPromise.resolve()`, `MyPromise.reject()`, so we can write e.g.`MyPromise.resolve('foo').then(() => {})`.

#

### Approach

#### The Constructor of `MyPromise`

The `promise` object created by the class `Promise` has a `state` property. Initially, the `state` is `pending`. Thus, in the constructor of the `MyPromise` class, we would define a property `state` and initialize it to `'pending'`.

```js
class MyPromise {
  #state;

  constructor() {
    this.#state = 'pending';
  }
}
```

&nbsp;

#### Handling the function passed to `new Promise()`

The function is called the **executor**. It is executed immediately and automatically by `new Promise()` and is able to either `resolve` or `reject` the promise. Hence, we would call the function in the `constructor` with the arguments `resolve` and `reject`.

If the executor function throws any errors, the promise will be rejected. To address this, we could make use of the `try...catch` statement and reject the promise in the `catch` block.

```js
class MyPromise {
  constructor(executor) {
    // ...
    try {
      executor(/* resolve */, /* reject */);
    } catch (error) {
      // reject the promise
    }
  }
}
```

&nbsp;

#### Implementing `resolve` and `reject` passed to the executor function

A executor function that resolves the promise looks like this:

```js
function(resolve, reject) {
  setTimeout(() => {
    resolve('Done!');
  }, 1000);
}
```

A executor function that rejects the promise looks like so:

```js
 function(resolve, reject) {
  setTimeout(() => {
    reject(new Error('Something went wrong!'));
  }, 1000);
}
```

Therefore `resolve` and `reject` are both functions which receive one argument. We could define the two methods on the class `MyPromise` with the following signatures:

```js
class MyPromise {
  // ...

  #resolve(value) {}

  #reject(reason) {}
}
```

When `resolve()` is called, the `state` of the `promise` is changed to `'fulfilled'`; when `reject()` is called, the `state` is changed to `'rejected'`.

Besides the `state` property, the `promise` object also has a `result` property to store the promise result. Initially, it is set to `undefined`. Its value changes either to the resolved value if `resolve()` is called, or to the rejected value if `reject()` is called.

In addition, a `Promise` can only be resolved or rejected once.

Thus, we could implement our `resolve()` and `reject()` methods like so:

```js
#resolve(value) {
  // Ensure the promise is only resolved or rejected once.
  if (this.#state !== 'pending') return;

  this.#state = 'fulfilled';
  this.#result = value;
}

#reject(reason) {
  // Ensure the promise is only resolved or rejected once.
  if (this.#state !== 'pending') return;

  this.#state = 'rejected';
  this.#result = reason;
}
```

Since `this.#resolve()` and `this.#reject()` are passed to the executor function as parameters, [`this` inside the two methods will refer to the global object instead of the `promise` object](https://github.com/getify/You-Dont-Know-JS/blob/1st-ed/this%20%26%20object%20prototypes/ch2.md#implicitly-lost). Therefore we need to `bind` both methods in the `constructor` or use the experimental fat arrow class methods.

&nbsp;

#### Implementing the `then()` method

`Promise.prototype.then()` takes up to two arguments:

1.  The first argument is a function that is invoked when the promise is resolved, and receives the promise result. If it is not a function, it is replaced with a function that simply returns the received result.

2.  The second argument is a function that runs when the promise is rejected, and receives the reason. If it is not a function, it is replaced with a "Thrower" function.

```js
promise.then(
  (value) => {
    // handle a successful value
  },
  (reason) => {
    // handle the reason
  }
);
```

Both arguments are optional.

The `Promise.prototype.then()` returns a new `Promise`:

```js
const promise = Promise.resolve('from promise');
const thenPromise = promise.then((result) => {});

console.log(promise);
// Promise {
//   [[PromiseState]]: 'fulfilled',
//   [[PromiseResult]]: 'from promise',
// }
console.log(thenPromise);
// Promise {
//   [[PromiseState]]: 'pending',
//   [[PromiseResult]]: undefined,
// }

setTimeout(() => {
  console.log(thenPromise);
});
// Promise {
//   [[PromiseState]]: 'fulfilled',
//   [[PromiseResult]]: undefined,
// }
```

Hence, the `then` method of the class `MyPromise` would look like this:

```js
then(onFulfilled, onRejected) {
  // to do

  return new MyPromise((resolve, reject) => {});
}
```

1. **Handling the first argument in our `then()` method**

   Since the callback function runs when the promise is resolved, it cannot be executed within the `then()` method.

   For example:

   ```js
   class MyPromise {
     #state;
     #result;

     constructor(executor) {
       this.#state = 'pending';
       this.#result = undefined;

       try {
         executor(this.#resolve.bind(this), this.#reject.bind(this));
       } catch (error) {
         this.#reject(error);
       }
     }

     #resolve(value) {
       // ...
       this.#state = 'fulfilled';
       this.#result = value;
     }

     then(onFulfilled) {
       onFulfilled(this.#result);
     }
   }

   const p = new MyPromise((resolve) => {
     resolve(10);
   }).then((value) => {
     console.log(value); // 10
   });
   ```

   Although the code above seems to work, it doesn't work as intended when the promise is resolved asynchronously, even if the `onFulfilled()` is called asynchronously:

   ```js
   class MyPromise {
     // ...

     then(onFulfilled) {
       // Call `onFulfilled()` asynchronously.
       queueMicrotask(() => {
         onFulfilled(this.#result);
       });
     }
   }

   const p = new MyPromise((resolve) => {
     setTimeout(() => {
       resolve(10);
     }, 0);
   }).then((value) => {
     console.log(value); // undefined
   });
   ```

   Therefore, the `onFulfilled()` function should be called within the `#resolve()` method and the `then()` method just registers the `onFulfilled()` function. The `onFulfilled()` function is like a subscriber, subscribing to the promised result, and the `then()` method is kind of like the function `subscribe()` in the Publisher/Subscriber Pattern, which receives subscriber callbacks and stores/registers them in some data structures.

   ```js
   class MyPromise {
     // ...
     #onFulfilled;

     constructor(executor) {
       // ...
       this.#onFulfilled = undefined;
       // ...
     }

     #resolve(value) {
       //...
       this.#onFulfilled(this.#result);
     }

     // ...

     then(onFulfilled) {
       // If `onFulfilled` is not a function, replace it with a function
       // that simply returns the received result.
       this.#onFulfilled =
         typeof onFulfilled === 'function' ? onFulfilled : (value) => value;

       return new Promise((resolve, reject) => {});
     }
   }
   ```

   Although the `then` method will be triggered instantly, the callback functions (handlers) will be invoked asynchronously. `Promise` uses the _microtask_ queue to run the callbacks. When a promise is settled, its `then` handlers are add into the microtask queue. [The microtasks get run whenever JavaScript finishes executing, in other words, whenever the JavaScript stack becomes empty](https://youtu.be/cCOL7MC4Pl0?t=1475):

   ```js
   console.log('Start!');

   setTimeout(() => {
     console.log('Timeout!');
   }, 0);

   Promise.resolve('Promise!').then((value) => {
     console.log(value);
   });

   console.log('End!');

   // Logs:
   // 'Start!'
   // 'End!'
   // 'Promise!'
   // 'Timeout!'
   ```

   To queue an function for execution in the microtask queue, we could use the function [queueMicrotask()](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/queueMicrotask):

   ```js
   #resolve(value) {
     //...
     queueMicroTask(() => {
       this.#onFulfilled(this.#result);
     });
   }
   ```

   Next, we need to handle the value returned by the `onFulfilled()` function.

   In the `Promise`, if the `onFulfilled()` function:

   - returns a value, the promise returned by `then()` gets resolved with the returned value as its value.

     ```js
     const promise = Promise.resolve('from promise');

     const thenPromise = promise.then((value) => {
       return 'from then handler';
     });

     setTimeout(() => {
       console.log(thenPromise);
     });
     // Promise {
     //   [[PromiseState]]: 'fulfilled',
     //   [[PromiseResult]]: 'from then handler',
     // }
     ```

   - doesn't return anything, the promise returned by `then()` gets resolved with an `undefined` value.

     ```js
     const promise = Promise.resolve('from promise');

     const thenPromise = promise.then((value) => {
       console.log(value); // 'from promise'
     });

     setTimeout(() => {
       console.log(thenPromise);
     });
     // Promise {
     //   [[PromiseState]]: 'fulfilled',
     //   [[PromiseResult]]: undefined,
     // }
     ```

   - throws an error, the promise returned by `then()` gets rejected with the error as its value.

     ```js
     const promise = Promise.resolve('from promise');

     const thenPromise = promise.then((value) => {
       throw new Error('error from then handler');
     });

     setTimeout(() => {
       console.log(thenPromise);
     });
     // Promise {
     //   [[PromiseState]]: 'rejected',
     //   [[PromiseResult]]: Error: error from then handler.
     // }
     ```

   - returns an already fulfilled promise, the promise returned by `then()` gets resolved with that promise's value as its value.

     ```js
     const promise = Promise.resolve('from promise');

     const thenPromise = promise.then((value) => {
       return Promise.resolve('resolved promise returned by then handler');
     });

     setTimeout(() => {
       console.log(thenPromise);
     });
     // Promise {
     //   [[PromiseState]]: 'fulfilled',
     //   [[PromiseResult]]: 'resolved promise returned by then handler',
     // }
     ```

   - returns an already rejected promise, the promise returned by `then()` gets rejected with that promise's value as its value.

     ```js
     const promise = Promise.resolve('promise');

     const thenPromise = promise.then((value) => {
       return Promise.reject('rejected promise returned by then handler');
     });

     setTimeout(() => {
       console.log(thenPromise);
     });
     // Promise {
     //   [[PromiseState]]: 'rejected',
     //   [[PromiseResult]]: 'rejected promise returned by then handler',
     // }
     ```

   - returns a **pending** promise, the promise returned by `then()` gets resolved or rejected after the the promise returned by the handler gets resolved or rejected. The resolved value of the promise returned by `then()` will be the same as the resolved value of the promise returned by the handler.

     ```js
     const promise = Promise.resolve('promise');

     const thenPromise = promise.then((value) => {
       return new Promise((resolve, reject) => {
         setTimeout(() => {
           resolve('resolved promise returned by then handler');
         }, 3000);
       });
     });

     setTimeout(() => {
       console.log(thenPromise); // Promise {<pending>}
     });

     setTimeout(() => {
       console.log(thenPromise);
       // Promise {<fulfilled>: "resolved promise returned by then handler"}
     }, 4000);
     ```

   Therefore, we need to make the `resolve()` and `reject()` of the promise returned by `then()` available in the `#resolve()` method, so that they can be invoked with the value returned by the `onFulfilled()` function. We could define two properties on `MyPromise` to store both functions:

   ```js
   class MyPromise {
     // ...
     #thenPromiseResolve;
     #thenPromiseReject;

     constructor(executor) {
       // ...
       this.#thenPromiseResolve = undefined;
       this.#thenPromiseReject = undefined;

       // ...
     }

     // ...

     then(onFulfilled) {
       // ...
       return new Promise((resolve, reject) => {
         this.#thenPromiseResolve = resolve;
         this.#thenPromiseReject = reject;
       });
     }
   }
   ```

   If the `onFulfilled()` function returns a value or `undefined`, we could just call `this.#thenPromiseResolve()` and pass the return value to it:

   ```js
    #resolve(value) {
      // ...
      queueMicroTask(() => {
        const returnValue = this.#onFulfilled(this.#result);

        if (!(returnValue instanceof MyPromise)) {
          this.#thenPromiseResolve(returnValue);
        } else {
          // do something else
        }
     });
   }
   ```

   If the return value is an instance of `MyPromise`, we need to wait for the promise to be settled and then resolve/reject the promise returned by the `then()` method. To accomplish this, we could call the `then()` method of the return value, passing `this.#thenPromiseResolve` and `this.#thenPromiseReject` as the `then` handlers.

   ```js
   #resolve(value) {
     // ...
     queueMicroTask(() => {
       const returnValue = this.#onFulfilled(this.#result);

       if (!(returnValue instanceof MyPromise)) {
         this.#thenPromiseResolve(returnValue);
       } else {
         returnValue.then(
           this.#thenPromiseResolve,
           this.#thenPromiseReject,
         );
       }
     });
   }
   ```

   To catch any errors thrown by the `this.#onFulfilled()` function, we could use a `try...catch` statement:

   ```js
   #resolve(value) {
     // ...
     queueMicroTask(() => {
       try {
        // ...
       } catch (error) {
         this.#thenPromiseReject(error);
       }
     });
   }
   ```

   In addition, we also need to ensure `this.#onFulfilled` is not `undefined`, since `then()` might not be called:

   ```js
   const promise = Promise.resolve('foo');

   promise.then((result) => {
     return 'bar';
   });
   // The promise returned by `then` is resolved, but there
   // is no further action with the promise. Therefore, when
   // the`#resolve()` method of the returned promise runs,
   // `this.#onFulfilled` would be `undefined`.
   ```

2. **Handling the second argument in our `then()` method**

   The second callback function runs when the promise is rejected. Like the first argument, the `then()` method should register the second callback function, so that `#reject()` can execute it asynchronously whenever the promise is rejected:

   ```js
   class MyPromise {
     // ...
     #onRejected;

     constructor(executor) {
       // ...
       this.#onRejected = undefined;
       // ...
     }

     // ...

     #reject(reason) {
       //...
       queueMicrotask(() => {
         this.#onRejected(this.#result);
       });
     }

     // ...

     then(onFulfilled, onRejected) {
       // ...

       // If `onRejected` is not a function, replace it with a
       // function that throws the received argument.
       this.#onRejected =
         typeof onRejected === 'function'
           ? onRejected
           : (reason) => {
               throw reason;
             };

       // ...
     }
   }
   ```

   Now we need to handle the value returned by the `onRejected()` function.

   In the `Promise`, if the `onRejected`:

   - is not a function, the `onRejected` is replaced with a function that throws the received argument, and the promise returned by `then()` gets rejected with that promise's value as its value.

     ```js
     const promise = Promise.reject('error from promise');

     const thenPromise = promise.then((value) => {});

     setTimeout(() => {
       console.log(thenPromise);
     });
     // Uncaught (in promise) error from promise
     // Promise {
     //   [[PromiseState]]: 'rejected',
     //   [[PromiseResult]]: 'error from promise',
     // }
     ```

   - throws an error, the promise returned by `then()` gets rejected with the thrown error as its value.

     ```js
     const promise = Promise.reject('error from promise');

     const thenPromise = promise.then(null, (reason) => {
       throw new Error('Error from onRejected');
     });

     setTimeout(() => {
       console.log(thenPromise);
     });
     // Promise {
     //   [[PromiseState]]: 'rejected',
     //   [[PromiseResult]]: Error: Error from onRejected
     // }
     ```

   - doesn't return anything, the promise returned by `then()` gets resolved with an `undefined` value.

     ```js
     const promise = Promise.reject('error from promise');

     const thenPromise = promise.then(null, (reason) => {
       console.log(reason);
     });

     setTimeout(() => {
       console.log(thenPromise);
     });
     // Promise {
     //   [[PromiseState]]: 'fulfilled',
     //   [[PromiseResult]]: undefined
     // }
     ```

   - returns a value, the promise returned by `then()` gets resolved with the returned value as its value.

     ```js
     const promise = Promise.reject('error from promise');

     const thenPromise = promise.then(null, (reason) => {
       return 'value returned by onRejected handler';
     });

     setTimeout(() => {
       console.log(thenPromise);
     });
     // Promise {
     //   [[PromiseState]]: 'fulfilled',
     //   [[PromiseResult]]: 'value returned by onRejected handler'
     // }
     ```

   - returns an already fulfilled promise, the promise returned by `then()` gets resolved with that promise's value as its value.

     ```js
     const promise = Promise.reject('error from promise');

     const thenPromise = promise.then(null, (reason) => {
       return Promise.resolve(
         'resolved promise returned by onRejected handler'
       );
     });

     setTimeout(() => {
       console.log(thenPromise);
     });
     // Promise {
     //   [[PromiseState]]: 'fulfilled',
     //   [[PromiseResult]]: 'resolved promise returned by onRejected handler'
     // }
     ```

   - returns an already rejected promise, the promise returned by `then()` gets rejected with that promise's value as its value.

     ```js
     const promise = Promise.reject('error from promise');

     const thenPromise = promise.then(null, (reason) => {
       return Promise.reject('rejected promise returned by onRejected handler');
     });

     setTimeout(() => {
       console.log(thenPromise);
     });
     // Promise {
     //   [[PromiseState]]: 'rejected',
     //   [[PromiseResult]]: 'rejected promise returned by onRejected handler'
     // }
     ```

   - returns a **pending** promise, the promise returned by `then()` gets resolved or rejected after the promise returned by the handler gets resolved or rejected.

     ```js
     const promise = Promise.reject('error from promise');

     const thenPromise = promise.then(null, (reason) => {
       return new Promise((resolve, reject) => {
         setTimeout(() => {
           resolve('resolved promise returned by onRejected handler');
         }, 3000);
       });
     });

     setTimeout(() => {
       console.log(thenPromise); // Promise {<pending>}
     });

     setTimeout(() => {
       console.log(thenPromise);
       // Promise {<fulfilled>: "resolved promise returned by onRejected handler"}
     }, 4000);
     ```

   As we can see it is basically the same as the `onFulfilled()`. We could update our `#reject()` method like below:

   ```js
   #reject(reason) {
     // ...
     queueMicrotask(() => {
       if (!this.#onRejected) return;

       try {
         const returnValue = this.#onRejected(this.#result);

         if (!(returnValue instanceof MyPromise)) {
           this.#thenPromiseResolve(returnValue);
         } else {
           returnValue.then(this.#thenPromiseResolve, this.#thenPromiseReject);
         }
       } catch (error) {
         this.#thenPromiseReject(error);
       }
     });
   }
   ```

&nbsp;

#### Implementing the `catch()` method

In the native `Promise`, we can also use the `catch()` method to handle rejected cases. The `Promise.prototype.catch()` also returns a new `Promise`.

The syntax is:

```js
const promise1 = new Promise((resolve, reject) => {
  throw 'Uh-oh!';
});

promise1.catch((error) => {
  console.error(error);
});
// expected output: Uh-oh!
```

Calling the `catch()` method is exactly the same as calling `Promise.prototype.then(null, errorHandlingFunction)`. [In fact, calling obj.catch(onRejected) internally calls obj.then(undefined, onRejected)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch).

Hence, we could implement our `catch()` method this way:

```js
catch(onRejected) {
  return this.then(null, onRejected);
}
```

&nbsp;

#### Implementing the static method `MyPromise.resolve()`

In the native `Promise`, the static method `Promise.resolve()` returns a `Promise` object that is resolved with a given value:

- Resolving a string:

  ```js
  Promise.resolve('Success').then(
    (value) => {
      console.log(value); // "Success"
    },
    (reason) => {
      // not called
    }
  );
  ```

- Resolving another `Promise`:

  ```js
  const original = Promise.resolve(33);
  const cast = Promise.resolve(original);
  cast.then(function (value) {
    console.log('value: ' + value);
  });
  console.log('original === cast?: ' + (original === cast));

  // logs:
  // original === cast?: true
  // value: 33
  ```

It is worth pointing out that [Promise.resolve() also handle the case where the value is a thenable](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve#resolving_thenables_and_throwing_errors). However, we don't need to cover it here.

In `MyPromise.resolve()`, we could first check if the value received is an instance of `MyPromise`. If it is, we could just return it. Otherwise we return a new instance of `MyPromise` and resolve the promise with the given value.

```js
static resolve(value) {
  if (value instanceof MyPromise) return value;

  return new MyPromise((resolve) => {
    resolve(value);
  });
}
```

&nbsp;

#### Implementing the static method `MyPromise.reject()`

In the native `Promise`, the static method `Promise.reject()` returns a `Promise` object that is rejected with a given reason:

```js
Promise.reject(new Error('fail')).then(
  (value) => {
    // not called
  },
  (reason) => {
    console.log(reason); // Error: fail
  }
);
```

We could implement our `MyPromise.reject()` like this:

```js
static reject(reason) {
  return new MyPromise((_, reject) => {
    reject(reason);
  });
}
```

&nbsp;

### Implementation

```js
class MyPromise {
  #state;
  #result;
  #onFulfilled;
  #onRejected;
  #thenPromiseResolve;
  #thenPromiseReject;

  constructor(executor) {
    this.#state = 'pending';
    this.#result = undefined;
    this.#onFulfilled = undefined;
    this.#onRejected = undefined;
    this.#thenPromiseResolve = undefined;
    this.#thenPromiseReject = undefined;

    try {
      executor(this.#resolve.bind(this), this.#reject.bind(this));
    } catch (error) {
      this.#reject(error);
    }
  }

  #resolve(value) {
    if (this.#state !== 'pending') return;

    this.#state = 'fulfilled';
    this.#result = value;

    queueMicrotask(() => {
      if (
        !this.#onFulfilled ||
        !this.#thenPromiseResolve ||
        !this.#thenPromiseReject
      ) {
        return;
      }

      try {
        const returnValue = this.#onFulfilled(this.#result);

        if (!(returnValue instanceof MyPromise)) {
          this.#thenPromiseResolve(returnValue);
        } else {
          returnValue.then(this.#thenPromiseResolve, this.#thenPromiseReject);
        }
      } catch (error) {
        this.#thenPromiseReject(error);
      }
    });
  }

  #reject(reason) {
    if (this.#state !== 'pending') return;

    this.#state = 'rejected';
    this.#result = reason;

    queueMicrotask(() => {
      if (
        !this.#onRejected ||
        !this.#thenPromiseResolve ||
        !this.#thenPromiseReject
      ) {
        return;
      }

      try {
        const returnValue = this.#onRejected(this.#result);

        if (!(returnValue instanceof MyPromise)) {
          this.#thenPromiseResolve(returnValue);
        } else {
          returnValue.then(this.#thenPromiseResolve, this.#thenPromiseReject);
        }
      } catch (error) {
        this.#thenPromiseReject(error);
      }
    });
  }

  then(onFulfilled, onRejected) {
    // Register consuming functions.
    this.#onFulfilled =
      typeof onFulfilled === 'function' ? onFulfilled : (value) => value;
    this.#onRejected =
      typeof onRejected === 'function'
        ? onRejected
        : (reason) => {
            throw reason;
          };

    return new MyPromise((resolve, reject) => {
      // Register `resolve` and `reject`, so that we can
      // resolve or reject this promise in `#resolve`
      // or `#reject`.
      this.#thenPromiseResolve = resolve;
      this.#thenPromiseReject = reject;
    });
  }

  catch(onRejected) {
    return this.then(undefined, onRejected);
  }

  static resolve(value) {
    if (value instanceof MyPromise) return value;

    return new MyPromise((resolve) => {
      resolve(value);
    });
  }

  static reject(reason) {
    return new MyPromise((_, reject) => {
      reject(reason);
    });
  }
}
```

### Note:

The above solution doesn't handle the following cases:

1. When calling the `then()` method asynchronously, it wouldn't properly trigger the handler passed to `then()`. (Credits to [L1ef6Zi](https://bigfrontend.dev/user/L1ef6Zi)

   For instance:

   ```js
   const promise = MyPromise.resolve(1);

   setTimeout(() => {
     promise.then(console.log);
   });
   ```

   To handle this, we could check the state of the promise in the `then()` method. If the promise is already fulfilled, we could execute the `onFulfilled()` immediately.

2. The `#resolve()` doesn't handle the case when the initial resolved value is a promise. (Credits to [L1ef6Zi](https://bigfrontend.dev/user/L1ef6Zi)

   For example:

   ```js
   const promise = new Promise((resolve) => {
     resolve(Promise.resolve(1));
   });
   // `PromiseResult` is `1`

   const promise = new MyPromise((resolve) => {
     resolve(MyPromise.resolve(1));
   });
   // `PromiseResult` is another promise
   ```

   To address this, we could check whether the `value` is an instance of `MyPromise` in the `#resolve()` method. If that is the case, we could call the `value`'s `then()` method and pass in `(value) => { this.#result = value; }` as the parameter:

   ```js
   class MyPromise {
     // ...

     #resolve(value) {
       // ...

       if (value instanceof MyPromise) {
         value.then((value) => {
           this.#state = 'fulfilled';
           this.#result = value;
         });
       } else {
         this.#state = 'fulfilled';
         this.#result = value;
       }

       // ...
     }

     // ...
   }

   const myPromise1 = new MyPromise((resolve) => {
     resolve(MyPromise.resolve(1));
   });
   console.log('myPromise1: ', myPromise1);

   const promise1 = new Promise((resolve) => {
     resolve(Promise.resolve(1));
   });
   console.log('promise1: ', promise1);

   // logs (Chrome):
   // myPromise1:  MyPromise {state: 'pending'}
   // promise1:  Promise {<pending>}
   //
   // When we expand the object, we get:
   // MyPromise {
   //   #result: 1
   //   #state: "fulfilled"
   // }
   // Promise {
   //   [[PromiseState]]: "fulfilled"
   //   [[PromiseResult]]: 1
   // }

   const myPromise2 = new MyPromise((resolve) => {
     resolve(
       new MyPromise((resolve) => {
         setTimeout(() => {
           resolve(1);
         }, 1000);
       })
     );
   });
   console.log('myPromise2: ', myPromise2);

   const promise2 = new Promise((resolve) => {
     resolve(
       new Promise((resolve) => {
         setTimeout(() => {
           resolve(1);
         }, 1000);
       })
     );
   });
   console.log('promise2: ', promise2);

   // logs (Chrome):
   // myPromise2:  MyPromise {state: 'pending'}
   // Promise {<pending>}
   //
   // When we expand the object, we get:
   // MyPromise {
   //   #result: 1
   //   #state: "fulfilled"
   // }
   // Promise {
   //   [[PromiseState]]: "fulfilled"
   //   [[PromiseResult]]: 1
   // }
   ```

3. The solution doesn't handle multiple `then()` for the same promise. (Credits to [1aN6SC9](https://bigfrontend.dev/user/1aN6SC9))

   For example:

   ```js
   const promise = new Promise((resolve, reject) => {
     setTimeout(() => {
       resolve(44);
     }, 1000);
   });
   promise.then((value) => console.log('value1: ', value));
   promise.then((value) => console.log('value2: ', value));
   // logs:
   // value1: 44
   // value2: 44

   const myPromise = new MyPromise((resolve, reject) => {
     setTimeout(() => {
       resolve(44);
     }, 1000);
   });
   myPromise.then((value) => console.log('value1: ', value));
   myPromise.then((value) => console.log('value2: ', value));
   // logs:
   // value2: 44
   ```

   To handle this, we could use an array to store the `then` handlers.

#

### Reference

- [Promises](https://javascript.info/promise-basics)
- [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [Promise.prototype.then()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then)
- [Promise.prototype.catch()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch)
- [Promise.resolve()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve)
- [Promise.reject()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/reject)
- [Using microtasks in JavaScript with queueMicrotask()](https://developer.mozilla.org/en-US/docs/Web/API/HTML_DOM_API/Microtask_guide)
- [Event loop: microtasks and macrotasks](https://javascript.info/event-loop)
- [Microtasks](https://javascript.info/microtask-queue)



# 68.get-DOM-tags.md

### Problem Description

Given a DOM tree, please return all the tag names it has.

Your function should return a unique array of tags names in lowercase, order doesn't matter.

#

### Understanding the problem

We are given a DOM tree and we are asked to write a function that returns all the unique **tag names** in it in an array. The tag names should be returned in lowercase, and we can return them in any order.

#

### Approach

We could traverse the DOM tree using either DFS or BFS to get all of the tag names. To store only the unique tag names, we could make use of a `Set`, since a `Set` cannot have duplicate values.

Because only an `Element` has the tag, as we traverse the DOM tree, we need to use `Element.children` rather than `Node.childNodes` to get only the child `Element`s.

### Implementation

Recursive DFS:

```js
/**
 * @param {HTMLElement} tree
 * @return {string[]}
 */
function getTags(tree) {
  const tagNames = new Set();

  dfs(tree, tagNames);
  return [...tagNames];
}

function dfs(el, tagNames) {
  const tagName = el.tagName.toLowerCase();
  tagNames.add(tagName);

  for (const child of el.children) {
    dfs(child, tagNames);
  }
}
```

Iterative DFS:

```js
/**
 * @param {HTMLElement} tree
 * @return {string[]}
 */
function getTags(tree) {
  const tagNames = new Set();
  const stack = [tree];

  while (stack.length > 0) {
    const el = stack.pop();
    const tagName = el.tagName.toLowerCase();
    tagNames.add(tagName);

    stack.push(...el.children);
  }

  return [...tagNames];
}
```

Iterative BFS:

```js
/**
 * @param {HTMLElement} tree
 * @return {string[]}
 */
function getTags(tree) {
  const tagNames = new Set();
  // Assume we have a Queue data structure implemented.
  const queue = [tree];

  while (queue.length > 0) {
    const el = queue.shift();
    const tagName = el.tagName.toLowerCase();
    tagNames.add(tagName);

    queue.push(...el.children);
  }

  return [...tagNames];
}
```



# 69.implement-deep-equal-isEqual.md

### Problem Description

[\_.isEqual](https://lodash.com/docs/4.17.15#isEqual) is useful when you want to compare complex data types by value not the reference.

Can you implement your own version of deep equal `isEqual`? The lodash version covers a lot of data types. In this problem, you are asked to support:

1. primitives
2. plain objects (object literals)
3. array

Objects are compared by their own, not inherited, enumerable properties

```js
const a = { a: 'bfe' };
const b = { a: 'bfe' };

isEqual(a, b); // true
a === b; // false

const c = [1, a, '4'];
const d = [1, b, '4'];

isEqual(c, d); // true
c === d; // false
```

Lodash implementation has some strange behaviors. ([github issue](https://github.com/lodash/lodash/issues/2463), like following code

```js
const a = {};
a.self = a;
const b = { self: a };
const c = {};
c.self = c;
const d = { self: { self: a } };
const e = { self: { self: b } };
```

`lodash.isEqual` gives us following result. Notice there is a case that resulting in `false`.

```js
// result from lodash implementation
_.isEqual(a, b); // true
_.isEqual(a, c); // true
_.isEqual(a, d); // true
_.isEqual(a, e); // true
_.isEqual(b, c); // true
_.isEqual(b, d); // true
_.isEqual(b, e); // false
_.isEqual(c, d); // true
_.isEqual(c, e); // true
_.isEqual(d, e); // true
```

Setting aside the performance concerns mentioned by lodash, **your implement should not have above problem**, which means above all returns true and call stack should not exceed the maximum.

#

### Understanding the problem

We are asked to write a function that compares not only primitive types but also reference types.

In JavaScript, when comparing two reference types, i.e., two objects, they are compared by reference not by value:

```js
const foo = { a: 1 };
const bar = { a: 1 };

foo === bar; // false
```

The function needs to cover primitives, objects and arrays. It also needs to handle circular reference.

#

### Approach

- Check if one of the two inputs is primitive by using `typeof` operator; since the `typeof` primitive `null` is `object`, check if one of the inputs is equal to `null`. If one of the two inputs is primitive, then I can compare them by reference.

- Check if the data type of the two inputs are the same, both are arrays or objects. If they are not, return false.

- Use `Object.keys` and `Object.getOwnPropertySymbols` to get all of the enumerable and not-inherited properties of the two inputs. Compare their size respectively, if one of them is not equal, return false.

- Compare the property names and the values. Loop through all the properties of the first input data, check if the property name also exist in the second input data, if not, return false; otherwise recursively compare the property value.

- To handle the circular reference, initialize a `WeakMap` that is going to keep track of the objects or arrays that have been seen, in which each key is an object or an array and each value is a set of objects or arrays, that have been compared to that object or array.

- If we get out of the loop without returning `false`, return `true`.

### Solution

```js
const cached = new WeakMap();

/**
 * @param {any} a
 * @param {any} b
 * @return {boolean}
 */
function isEqual(a, b) {
  if (a === null || b === null) {
    return a === b;
  }

  if (typeof a !== 'object' || typeof b !== 'object') {
    return a === b;
  }

  const dataTypeA = detectDataType(a);
  const dataTypeB = detectDataType(b);
  if (dataTypeA !== dataTypeB) return false;

  const keysA = Object.keys(a);
  const keysB = Object.keys(b);
  if (keysA.length !== keysB.length) return false;

  const symbolsA = Object.getOwnPropertySymbols(a);
  const symbolsB = Object.getOwnPropertySymbols(b);
  if (symbolsA.length !== symbolsB.length) return false;

  if (cached.get(a)?.has(b)) return true;
  if (cached.get(b)?.has(a)) return true;

  cache(a, b, cached);

  const propertyNamesA = [...keysA, ...symbolsA];

  for (const propertyNameA of propertyNamesA) {
    if (!b.hasOwnProperty(propertyNameA)) return false;

    const propertyValueA = a[propertyNameA];
    const propertyValueB = b[propertyNameA];

    if (!isEqual(propertyValueA, propertyValueB)) {
      return false;
    }
  }

  return true;
}

function detectDataType(data) {
  if (Array.isArray(data)) return 'array';
  return 'object';
}

function cache(a, b, cached) {
  let setForA = cached.get(a);
  if (!setForA) {
    cached.set(a, (setForA = new Set()));
  }
  setForA.add(b);

  let setForB = cached.get(b);
  if (!setForB) {
    cached.set(b, (setForB = new Set()));
  }
  setForB.add(a);
}
```



# 7.implement-debounce-with-leading-and-trailing-option.md

### Problem Description

This is a follow up on [6. implement basic debounce()](https://bigfrontend.dev/problem/implement-basic-debounce), please refer to it for detailed explanation.

In this problem, you are asked to implement a enhanced `debounce()` which accepts third parameter, `option: {leading: boolean, trailing: boolean}`

1. leading: whether to invoke right away
   2/ trailing: whether to invoke after the delay.

[6. implement basic debounce()](<https://bigfrontend.dev/problem/implement-basic-debounce()>) is the default case with `{leading: false, trailing: true}`.

for the previous example of debouncing by 3 dashes

─A─B─C─ ─D─ ─ ─ ─ ─ ─ E─ ─F─G

with `{leading: false, trailing: true}`, we get as below

─ ─ ─ ─ ─ ─ ─ ─D─ ─ ─ ─ ─ ─ ─ ─ ─ G

with `{leading: true, trailing: true}`:

─A─ ─ ─ ─ ─ ─ ─D─ ─ ─E─ ─ ─ ─ ─ ─G

with `{leading: true, trailing: false}`

─A─ ─ ─ ─ ─ ─ ─ ─ ─ ─E

with `{leading: false, trailing: false}`, of course, nothing happens.

**notes**

1. please follow above spec. the behavior might not be exactly the same as `lodash.debounce()`

2. because `window.setTimeout` and `window.clearTimeout` are not accurate in browser environment, they are replaced to other implementation when judging your code. They still have the same interface, and internally keep track of the timing for testing purpose.

Something like below will be used to do the test.

```js
let currentTime = 0;

const run = (input) => {
  currentTime = 0;
  const calls = [];

  const func = (arg) => {
    calls.push(`${arg}@${currentTime}`);
  };

  const debounced = debounce(func, 3);
  input.forEach((call) => {
    const [arg, time] = call.split('@');
    setTimeout(() => debounced(arg), time);
  });
  return calls;
};

expect(run(['A@0', 'B@2', 'C@3'])).toEqual(['C@6']);
```

#

### Initial Attempt

```js
/**
 * @param {Function} func
 * @param {number} wait
 * @param {boolean} option.leading
 * @param {boolean} option.trailing
 */
function debounce(func, wait, option = { leading: false, trailing: true }) {
  let timeout;
  let isWaitingForLeading = false;

  return (...args) => {
    if (timeout) {
      clearTimeout(timeout);
    }

    if (!isWaitingForLeading && option.leading) {
      func.apply(null, args);
      isWaitingForLeading = true;
    }

    timeout = setTimeout(() => {
      if (option.trailing) {
        func.apply(null, args);
      }
      isWaitingForLeading = false;
    }, wait);
  };
}
```

All tests passed except `['A@0'] with {leading: true, trailing: true} expects ["A@0"]`

#

### Solution based on other people's accepted solutions

```js
/**
 * @param {Function} func
 * @param {number} wait
 * @param {boolean} option.leading
 * @param {boolean} option.trailing
 */
function debounce(func, wait, option = { leading: false, trailing: true }) {
  let timeout = null;
  // Flag to skip the trailing if leading is true
  // and the debounced function isn't called again after the initial execution.
  let isCalledForLeading = false;

  return (...args) => {
    if (timeout) {
      clearTimeout(timeout);
    }

    if (option.leading && timeout === null) {
      func.apply(null, args);
      isCalledForLeading = true;
    } else {
      isCalledForLeading = false;
    }

    timeout = setTimeout(() => {
      if (option.trailing && !isCalledForLeading) {
        func.apply(null, args);
      }
      timeout = null;
    }, wait);
  };
}
```

#

### Reference

- [Debouncing and Throttling Explained Through Examples](https://css-tricks.com/debouncing-throttling-explained-examples/)
- [Problem Discuss](https://bigfrontend.dev/problem/implement-debounce-with-leading-and-trailing-option/discuss)



# 70.implement-Observable-from.md

### Problem Description

This is a follow-up on [57. create an Observable](https://bigfrontend.dev/problem/create-an-Observable).

Suppose you have solved [57. create an Observable](https://bigfrontend.dev/problem/create-an-Observable), here you are asked to implement a creation operator `from()`.

From the [document](https://rxjs-dev.firebaseapp.com/api/index/function/from), `from()`

> Creates an Observable from an Array, an array-like object, a Promise, an iterable object, or an Observable-like object.

Your `from()` should accept all above types.

```js
from([1, 2, 3]).subscribe(console.log);
// 1
// 2
// 3
```

**Note**

1. Observable is already given for you, no need to create it.
2. for the problem here, `Observable-like` means `Observable instance`. Though in real-world you should check `Symbol.observable`

#

### Understanding the problem

I am asked to write a function called `from()` that converts an array, an array-like object, a Promise, an iterable object, or an Observable-like object into an Observable.

#

### Approach

First understand how Observable works:

```js
const observable = new Observable((subscriber) => {
  subscriber.next(1);
  subscriber.next(2);
  setTimeout(() => {
    subscriber.next(3);
    subscriber.next(4);
    subscriber.complete();
  }, 100);
});

const observer = {
  next: (value) => {
    console.log('we got a value', value);
  },
  error: (error) => {
    console.log('we got an error', error);
  },
  complete: () => {
    console.log('ok, no more values');
  },
};

const sub = observable.subscribe(observer);
// we got a value 1
// we got a value 2

// we got a value 3
// we got a value 4
// ok, no more values
```

So my `from` function should create a new instance of `Observable` and return it. Then I need to define the callback function that is passed to the new `Observable` constructor.

If the data passed to `from` is an array, in the callback function I need to iterate through every value in the array; for each value, call `subscriber.next()` passing in the value.

```js
(subscriber) => {
  for (const value of array) {
    subscriber.next(value);
  }
};
```

If the data passed to `from` is an iterable object, I can also use a `for...of` loop to iterate through all the values in it, since an iterable object is a object that has a `[Symbol.iterator]` property whose value is a function that returns an `Iterable`, such as `Map` and `Set`.

```js
(subscriber) => {
  for (const [key, value] of iterableObject) {
    subscriber.next(value);
  }
};
```

If the data passed to `from` is an array-like object, that is an object that has a `length` property and indexed elements, such as `{ 0: 'foo', 5: 'bar', length: 6 }`, use a `for...of` loop to iterate through all the values in it.

```js
(subscriber) => {
  for (const [key, value] of arrayLikeObject.entries()) {
    subscriber.next(value);
  }
};
```

The code above also works for normal objects, therefore I need to check if the input data is array-like object. So to do that, I can use `Array.from` to creates an shallowed-copied array from the input data, since `Array.from()` accepts an array-like or iterable object, and will return an empty array if the source data is not an array-like or iterable object.

If the data passed to `from` is a Promise, according to the [document](https://rxjs-dev.firebaseapp.com/api/index/function/from), the Observable created by `from` should emit the items in that promise:

```js
import { from } from 'rxjs';

const promise = Promise.resolve(2);
const result = from(promise);

result.subscribe((x) => console.log(x)); // 2
```

```js
import { from } from 'rxjs';

const promise = Promise.reject('error');
const result = from(promise);

result.subscribe((x) => console.log(x)); // not executed.
```

Therefore in our callback function, I can call the `subscriber.next()` in the `then` method of the promise, and only execute it if the promise is resolved.

```js
(subscriber) => {
  data.then((result) => {
    subscriber.next(result);
  });
};
```

If the data passed to `from` is an Observable-like object, which means it is an instance of Observable, I can returns the data.

```js
import { from, Observable } from 'rxjs';

const obj = new Observable((subscriber) => {
  subscriber.next(1);
  subscriber.next(2);
});
const result = from(obj);
console.log('isEqual: ', result === obj); // isEqual: true

result.subscribe((x) => console.log(x));
// 1
// 2
```

Since the observer/subscriber also has `error` and `complete` methods, I can use `try...catch...finally` to handle errors and invoke `complete` in the `finally` block.

🙋‍♀️🙋‍♂️ Using `Array.from` to check if the input data is an array-like or an iterable object cannot correctly handle the case where the input data is an iterable and it will throw an error, such as:

```js
function* range() {
  let i = 0;
  while (i < 5) {
    if (i === 3) {
      throw new Error('error');
    }
    yield i;
    i += 1;
  }
}
const gen = range();
```

`from` should create an Observable from such an iterable and relay the error to `subscriber.error()`:

```js
import { from } from 'rxjs';

function* range() {
  let i = 0;
  while (i < 5) {
    if (i === 3) {
      throw new Error('error');
    }
    yield i;
    i += 1;
  }
}
const gen = range();
const result = from(gen);

result.subscribe((x) => console.log(x));
// Logs:
// 0
// 1
// 2
// Error: error
```

When using `Array.from()` on such iterables, the error is thrown during the creation of the new shallow-copied array. Therefore, I need to use `typeof input[Symbol.iterator] === function` to check if the input is an iterable and use `input.length !== undefined` to check if it is an array-like; if it is an array-like, use `Array.from()` convert it into an array, so that I can use `for...of` loop to loop over it.

### Solution

```js
/**
 * @param {Array | ArrayLike | Promise | Iterable | Observable} input
 * @return {Observable}
 */
function from(input) {
  if (input instanceof Observable) return input;

  const isIterable = typeof input[Symbol.iterator] === 'function';
  const isArrayLike = input.length !== undefined;

  if (isIterable || isArrayLike) {
    return new Observable((subscriber) => {
      try {
        if (isArrayLike) input = Array.from(input);

        for (const value of input) {
          subscriber.next(value);
        }
      } catch (err) {
        subscriber.error(err);
      } finally {
        subscriber.complete();
      }
    });
  }

  if (input instanceof Promise) {
    return new Observable((subscriber) => {
      input
        .then((result) => {
          subscriber.next(result);
        })
        .catch((err) => {
          subscriber.error(err);
        })
        .finally(() => {
          subscriber.complete();
        });
    });
  }

  throw new Error(
    'You can provide an Observable, Promise, Array, or Iterable.'
  );
}
```



# 71.implement-Observable-Subject.md

### Problem Description

This is a follow-up on [57. create an Observable](https://bigfrontend.dev/problem/create-an-Observable).

Plain Observables are unicast, meaning every subscription is independent. To create multicast, you need to use [Subject](https://rxjs-dev.firebaseapp.com/guide/subject).

Following code is easier to understand.

```js
// default behavior with plain Observable
const observable = from([1, 2, 3]);
observable.subscribe(console.log);
observable.subscribe(console.log);
// 1
// 2
// 3
// 1
// 2
// 3
```

You can see that two subscriptions are independent so the logs are grouped by subscription.

with Subject, it works like Event Listeners in DOM world.

```js
const subject = new Subject();
subject.subscribe(console.log);
subject.subscribe(console.log);

const observable = from([1, 2, 3]);
observable.subscribe(subject);

// 1
// 1
// 2
// 2
// 3
// 3
```

Now the logs are different! That is because Subject first works as a observer, get the values, then works as an Observable and dispatch the value to different observers.

Cool right? Ok, you are asked to **implement a simple `Subject Class`**.

1. `Observable` is given for you, you can just use it.
2. you can use `new Observer({next,error,complete})` or `new Observer(function)` to create an observer.

#

### Understanding the problem

I am asked to write a `Subject` class. The `Subject` class should have a `subscribe` method which can register subscribers. The `Subject` instance can be provided as the argument to `observable.subscribe()`, and this will invoke all the registered subscriber. I am also given the `Observer` class. To create an observer I can use `new Observer({ next, error, complete })` or `new Observer(function)`.

#

### Approach

First understand how Observable works:

```js
const observable = new Observable((subscriber) => {
  subscriber.next(1);
  subscriber.next(2);
  setTimeout(() => {
    subscriber.next(3);
    subscriber.next(4);
    subscriber.complete();
  }, 100);
});

const observer = {
  next: (value) => {
    console.log('we got a value', value);
  },
  error: (error) => {
    console.log('we got an error', error);
  },
  complete: () => {
    console.log('ok, no more values');
  },
};

const sub = observable.subscribe(observer);
// we got a value 1
// we got a value 2

// we got a value 3
// we got a value 4
// ok, no more values
```

A `Subject` instance can be provided as the argument to `observable.subscribe()`. This means a `Subject` instance should be an observer, which is an object having the methods `next(value)`, `error(err)`, `complete()`.

```js
class Subject {
  next(value) {}

  error(error) {}

  complete() {}
}
```

The `Subject` class should also has a `subscribe` method that stores the subscribers:

```js
const subject = new Subject();
subject.subscribe(console.log);
subject.subscribe(console.log);
```

I can use an array to store the subscribers:

```js
class Subject {
  constructor() {
    this.subscribers = [];
  }

  subscribe(subscriber) {}

  // ...
}
```

According to the [document](https://rxjs-dev.firebaseapp.com/guide/subject), the subscribers can also be observers:

```js
subject.subscribe({
  next: (v) => console.log(`observerA: ${v}`),
});
subject.subscribe({
  next: (v) => console.log(`observerB: ${v}`),
});
```

Since the `Observer` class is provided to me and I can use `new Observer({next,error,complete})` or `new Observer(function)` to create an observer, I can let `Observer` to take care whether a subscriber is a function or an observer.

```js
class Subject {
  constructor() {
    this.observers = [];
  }

  subscribe(subscriber) {
    this.observers.push(new Observer(subscriber));
  }

  // ...
}
```

The `Subject`'s `next(value)/error(err)/complete()` is going to iterate through the registered observers and invoke the corresponding methods.

```js
class Subject {
  next(value) {
    for (const observer of this.observers) {
      observer.next(value);
    }
  }

  error(err) {
    for (const observer of this.observers) {
      observer.error(err);
    }
  }

  complete() {
    for (const observer of this.observers) {
      observer.complete();
    }
  }
}
```

🙋‍♀️🙋‍♂️ This approach doesn't handle the case where `next(value)/error(err)/complete()` of a `Subject` will be invoked as a plain function, which means the `this` binding will be lost, since class methods are not bound by default. In addition, the `subscribe` method should returns the observer, so that we can `unsubscribe`.

```js
const result = [];
const log = (item) => result.push(item);
const subject = new Subject();
const sub1 = subject.subscribe(log);
const sub2 = subject.subscribe(log);
const sub3 = subject.subscribe(log);

console.log(subject);

const observer = new Observable((subscriber) => {
  setTimeout(() => {
    subscriber.next(1);
    sub2.unsubscribe();
  }, 0);
  setTimeout(() => {
    subscriber.next(2);
  }, 100);
  setTimeout(() => subscriber.next(3), 200);
});
observer.subscribe(subject);
```

### Solution

```js
// You can use Observer which is bundled to your code

// class Observer {
//   // subscriber could one next function or a handler object {next, error, complete}
//   constructor(subscriber) { }
//   next(value) { }
//   error(error) { }
//   complete() {}
// }

class Subject {
  constructor() {
    this.observers = [];
    this.next = this.next.bind(this);
    this.error = this.error.bind(this);
    this.complete = this.complete.bind(this);
  }

  subscribe(subscriber) {
    const observer = new Observer(subscriber);
    this.observers.push(observer);
    return observer;
  }

  next(value) {
    for (const observer of this.observers) {
      observer.next(value);
    }
  }

  error(err) {
    for (const observer of this.observers) {
      observer.error(err);
    }
  }

  complete() {
    for (const observer of this.observers) {
      observer.complete();
    }
  }
}
```



# 72.implement-Observable-interval.md

### Problem Description

This is a follow-up on [57. create an Observable](https://bigfrontend.dev/problem/create-an-Observable).

Suppose you have solved [57. create an Observable](https://bigfrontend.dev/problem/create-an-Observable), here you are asked to implement a creation operator `interval()`.

From the [document](https://rxjs-dev.firebaseapp.com/api/index/function/interval), `interval()`

> Creates an Observable that emits sequential numbers every specified interval of time

```js
interval(1000).subscribe(console.log);
```

Above code prints 0, 1, 2 .... with an interval of 1 seconds.

**Note** Observable is already given for you, no need to create it.

#

### Understanding the problem

I am asked to write a function called `interval(period)`. The function should create an Observable that will emit an infinite sequence of ascending integers, starting from 0, every specified interval of time. The class `Observable` is provided to me.

#

### Approach

To create a Observable, I need to provide a callback as setup to the new `Observable` constructor, such as:

```js
const observable = new Observable((subscriber) => {
  subscriber.next(1);
  subscriber.next(2);
  setTimeout(() => {
    subscriber.next(3);
    subscriber.next(4);
    subscriber.complete();
  }, 100);
});
```

And in order to emit an infinite sequence of ascending integers, I can initialize a variable that is going to store the current integer.

### Solution

```js
/**
 * @param {number} period
 * @return {Observable}
 */
function interval(period) {
  let num = 0;
  return new Observable((subscriber) => {
    setInterval(() => {
      // subscriber.next(num++);
      subscriber.next(num);
      num++;
    }, period);
  });
}
```



# 73.implement-Observable-fromEvent.md

### Problem Description

This is a follow-up on [57. create an Observable](https://bigfrontend.dev/problem/create-an-Observable).

Suppose you have solved [57. create an Observable](https://bigfrontend.dev/problem/create-an-Observable), here you are asked to implement a creation operator `fromEvent()`( for DOM Event)

From the [document](https://rxjs-dev.firebaseapp.com/api/index/function/fromEvent), `fromEvent()`

> Creates an Observable that emits events of a specific type coming from the given event target.

Simply speaking, it is a util to attach event listener in Observable fashion.

```js
const source = fromEvent(node, 'click');
source.subscribe((e) => console.log(e));
```

When `node` is clicked, the event is logged.

**Note**

1. Observable is already given for you, no need to create it.
2. the event listener removal is handled by [add()](https://rxjs-dev.firebaseapp.com/api/index/class/Subscription#add), which is beyond our scope here, you can ignore that.

#

### Understanding the problem

I am asked to write a function `fromEvent` which takes in three parameters. The first parameter is a DOM element. The second parameter is a string representing a event type. The third parameter is optional which is a Boolean that specifies whether the event should be executed in the capturing or bubbling phase. The function should attach a event listener to the given DOM element with the specified event type. It should returns an Observable, so that we can use `observable.subscribe(func)` to subscribe a function to the observable. The function will be invoked when the event gets fired, and it will receive the event object. The `Observable` is provided for me, and I don't need to handle the removal of a event listener.

#

### Approach

Since, the `fromEvent` will returns a Observable, so I need to define the callback function that the new `Observable` constructor will receive. The callback function is going to receive a subscriber object.

```js
function fromEvent(/* parameters */) {
  return new Observable((subscriber) => {});
}
```

The callback function will get executed when we call `observable.subscribe(func)`. So I should attach the event listener to the given node in the callback function.

```js
function fromEvent(element, eventName, capture = false) {
  return new Observable((subscriber) => {
    element.addEventListener(eventName, (e) => {}, capture);
  });
}
```

The function passed to `observable.subscribe(func)` will be wrapped by a decorator. In the callback function I can access that function with `subscriber.next`. It gets invoked when the event gets fired, so I need to call it in the event handler and pass in the event object.

```js
// ...
element.addEventListener(
  eventName,
  (e) => {
    subscriber.next(e);
  },
  capture
);
// ...
```

### Solution

```js
/**
 * @param {HTMLElement} element
 * @param {string} eventName
 * @param {boolean} capture
 * @return {Observable}
 */
function fromEvent(element, eventName, capture = false) {
  return new Observable((subscriber) => {
    element.addEventListener(
      eventName,
      (e) => {
        subscriber.next(e);
      },
      capture
    );
  });
}
```



# 74.implement-Observable-Transformation-Operators.md

### Problem Description

This is a follow-up on [57. create an Observable](https://bigfrontend.dev/problem/create-an-Observable).

There are [a lot of operators](https://rxjs-dev.firebaseapp.com/guide/operators) for Observable, if we think of Observable as event stream, then modifying the stream is a common task, transformation operators are useful at this.

In this problem, you are asked to implement [map()](https://rxjs-dev.firebaseapp.com/api/operators/map), as the name indicates, it maps the value to another value thus creating a new event stream.

Here is an example.

```js
const source = Observable.from([1, 2, 3]);

map((x) => x * x)(source) // this transformer doubles numbers and create a new stream
  .subscribe(console.log);
// 1
// 4
// 9
```

Observable has `pipe()` method which could make this more readable.

```js
const source = Observable.from([1, 2, 3]);

source.pipe(map((x) => x * x)).subscribe(console.log);
// 1
// 4
// 9
```

**Note** Observable is already given for you, no need to create it.

#

### Understanding the problem

I am asked to write a function called `map()`. It should take in a function that is going to be called on each value emitted by the source Observable. It should return a function that will receive the source Observable and returns the transformed Observable. The class `Observable` is provided for me.

#

### Approach

In the `map` function, first I need to return a function which takes in an observable as source and returns the transformed observable.

```js
function map(transform) {
  return (sourceObservable) => {
    // return transformedObservable
  };
}
```

In a observable, the value that the observable emits is going to be passed to `subscriber.next(value)`. Therefore, in order to apply the `transform` function on each value the source observable emits, I need to wrap the `subscriber.next(value)`:

```js
// store the original subscriber.next
subscriber.next = (value) => {
  value = transform(value);
  // invoke the original subscriber.next passing
  // in the transformed value.
};
```

That means, I need to create a new `Observable` instance; wrap the `subscriber.next(value)` in the `setup` of the new `Observable` and invoke the source observable `subscriber` passing in the modified `subscriber`.

```js
return (sourceObservable) => {
  const transformedObservable = new Observable((subscriber) => {
    subscriber.next = (value) => {
      // ...
    };
    sourceObservable.subscribe(subscriber);
  });
  // return transformedObservable
};
```

Return the transformed observable at the end.

### Solution

```js
/**
 * @param {any} input
 * @return {(observable: Observable) => Observable}
 * returns a function which transform Observable
 */
function map(transform) {
  return (source) => {
    return new Observable((subscriber) => {
      const originalNext = subscriber.next;
      subscriber.next = (value) => {
        const newValue = transform(value);
        originalNext.call(subscriber, newValue);
      };
      source.subscribe(subscriber);
    });
  };
}
```



# 75.implement-BigInt-subtraction.md

### Problem Description

Luckily we already have built-in support of [BigInt](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt) in JavaScript, at least in some browsers.

```js
1000000000000000000000n - 999999999999999999999n;
// 1n
```

Suppose BigInt cannot be used, can you implement a string subtraction function by yourself?

```js
subtract('1000000000000000000000', '999999999999999999999');
// '1'
```

All input are valid **non-negative integer strings**, and the result is guaranteed to be non-negative.

#

### Understanding the problem

We are given two non-negative integers, `minuend` and `subtrahend` represented as string. We are asked to write a function that is going to return the difference of the two integers as a string.

The result is guaranteed to be non-negative, so it means `minuend` is for sure going to be greater than or equal to `subtrahend`.

We are not allowed to convert the input strings directly to numbers.

#

### Approach

We could solve the problem by subtracting the two integer strings digit by digit starting from the least significant digit and keeping track of the borrow from the previous step.

**Algorithm**

- Initialize a pointer `minuendIdx` to the end of `minuend` and a pointer `subtrahendIdx` to the end of `subtrahend`.

- Use a variable to remember the borrow from the previous subtraction. Initialize it to `0`.

- Initialize an empty array `result` that is going to hold the results of all the subtractions. The reason that we use an array rather than a string to store the results is because in most programming languages string is immutable, which means every time we append a character to a string, a brand new string needs to be created, which is an O(N) operation ([In JavaScript, `+=` is optimized](https://josephmate.github.io/java/javascript/stringbuilder/2020/07/27/javascript-does-not-need-stringbuilder.html). We are also going to store the results in reverse order: `[last significant digit, ..., most significant digit]`, since inserting a value at the beginning of an array takes O(N) time.

- Walk through the two integer strings simultaneously using the two pointers. The loop stops when we reach the beginning of `minuend`. This is because `minuend` is for sure going to be greater than or equal to `subtrahend`, which means the length of `subtrahend` can't be greater than the length of `minuend`.

  - Set `minuendDigit` to the digit from `minuend` at index `minuendIdx`. Subtract the previous borrow from `minuendDigit`.

  - Set `subtrahendDigit` to the digit from `subtrahend` at index `subtrahendIdx`. If `subtrahendIdx` has reached the beginning of `subtrahend`, set `subtrahendDigit` to `0`.

  - If `minuendDigit` is smaller than `subtrahendDigit`, add `10` to `minuendDigit`: `minuendDigit += 10`, and set borrow to `1`. Otherwise set borrow to `0`.

  - Subtract `subtrahendDigit` from `minuendDigit`. Push the result into the `result` array.

- Now we need to remove the "leading" zeros from the result if they are present. For example, if both `minuend` and `subtrahend` are `100`, the `result` array is going to be `[0, 0, 0]` when the loop terminates. We could keep removing the last number from the `result` array until the last number of the array is not equal to `0` or the `result` array contains only one element.

- Reverse the `result` array. Join the reversed array into a string and return it.

### Implementation

```js
/**
 * @param {string} minuend
 * @param {string} subtrahend
 * @return {string}
 */
function subtract(minuend, subtrahend) {
  let minuendIdx = minuend.length - 1;
  let subtrahendIdx = subtrahend.length - 1;
  const result = [];
  let borrow = 0;

  while (minuendIdx >= 0) {
    let minuendDigit = Number(minuend[minuendIdx]) - borrow;
    const subtrahendDigit = Number(subtrahend[subtrahendIdx] ?? 0);

    if (minuendDigit < subtrahendDigit) {
      borrow = 1;
      minuendDigit += 10;
    } else {
      borrow = 0;
    }

    result.push(minuendDigit - subtrahendDigit);

    minuendIdx--;
    subtrahendIdx--;
  }

  while (result[result.length - 1] === 0 && result.length > 1) {
    result.pop();
  }

  return result.reverse().join('');
}
```

### Complexity Analysis

Let N be the length of the first integer string.

- Time Complexity: O(N).

- Space Complexity: O(N).



# 76.implement-BigInt-addition-with-sign.md

### Problem Description

This is a follow-up on [62. implement BigInt addition](https://bigfrontend.dev/problem/add-BigInt-string).

You are asked to implement a string addition function, **with possible negative integers**. Also, '+' plus sign should also be supported

```js
add('-999999999999999999', '-1');
// '-1000000000000000000'

add('-999999999999999999', '+1');
// '-999999999999999998'
```

Don't use BigInt directly, it is not our goal here.

#

### Understanding the problem

We are given two integers, `num1` and `num2` represented as string. We are asked to write a function that is going to return the sum of the two integers as a string. The function should work for both positive and negative integers and it should support positive integers that start with the plus sign, such as `'+1'`. We cannot convert the strings directly into numbers.

#

### Approach

We can solve the problem by 'adding' these two strings digit by digit.

### Implementation

```js
/**
 * @param {string} num1
 * @param {string} num2
 * @return {string}
 */
function add(num1, num2) {
  const num1IsNegative = isNegative(num1);
  const num2IsNegative = isNegative(num2);
  num1 = trimSign(num1);
  num2 = trimSign(num2);

  if (num1IsNegative && num2IsNegative) {
    return '-' + addPositives(num1, num2);
  }

  if (!num1IsNegative && !num2IsNegative) {
    return addPositives(num1, num2);
  }

  const num1IsGreater = isGreater(num1, num2);
  let result;

  if (num1IsGreater) {
    result = subtractPositives(num1, num2);
    return num1IsNegative ? '-' + result : result;
  }

  result = subtractPositives(num2, num1);
  return num2IsNegative ? '-' + result : result;
}

function addPositives(num1, num2) {
  let p1 = num1.length - 1;
  let p2 = num2.length - 1;
  let carry = 0;
  const result = [];

  while (p1 >= 0 || p2 >= 0) {
    const digit1 = Number(num1[p1] ?? 0);
    const digit2 = Number(num2[p2] ?? 0);
    const sum = digit1 + digit2 + carry;

    carry = Math.floor(sum / 10);
    result.push(sum % 10);

    p1--;
    p2--;
  }

  if (carry > 0) result.push(carry);

  return result.reverse().join('');
}

function subtractPositives(minuend, subtrahend) {
  let minuendIdx = minuend.length - 1;
  let subtrahendIdx = subtrahend.length - 1;
  const result = [];
  let borrow = 0;

  while (minuendIdx >= 0) {
    let minuendDigit = Number(minuend[minuendIdx]) - borrow;
    const subtrahendDigit = Number(subtrahend[subtrahendIdx] ?? 0);

    if (minuendDigit < subtrahendDigit) {
      borrow = 1;
      minuendDigit += 10;
    } else {
      borrow = 0;
    }

    result.push(minuendDigit - subtrahendDigit);

    minuendIdx--;
    subtrahendIdx--;
  }

  while (result[result.length - 1] === 0 && result.length > 1) {
    result.pop();
  }

  return result.reverse().join('');
}

function isGreater(num1, num2) {
  if (num1.length !== num2.length) {
    return num1.length > num2.length;
  }

  let p1 = 0;
  let p2 = 0;

  while (p1 < num1.length - 1 && num1[p1] === num2[p2]) {
    p1++;
    p2++;
  }

  return num1[p1] >= num2[p2];
}

function isNegative(num) {
  return num[0] === '-';
}

function trimSign(num) {
  if (num[0] === '+' || num[0] === '-') {
    return num.substring(1);
  }

  return num;
}
```



# 78.convert-HEX-color-to-RGBA.md

### Problem Description

Suppose you write some CSS code, you need to set [colors](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value). You can choose hexadecimal notation `#fff` or Functional notation `rgba(255,255,255,1)`.

Can you write a function to convert hexadecimal notation to functional notation?

```js
hexToRgb('#fff');
// 'rgba(255,255,255,1)'
```

1. Alpha channel should have **maximum 2 digits after decimal point, round up if needed**.
2. Don't forget to do input validation

#

### Understanding the problem

I am asked to write a function that converts hexadecimal color notation into rgba color notation. If the input does not contain the value that represents the alpha value of the color, the alpha value in the output should be `1`.

#

### Approach

- validate input.
  Hexadecimal color notation consists of a '#" symbol, followed by 6 characters/digits, or 3 characters/digits. The 3-digit color notation is a shorthand for the 6-digit notation, where duplicate digits for each color component is combined into one. The 6-digit notation represents an opaque color. If we want to set alpha transparency to the color, we need to append another two digits to the notation, which specifies transparency in percentage. If the two characters are same, then we can combine them into one. However, in browser, only 8-digit and 4-digit work.

  So first check if the string starts with '#' and check the number of the following digits. If the the '#' symbol is not followed by 3 or 4 or 6 or 8 digits, then it is not a valid input.

- Convert 3-digit notation into 6-digit notation or 4-digit notation into 8-digit notation, by copying each character next to each to other. After the conversion, if the number of digits is 6, append `'FF'` to it.

- Initialize an empty array to store the results.

- Loop over the string, process two digits at a time. At each iteration, convert the hexadecimal into decimal and append the result to the array. If the conversion fails, break out of the entire function, because it means the input string is invalid. If I am at index `6`, after the conversion, divide the result by `255` to get alpha value and round the result up to 2 digits after decimal points.

- Concatenate the elements in the array of results into string, separated by commas. Insert `'rgba(` at the beginning and a right parenthese at the end.

### Solution

```js
/**
 * @param {string} hex
 * @return {string}
 */
function hexToRgba(hex) {
  if (!validateInput(hex)) throw new Error('Invalid input.');

  let digits = hex.slice(1);
  if (digits.length === 3 || digits.length === 4) {
    digits = digits.replace(/\w/g, (digit) => digit + digit);
  }

  if (digits.length === 6) digits += 'FF';

  const rgbas = [];

  for (let idx = 0; idx < digits.length; idx += 2) {
    const hex = digits[idx] + digits[idx + 1];
    const decimal = parseInt(hex, 16);
    if (isNaN(decimal)) throw new Error('Invalid input.');

    const isAlpha = idx === 6;
    if (isAlpha) {
      rgbas.push(roundUpToTwoDecimalPlaces(decimal / 255));
      continue;
    }

    rgbas.push(decimal);
  }

  return 'rgba(' + rgbas.join(',') + ')';
}

function validateInput(hex) {
  if (typeof hex !== 'string' || !hex.startsWith('#')) {
    return false;
  }

  if (
    hex.length !== 4 &&
    hex.length !== 5 &&
    hex.length !== 7 &&
    hex.length !== 9
  ) {
    return false;
  }

  return true;
}

function roundUpToTwoDecimalPlaces(floatNumber) {
  return Math.round(floatNumber * 100) / 100;
}
```



# 79.convert-snake_case-to-camelCase.md

### Problem Description

Do you prefer [snake_case](https://en.wikipedia.org/wiki/Snake_case) or [camelCase](https://en.wikipedia.org/wiki/Camel_case) ?

Anyway, please create a function to convert snake_case to camcelCase.

```js
snakeToCamel('snake_case');
// 'snakeCase'
snakeToCamel('is_flag_on');
// 'isFlagOn'
snakeToCamel('is_IOS_or_Android');
// 'isIOSOrAndroid'
snakeToCamel('_first_underscore');
// '_firstUnderscore'
snakeToCamel('last_underscore_');
// 'lastUnderscore_'
snakeToCamel('_double__underscore_');
// '_double__underscore_'
```

contiguous underscore `__`, leading underscore `_a`, and trailing underscore `a_` should be kept untouched.

#

### Solution

```js
/**
 * @param {string} str
 * @return {string}
 */
function snakeToCamel(str) {
  return str.replace(/[a-z]_[a-z]/gi, (match) => {
    return match[0] + match[2].toUpperCase();
  });
}
```

#

### Refactored Solution

```js
/**
 * @param {string} str
 * @return {string}
 */
function snakeToCamel(str) {
  return str.replace(/([^_])_([^_])/g, (_, before, after) => {
    return before + after.toUpperCase();
  });
}
```

#

### Solution with Lookbehind

```js
/**
 * @param {string} str
 * @return {string}
 */
function snakeToCamel(str) {
  return str.replace(/(?<=[a-z])_[a-z]/gi, (match) => {
    return match[1].toUpperCase();
  });
}
```

#

### Reference

[Problem Discuss](https://bigfrontend.dev/problem/convert-snake_case-to-camelCase/discuss)



# 8.can-you-shuffle-an-array.md

### Problem Description

How would you implement a `shuffle()` ?

When passed with an array, it should modify the array inline to generate a randomly picked permutation at the same probability.

for an array like this:

```js
const arr = [1, 2, 3, 4];
```

there would be possibly 4! = 24 permutations

```
[1, 2, 3, 4]
[1, 2, 4, 3]
[1, 3, 2, 4]
[1, 3, 4, 2]
[1, 4, 2, 3]
[1, 4, 3, 2]
[2, 1, 3, 4]
[2, 1, 4, 3]
[2, 3, 1, 4]
[2, 3, 4, 1]
[2, 4, 1, 3]
[2, 4, 3, 1]
[3, 1, 2, 4]
[3, 1, 4, 2]
[3, 2, 1, 4]
[3, 2, 4, 1]
[3, 4, 1, 2]
[3, 4, 2, 1]
[4, 1, 2, 3]
[4, 1, 3, 2]
[4, 2, 1, 3]
[4, 2, 3, 1]
[4, 3, 1, 2]
[4, 3, 2, 1]
```

your `shuffle()` should transform the array in one of the above array, at the same 1/24 probability.

**notes**

Your `shuffle()` will be called multiple times, to calculate the probability on each possible result, and test again [standard deviation](https://simple.wikipedia.org/wiki/Standard_deviation)

ref: https://javascript.info/task/shuffle

#

### Solution

```js
/**
 * @param {any[]} arr
 */
function shuffle(arr) {
  // Fisher-Yates shuffle
  for (let i = arr.length - 1; i > 0; i--) {
    const randomIndex = Math.floor(Math.random() * (i + 1));
    [arr[randomIndex], arr[i]] = [arr[i], arr[randomIndex]];
  }
}
```

#

### Reference

[Fisher–Yates shuffle](https://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle)



# 80.implement-your-own-URLSearchParams.md

### Problem Description

When we want to extract parameters from query string, [URLSearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams) could be very handy.

Can you implement `MyURLSearchParams` which works the same ?

```js
const params = new MyURLSearchParams('?a=1&a=2&b=2');
params.get('a'); // '1'
params.getAll('a'); // ['1', '2']
params.get('b'); // '2'
params.getAll('b'); // ['2']

params.append('a', 3);
params.set('b', '3');
params.toString(); // 'a=1&a=2&b=3&a=3'
```

There are [a few methods](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams) on URLSearchParams, please implement them all.

#

### Understanding the problem

I am asked to implement a class called `MyURLSearchParams` that is going to work the same as the `URLSearchParams` API.

#

### Approach

- The constructor of the class `URLSearchParams` accepts a query string of a URL as argument, such as `'?foo=1&bar=2'` or `'foo=1&bar=2'`. The `?` at the beginning of the string will be ignored. However, the input string doesn't have to be in that format:

  ```js
  const params = new URLSearchParams('?sss?123');
  console.log(params.toString()); // => 'sss%3F123='
  ```

  It also accepts an array of pairs, representing names/values, such as `[['foo', '1'], ['bar', '2']]`, or an object, such as `{'foo': '1', 'bar': '2'}`. If the value of a search parameter is not a string, it will be converted into a string value:

  ```js
  const params1 = new URLSearchParams({ a: 1 });
  console.log(params1.get('a')); // => '1'

  const params2 = new URLSearchParams([['b', 2]]);
  console.log(params2.get('b')); // => '2'
  ```

  - If the input is not an array of pairs, such as `[2]`, `[['foo']]` or `[['foo', '1', 'bar']]`, it will throw an error.

    ```js
    const params2 = new URLSearchParams([2]);
    // => TypeError: Failed to construct 'URLSearchParams':
    // The provided value cannot be converted to a sequence.
    ```

  - If the input is an empty object or a nested object, it will not throw an error.

    ```js
    const params1 = new URLSearchParams({});
    console.log(params1.toString());
    // => ''

    const params2 = new URLSearchParams({ a: { b: 1 } });
    console.log(params2.toString()); // => 'a=%5Bobject+Object%5D'
    params2.forEach((value, key) => {
      console.log(key, value); // => 'a', '[object Object]'
    });
    ```

  The constructor also accepts other types as argument except for `Set`:

  ```js
  const params1 = new URLSearchParams(true);
  console.log(params1.toString()); // => 'true='

  const params2 = new URLSearchParams(() => {});
  console.log(params2.toString()); // => ''
  params2.forEach((value, key) => {
    console.log(value, key); // logs nothing
  });

  const set = new Set();
  set.add(1);
  const params3 = new URLSearchParams(set);
  // => TypeError: Failed to construct 'URLSearchParams':
  // The provided value cannot be converted to a sequence.
  ```

  Therefore, in the constructor of `MyURLSearchParams`, I would first detect the type of the input and process the input if needed:

  - If the input is primitive, convert it to string. Drop the `?` if the string has. Then split the string into an array by `&` as delimiter. Finally, loop over every substrings in the array and map them into name/value pair.
  - If the input is an array, check if it is a sequence of pairs. Then convert all the values into strings.
  - If the input is an object literal, check if it is a `Set`. Then convert all the values into strings.

  Then initialize a new `Map` to store the names and values of the search parameters, in which each key is the search parameter and the values are an array of values associated with each search parameter.

- `URLSearchParams.get(name)` returns the first value associated with the given search parameter. If the given search parameter is not found, it returns `null`. If the given search parameter is not a string, it will be converted into a string:

  ```js
  const params = new URLSearchParams([['2', 3]]);
  console.log(params.get(2)); // => '3'
  ```

  First, `MySearchParams.get(name)` is going to convert the input value into a string. Then it will look up the given search parameter in the `Map`, retrieve the first value in its values array and returns the value.

- `URLSearchParams.getAll(name)` returns all the values associated with the given search parameter. If the given search parameter is not found, it returns an empty array. If the given search parameter is not a string, it will be converted into a string:

  ```js
  const params = new URLSearchParams([['2', 3]]);
  console.log(params.getAll(2)); // => ['3']
  ```

  First, `MySearchParams.getAll(name)` is going to convert the input value into a string value, then it will retrieve the values array of the search parameter in the `Map` and return the array.

- `URLSearchParams.append(name, value)` appends a specified key/value pair as a new search parameter.

  ```js
  const params = new URLSearchParams({ a: 1, b: 1 });
  params.append('d', 5);
  console.log(params.toString());
  // => 'a=1&b=1&d=5'
  ```

  If the key already exists, the new value is added to that search parameter.

  ```js
  const params = new URLSearchParams({ a: 1, b: 1 });
  params.append('a', 5);
  console.log(params.getAll('a'));
  // => ['1', '5']
  ```

  `MySearchParams.append(name, value)` is going to append the given key/value pair to the `Map`.

  However, when using `URLSearchParams.toString()` to convert the search parameters object to string, the order of the search parameters is kept.

  ```js
  const params = new URLSearchParams({ a: 1, b: 1 });
  params.append('a', 5);
  console.log(params.toString());
  // => 'a=1&b=1&a=5'
  ```

  Therefore, I need to keep track of the order of the search parameters. Without that, the order of the search parameters will be lost when a new value is added to a search parameter that already exists.

  ```js
  class MySearchParams {
    constructor(init) {
      this.searchParamsMap = new Map();
      // Store the `init` in the Map.
    }

    append(name, value) {
      const values = this.searchParamsMap.get(name);
      if (!values) {
        this.searchParamsMap.set(name, (values = []));
      }

      values.push(value);
    }

    toString() {
      // Convert the Map to a string.
    }
  }

  const params = new MySearchParams({ a: 1, b: 1 });
  params.append('a', 5);
  console.log(params.toString());
  // => 'a=1&a=5&b=1'
  ```

  There is no way of knowing that `b=1` should come before `a=5`. Thus, in addition to the `Map`, I would store the query string array; every time a key/value pair is appended, it will also appended to that array. I could use an array or a `Set` alone, but then retrieving the value of a search parameter can not be done in constant time on average.

- `URLSearchParams.set(name, value)` sets the value associated with a given search parameter to the given value. If the search parameter has multiple values, all of them will be deleted. If the given search parameter doesn't exist, it will be created.

  ```js
  const params = new URLSearchParams('a=1&b=2&a=3');
  params.set('a', 5);
  console.log(params.toString()); // => 'a=5&b=2'

  params.set('c', 6);
  console.log(params.toString()); // => 'a=5&b=2&c=6'
  ```

  Therefore, `MySearchParams.set(name, value)` would set the given search parameter to the new value, or create a new key/value pair in the `Map` if it doesn't exist, and update the query string array. It is going to iterate through each pair in the array, update the first pair in which the given search parameter is found and delete the rest pairs that have the given search parameter, if such pair doesn't exit, create a new pair and append it to the array.

- `URLSearchParams.toString()` converts a `URLSearchParams` object to a query string in which special characters are URL-encoded. If there are no search parameters, it returns an empty string.

  First, `MySearchParams.toString()` is going to encode every pair in the query string array and concatenate them into a string separated by `=`, then it is going to concatenate the entire array into a string separated by `&` and return the string.

- `URLSearchParams.delete(name)` deletes the given search parameter and all its associated value. So `MySearchParams.delete(name)` is going to delete the given search parameter in the `Map` and in the query string array.

- `URLSearchParams.has(name)` returns a Boolean indicating whether a given search parameter exists. So `MySearchParams.has(name)` is going to check whether a given search parameter is already a key in the `Map` and return the result.

- `URLSearchParams.sort()` sort all key/value pairs by the keys:

  ```js
  const params = new URLSearchParams('ad=4&bc=2&ab=3');
  params.sort();
  console.log(params.toString()); // => 'ab=3&ad=4&bc=2'
  ```

  `MySearchParams.sort()` is going to sort the query string array by the keys in the pairs.

- Since we can use `for const [key, value] of params` to iterate through every key/value pair, the `MySearchParams` class is going to have the `[Symbol.iterator]` property.

  ```js
  const params = new URLSearchParams('a=1&b=2&a=3');

  for (const [key, value] of params) {
    console.log(key, value);
  }
  // Logs:
  // 'a', '1'
  // 'b', '2'
  // 'a', '3'
  ```

  The `[Symbol.iterator]()` method in `MySearchParams` class would call query string array's `[Symbol.iterator]` method and return its returned value.

- We can also use `URLSearchParams.entries()` to iterate through all key/value pair, so `MySearchParams.entries()` is going to call its `[Symbol.iterator]` method and return the result.

- `URLSearchParams.keys()` allows iteration through all keys of the key/value pairs:

  ```js
  const params = new URLSearchParams('a=1&b=2&a=3');

  for (const key of params.keys()) {
    console.log(key);
  }
  // Logs:
  // 'a'
  // 'b'
  // 'a'
  ```

  `MySearchParams.keys()` would return an object containing `[Symbol.iterator]` method, so that `for...of` can call the method when it starts. The `[Symbol.iterator]` is going to return an iterator, whose `next` method is going to generate the key for each iteration from the query string array.

- `URLSearchParams.values()` allows iteration through all values of the key/value pairs:

  ```js
  const params = new URLSearchParams('a=1&b=2&a=3');

  for (const value of params.values()) {
    console.log(value);
  }
  // Logs:
  // '1'
  // '2'
  // '3'
  ```

  `MySearchParams.values()` is going to return an object containing `[Symbol.iterator]` method. The `[Symbol.iterator]` method would return an iterator and its `next` method is going to generate the value for each iteration from the query string array.

- `URLSearchParams.forEach(callback)` allows iteration through all key/value pairs via a callback function:

  ```js
  const params = new URLSearchParams('a=1&b=2&a=3');

  params.forEach(function (value, key) {
    console.log(value, key);
  });
  ```

  `MySearchParams.forEach(callback)` is going to iterate through each key/value pair in the query string array and execute the callback function passing in the value and the key.

### Solution

```js
class MyURLSearchParams {
  /**
   * @params {string} init
   */
  constructor(init) {
    this._searchParamsArray = [];
    this._searchParamsMap = new Map();

    if (typeof init !== 'object') {
      this._constructSearchParamsFromString(init);
    } else if (Array.isArray(init)) {
      this._constructSearchParamsFromArray(init);
    } else {
      this._constructSearchParamsFromObject(init);
    }
  }

  /**
   * @params {string} name
   * @params {any} value
   */
  append(name, value) {
    name = String(name);
    value = String(value);

    this._searchParamsArray.push([name, value]);
    this._storeParamInSearchParamsMap(name, value);
  }

  /**
   * @params {string} name
   */
  delete(name) {
    name = String(name);

    this._searchParamsMap.delete(name);
    this._searchParamsArray = this._searchParamsArray.filter(
      (param) => param[0] !== name
    );
  }

  /**
   * @returns {Iterator}
   */
  entries() {
    return this[Symbol.iterator]();
  }

  /**
   * @param {(value, key) => void} callback
   */
  forEach(callback) {
    this._searchParamsArray.forEach((param) => {
      callback(param[1], param[0]);
    });
  }

  /**
   * @param {string} name
   * returns the first value of the name
   */
  get(name) {
    name = String(name);
    const value = this._searchParamsMap.get(name);
    return value ? value[0] : null;
  }

  /**
   * @param {string} name
   * @return {string[]}
   * returns the value list of the name
   */
  getAll(name) {
    name = String(name);
    const value = this._searchParamsMap.get(name);
    return value ? value : [];
  }

  /**
   * @params {string} name
   * @return {boolean}
   */
  has(name) {
    name = String(name);
    return this._searchParamsMap.has(name);
  }

  /**
   * @return {Iterator}
   */
  keys() {
    return this._createItertor({ shouldReturnKey: true });
  }

  /**
   * @param {string} name
   * @param {any} value
   */
  set(name, value) {
    name = String(name);
    value = String(value);

    if (!this.has(name)) {
      this.append(name, value);
      return;
    }

    this._searchParamsMap.set(name, [value]);
    const searchParamIdx = this._searchParamsArray.findIndex(
      (param) => param[0] === name
    );
    this._searchParamsArray[searchParamIdx][1] = value;
    this._searchParamsArray = this._searchParamsArray.filter(
      (param, idx) => param[0] !== name || idx === searchParamIdx
    );
  }

  // sor all key/value pairs based on the keys
  sort() {
    this._searchParamsArray.sort((a, b) => {
      const keyA = a[0];
      const keyB = b[0];

      if (keyA < keyB) return -1;

      if (keyA > keyB) return 1;

      return 0;
    });
  }

  /**
   * @return {string}
   */
  toString() {
    return this._searchParamsArray
      .map(([key, value]) => {
        return `${encodeURIComponent(key)}=${encodeURIComponent(value)}`;
      })
      .join('&');
  }

  /**
   * @return {Iterator} values
   */
  values() {
    return this._createItertor({ shouldReturnKey: false });
  }

  [Symbol.iterator]() {
    return this._searchParamsArray[Symbol.iterator]();
  }

  _constructSearchParamsFromString(init) {
    let searchParams = String(init);
    if (searchParams.startsWith('?')) {
      searchParams = searchParams.slice(1);
    }

    searchParams.split('&').forEach((param) => {
      let [key, value] = param.split('=');
      if (!value) {
        value = '';
      }
      this._searchParamsArray.push([key, value]);
      this._storeParamInSearchParamsMap(key, value);
    });
  }

  _constructSearchParamsFromArray(init) {
    for (const param of init) {
      if (!Array.isArray(param || param.length !== 2)) {
        throw new Error("Failed to construct 'URLSearchParams'");
      }

      const key = String(param[0]);
      const value = String(param[1]);

      this._searchParamsArray.push([key, value]);
      this._storeParamInSearchParamsMap(key, value);
    }
  }

  _constructSearchParamsFromObject(init) {
    if (init instanceof Set) {
      throw new Error("Failed to construct 'URLSearchParams'");
    }

    for (const key of Object.keys(init)) {
      const value = String(init[key]);

      this._searchParamsArray.push([key, value]);
      this._storeParamInSearchParamsMap(key, value);
    }
  }

  _storeParamInSearchParamsMap(key, value) {
    let values = this._searchParamsMap.get(key);
    if (!values) {
      values = [];
      this._searchParamsMap.set(key, values);
    }
    values.push(value);
  }

  _createItertor({ shouldReturnKey }) {
    const iterable = {
      idx: 0,
      searchParamsArray: this._searchParamsArray,
      shouldReturnKey,
      next() {
        if (this.idx === this.searchParamsArray.length) {
          return {
            done: true,
          };
        }

        const searchParam = this.searchParamsArray[this.idx];
        const value = shouldReturnKey ? searchParam[0] : searchParam[1];
        this.idx++;
        return {
          done: false,
          value,
        };
      },
    };

    return {
      [Symbol.iterator]() {
        return iterable;
      },
    };
  }
}
```



# 81.merge-sorted-arrays.md

### Problem Description

You are given a list of sorted non-descending integer arrays, write a function to merge them into one sorted non-descending array.

```js
merge([
  [1, 1, 1, 100, 1000, 10000],
  [1, 2, 2, 2, 200, 200, 1000],
  [1000000, 10000001],
  [2, 3, 3],
]);
// [1,1,1,1,2,2,2,2,3,3,100,200,200,1000,1000,10000,1000000,10000001]
```

What is time complexity of your solution?

#

### Understanding the problem

Given a list of sorted arrays of integers, which are all in non-descending order, I am asked to write a function that is going to merge them into one array sorted in the same order.

#

### Approach

I can solve the problem by using divide-and-conquer approach. Divide the list of sorted arrays in half, then keep dividing the two sub-lists of sorted arrays in half, until the sub-list contains only one array, cause if there is only one sorted array in the list, I can simply return that array. Then I can merge the two sorted arrays from the previous step into one and return the merged array. Keep merging the two arrays from the previous step until all arrays are merged.

To merge two sorted array I can use two pointers approach. Initially, point the first pointer to the start of the first array and the second pointer to the start of the second array, loop until both pointers point to the ends of the arrays. At each iteration, compare the elements in the two arrays the two pointers point to, if the element in the first array is smaller than the element in the second array, push the element from the first array into the empty array and move the pointer of that element to right by 1; otherwise push the element from the second array and move its pointer to right. If one of these two elements is `undefined`, set it to `Infinity`.

### Solution

```js
/**
 * @param {number[][]} arrList
 * non-descending integer array
 * @return {number[]}
 */
function merge(arrList) {
  return mergeImpl(arrList, 0, arrList.length - 1);
}

function mergeImpl(arrList, start, end) {
  if (start >= end) return arrList[end] || [];

  const mid = Math.floor((start + end) / 2);

  const left = mergeImpl(arrList, start, mid);
  const right = mergeImpl(arrList, mid + 1, end);
  return mergeSort(left, right);
}

function mergeSort(arrOne, arrTwo) {
  const mergedArr = [];
  let idxOne = 0;
  let idxTwo = 0;

  while (idxOne !== arrOne.length || idxTwo !== arrTwo.length) {
    const firstElement = arrOne[idxOne] || Infinity;
    const secondElement = arrTwo[idxTwo] || Infinity;

    if (firstElement < secondElement) {
      mergedArr.push(firstElement);
      idxOne++;
    } else {
      mergedArr.push(secondElement);
      idxTwo++;
    }
  }

  return mergedArr;
}
```



# 82.find-available-meeting-slots.md

### Problem Description

`[start, end]` is a time interval, with all integers from 0 to 24.

Given schedules for all team members,

<!--prettier-ignore-->
```js
[
  [[13, 15], [11, 12], [10, 13]], //schedule for member 1
  [[8, 9]], // schedule for member 2
  [[13, 18]] // schedule for member 3
]
```

You need to create a function `findMeetingSlots()` to return the time slots available for all the members to have a meeting.

For the input above, below slots should be returned

<!--prettier-ignore-->
```js
[[0, 8],[9, 10],[18, 24]]
```

**Notes**

1. the input schedule intervals might be unsorted
2. one member's schedule might have overlapping intervals.

#

### Understanding the problem

I am given an array of schedules for team members. Each schedule is an array of time blocks, which are in the form of `[start, end]`, and both `start` and `end` are integers from 0 to 24. I am asked to write a function that is going to return an array of all the time blocks during which all the team members can have a meeting. The schedules might not be sorted by the start time and they might have overlapping time blocks.

#

### Approach

To solve the problem I would first merge the schedules for the team members into one array and sorted the array by start time in ascending order, so that I can find the time blocks available for all team members. Then I would initialize an empty array that is going to store the available time blocks and a variable called `currentEndTime` that is going to keep track of the current end time to handle the overlapping time blocks; initially, set it to `0`. I will loop through the sorted array of time blocks. At each time block, I am going to compare its start time to the `currentEndTime`, if it is greater than the `currentEndTime`, then it means I have found a available time block, where the start time is `currentEndTime` and the end time is the start time of the current time block; otherwise, compare the `currentEndTime` to the end time of the current time block, if it is smaller than the end time, update `currentEndTime` to the end time of the current time block. When I get out of the loop, compare the `currentEndTime` to `24`, if it is smaller than `24`, push the time block that starts at `currentEndTime` and ends at ``24` to the array of available time blocks. Finally, return the array of available time blocks.

### Time & Space Complexity

O(nlog(n)) - time | O(n) space, where n is total number of time blocks(time intervals) in the input array.

### Solution

```js
// type Interval = [number, number]

/**
 * @param {Interval[][]} schedules
 * @return {Interval[]}
 */
function findMeetingSlots(schedules) {
  const mergedSchedules = mergeSchedules(schedules);
  mergedSchedules.sort((a, b) => a[0] - b[0]);

  const availableTimeBlocks = [];
  let currentEndTime = 0;

  for (const timeBlock of mergedSchedules) {
    const [startTime, endTime] = timeBlock;

    if (startTime > currentEndTime) {
      availableTimeBlocks.push([currentEndTime, startTime]);
    }

    if (endTime > currentEndTime) {
      currentEndTime = endTime;
    }
  }

  if (currentEndTime < 24) {
    availableTimeBlocks.push([currentEndTime, 24]);
  }

  return availableTimeBlocks;
}

function mergeSchedules(schedules) {
  const mergedSchedules = [];
  for (const schedule of schedules) {
    mergedSchedules.push(...schedule);
  }
  return mergedSchedules;
}
```

#

### Approach without Sorting

Instead of merging the schedules into one array and then sorting it, I could create an array of hours where the indices are every hour of the day and the values are going to be 0, then I am going to loop through the input array and store start times and end times into the array of hours: for the start time I will increase the value at that hour by 1 and for the end time I will decrease the value by 1, so that I can differentiate start time from end time in the array of hours. For instance, if the schedules are: `[[13, 15], [11, 12], [10, 13]]`, `[[8, 9]]` and `[[13, 18]]`, the array of hours is going to be:

<!--prettier-ignore-->
```js
// 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24
[  0, 0, 0, 0, 0, 0, 0, 0, 1, -1, 1,  1, -1,  1,  0, -1,  0,  0, -1,  0,  0,  0,  0,  0, 0 ]
```

If the schedules are `[[9, 10], [12, 13], [16, 18]]` and `[[10, 11], [12, 14], [14, 15], [16, 17]]`, the array of hours is going to be:

<!--prettier-ignore-->
```js
// 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24
[  0, 0, 0, 0, 0, 0, 0, 0, 0, 1,  0, -1,  2, -1,  0, -1,  2, -1, -1,  0,  0,  0,  0,  0, 0 ]
```

I can then initialize a variable called `busyTimeBlockCounter` that is going to keep track of the time block, initially set it to 0. From the arrays of hours, I can notice that if I loop though the array of hours and add each value in the array of hours to `busyTimeBlockCounter`, when I get to a value that is equal to or greater than `1` and `busyTimeBlockCounter` is equal to `0`, then I have found an available time block:

```
  0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24
[ 0, 0, 0, 0, 0, 0, 0, 0, 1, -1, 1,  1, -1,  1,  0, -1,  0,  0, -1,  0,  0,  0,  0,  0, 0 ]
                          ^
                          i

busyTimeBlockCounter: 0
available time blocks: [0, 8]
```

```
  0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24
[ 0, 0, 0, 0, 0, 0, 0, 0, 1, -1, 1,  1, -1,  1,  0, -1,  0,  0, -1,  0,  0,  0,  0,  0, 0 ]
                          ^
                          i

busyTimeBlockCounter: 0 + 1 = 1;
available time blocks: [0, 8]
```

```
  0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24
[ 0, 0, 0, 0, 0, 0, 0, 0, 1, -1, 1,  1, -1,  1,  0, -1,  0,  0, -1,  0,  0,  0,  0,  0, 0 ]
                              ^
                              i

busyTimeBlockCounter: 1 - 1 = 0;
available time blocks: [0, 8]
```

```
  0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24
[ 0, 0, 0, 0, 0, 0, 0, 0, 1, -1, 1,  1, -1,  1,  0, -1,  0,  0, -1,  0,  0,  0,  0,  0, 0 ]
                                 ^
                                 i

busyTimeBlockCounter: 1 - 1 = 0;
available time blocks: [0, 8], [9, 10]
```

```
  0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24
[ 0, 0, 0, 0, 0, 0, 0, 0, 1, -1, 1,  1, -1,  1,  0, -1,  0,  0, -1,  0,  0,  0,  0,  0, 0 ]
                                 ^
                                 i

busyTimeBlockCounter:  0 + 1 = 1;
available time blocks: [0, 8], [9, 10]
```

```
  0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24
[ 0, 0, 0, 0, 0, 0, 0, 0, 1, -1, 1,  1, -1,  1,  0, -1,  0,  0, -1,  0,  0,  0,  0,  0, 0 ]
                                     ^
                                     i

busyTimeBlockCounter:  1 + 1 = 2;
available time blocks: [0, 8], [9, 10]
```

```
  0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24
[ 0, 0, 0, 0, 0, 0, 0, 0, 1, -1, 1,  1, -1,  1,  0, -1,  0,  0, -1,  0,  0,  0,  0,  0, 0 ]
                                         ^
                                         i

busyTimeBlockCounter:  2 - 1 = 1;
available time blocks: [0, 8], [9, 10]
```

```
  0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24
[ 0, 0, 0, 0, 0, 0, 0, 0, 1, -1, 1,  1, -1,  1,  0, -1,  0,  0, -1,  0,  0,  0,  0,  0, 0 ]
                                             ^
                                             i

busyTimeBlockCounter:  1 + 1 = 2;
available time blocks: [0, 8], [9, 10]
```

...

```
  0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24
[ 0, 0, 0, 0, 0, 0, 0, 0, 1, -1, 1,  1, -1,  1,  0, -1,  0,  0, -1,  0,  0,  0,  0,  0, 0 ]
                                                                 ^
                                                                 i

busyTimeBlockCounter:  2 - 1 - 1 = 0;
available time blocks: [0, 8], [9, 10], [18, 24]
```

So I also need to initialize a variable that is going to keep track of the start time of a available time block; initially set it to `0`. While going through the array of hours, if I get to a value that is not `0` and after adding it to `busyTimeBlockCounter`, `busyTimeBlockCounter` becomes `0`, then the index that the value is at is the start time of a available time block.

When I get out of the loop and the start time of a available time block is less than `24`, then it means a time block that starts at that time and ends at `24` is available for all team members.

Since I don't need to sort the array, I get the time complexity down to linear time from nlog(n).

Inspired by [this solution](https://bigfrontend.dev/problem/82/discuss/919).

### Time & Space Complexity

O(n) time | O(n) space, where n is total number of time blocks(time intervals) in the input array.

### Solution without Sorting

```js
// type Interval = [number, number]

// 0, 1, 2, ... , 24.
const NUM_OF_HOURS = 25;

/**
 * @param {Interval[][]} schedules
 * @return {Interval[]}
 */
function findMeetingSlots(schedules) {
  const busyHours = generateBusyHours(schedules);

  const availableTimeBlocks = [];
  let availableStartTime = 0;
  let busyTimeBlockCounter = 0;

  for (let currentHour = 0; currentHour < NUM_OF_HOURS; currentHour++) {
    const isStartTime = busyHours[currentHour] > 0;
    const isNewBusyTimeBlock = isStartTime && busyTimeBlockCounter === 0;
    if (isNewBusyTimeBlock && currentHour !== 0) {
      availableTimeBlocks.push([availableStartTime, currentHour]);
    }

    busyTimeBlockCounter += busyHours[currentHour];

    const isEndTime = busyHours[currentHour] < 0;
    const isNewAvailableStartTime = isEndTime && busyTimeBlockCounter === 0;
    if (isNewAvailableStartTime) {
      availableStartTime = currentHour;
    }
  }

  if (availableStartTime < 24) {
    availableTimeBlocks.push([availableStartTime, 24]);
  }

  return availableTimeBlocks;
}

function generateBusyHours(schedules) {
  const busyHours = Array(NUM_OF_HOURS).fill(0);
  for (const schedule of schedules) {
    for (const [startTime, endTime] of schedule) {
      busyHours[startTime]++;
      busyHours[endTime]--;
    }
  }
  return busyHours;
}
```



# 83.create-an-interval.md

### Problem Description

You are asked to create a new `mySetInterval(a, b)` which has a different behavior from `window.setInterval`, the time between calls is a linear function, growing larger and larger `period = a + b * count`.

```js
let prev = Date.now();
const func = () => {
  const now = Date.now();
  console.log('roughly ', Date.now() - prev);
  prev = now;
};

const id = mySetInterval(func, 100, 200);

// roughly 100, 100 + 200 _ 0
// roughly 400, 100 + 200 _ 1
// roughly 900, 100 + 200 _ 2
// roughly 1600, 100 + 200 _ 3
// ....

myClearInterval(id); // stop the interval
```

1. Interface is `mySetInterval(delay, period)`, the first `delay` is used for the first call, and then `period` is used.
2. Because `window.setTimeout` and `window.setInterval` are not accurate in browser environment, they are replaced to other implementation when judging your code. They still have the same interface, and internally keep track of the timing for testing purpose.

Something like below will be used to do the test. (You don't need to read following code to accomplish this task)

```js
let currentTime = 0;

const run = (delay, period, clearAt) => {
  currentTime = 0;
  pipeline.length = 0;

  const logs = [];

  const func = () => {
    logs.push(currentTime);
  };

  mySetInterval(func, delay, period);
  if (clearAt !== undefined) {
    setTimeout(() => {
      myClearInterval(id);
    }, clearAt);
  }

  while (pipeline.length > 0 && calls.length < 5) {
    const [time, callback] = pipeline.shift();
    currentTime = time;
    callback();
  }

  return calls;
};

expect(run(100, 200)).toEqual([100, 400, 900, 1600, 2500]);
expect(run(100, 200, 450)).toEqual([100, 400]);
expect(run(100, 200, 50)).toEqual([]);
```

#

### Understanding the problem

I am asked to write my own version of `setInterval(func, delay)`. `mySetInterval()` will take in three parameters, `func`, `delay` and `period`. Unlike the built-in `setInterval(func, delay)`, which executes the specified callback function in an constant interval of time, the interval of `mySetInterval()` will increase: `interval = delay + period * count`. In addition, I also need to implement the `clearInterval()` function which stops the interval created by `mySetInterval(func, delay, period)`.

#

### Approach

Since I need to change the interval while the interval timer is running and I cannot use a while loop since it will block the execution of other code, I would use `setTimeout()` to implement `mySetInterval()`.

I am going to first initialize a variable called `multiplier` that is going to count the number of intervals so far; initially, set it to `0`. Then I am going to define a function named `run` that is going to be the callback function for the `setTimeout()`. Within `run`, I would execute the provided callback function and increase `multiplier` by one, then I am going to call `setTimeout()`, passing in the function `run()` and the increased timeout as arguments to mimic an interval timer. To set the interval timer, in `mySetInterval()` I am going to call `setTimeout()`, passing in the function `run()` and the initial amount of time to delay.

`mySetInterval()` is going to return the id of an interval timer so that the timer can be stopped by `myClearInterval(id)`. However, the problem is that I need to run multiple nested `setTimeout()`s and there is no way to return the id of a nested timeout timer when `mySetInterval()` gets called. To clear an existing interval timer, all I need to do is cancel the timeout timer that is currently set, so I could probably create a global variable that is going to keep track of the id of the latest timeout timer, and in `myClearInterval(id)`, I would just call `clearTimeout()` passing in the global variable. However, with this approach, `myClearInterval()` will not work correctly when two or more interval timers are set. So a better way of doing this would be store the ids in a `Map` where the key is the id of the initial timeout timer and the value is going to be the id of a nested timeout timer that is currently set. In `myClearInterval()`, I am going to call `clearTimeout()` and pass in the specified `id`, then I will check if the `id` is already a key in the `Map`, if it is, then it means that a nested timeout timer is set, so I also need to cancel that timeout timer.

### Solution

```js
const timerIds = new Map();

/**
 * @param {Function} func
 * @param {number} delay
 * @param {number} period
 * @return {number}
 */
function mySetInterval(func, delay, period) {
  let multiplier = 0;

  const initialTimerId = setTimeout(run, delay + period * multiplier);

  function run() {
    func();
    multiplier++;
    const nextTimerId = setTimeout(run, delay + period * multiplier);
    timerIds.set(initialTimerId, nextTimerId);
  }

  return initialTimerId;
}

/**
 * @param { number } id
 */
function myClearInterval(id) {
  clearTimeout(id);
  if (timerIds.has(id)) {
    clearTimeout(timerIds.get(id));
    timerIds.delete(id);
  }
}
```



# 84.create-a-fake-timer-setInterval.md

### Problem Description

This is a follow-up on [36. create a fake timer(setTimeout)](https://bigfrontend.dev/problem/create-a-fake-timer)

Like `setTimeout`, `setInterval` also is not accurate. (please refer [Event Loop](https://javascript.info/event-loop) for detail).

This is OK in general web application, but might be problematic in test.

Could you implement your own `setInterval()` and `clearInterval()` to be sync? so that they have **accurate timing** for test. This is what [FakeTimes](https://github.com/sinonjs/fake-timers) are for.

By "accurate", it means **suppose all functions cost no time**, we start our function at time `0`, then `setInterval(func1, 100)` would schedule `func1` exactly at `100, 200, 300 .etc`.

You need to replace `Date.now()` as well to provide the time.

```js
class FakeTimer {
  install() {
    // replace window.setInterval, window.clearInterval, Date.now
    // with your implementation
  }

  uninstall() {
    // restore the original implementation of
    // window.setInterval, window.clearInterval, Date.now
  }

  tick() {
    // run the scheduled functions without waiting
  }
}
```

** Be careful about the infinite loops **. Your code is tested like this:

```js
const fakeTimer = new FakeTimer();
fakeTimer.install();

const logs = [];
const log = () => {
  logs.push(Date.now());
};

let count = 0;
const id = setInterval(() => {
  if (count > 1) {
    clearInterval(id);
  } else {
    log();
  }
  count += 1;
}, 100);
// log 'A' at every 100, stop at 200
fakeTimer.tick();
fakeTimer.uninstall();

expect(logs).toEqual([100, 200]);
```

**Note**

Only `Date.now()` is used when judging your code, you can ignore other time related apis.

#

### Understanding the problem

I am asked to implement a `FakeTimer` class which is going to have three methods: `install()`, `uninstall()` and `tick()`. The `install()` method is going to replace the built-in `setInterval()`, `clearInterval()` and `Date.now()` with my implementation of the three methods and the `uninstall()` method is going to restore the original implementation:

- My implementation of `setInterval()` is going to take in a callback function and the amount of time to delay the execution of the function as arguments. Unlike the built-in `setInterval()`, it will only queue/schedule the specified callback function but will not actually set the interval timer.

- My version of `clearInterval(id)` will stop the repeated execution of the specified function.

- My `Date.now()` is going to return the "current" time, which is the total amount of time that a scheduled function has been 'waited' before it executes, for instance, the following code snippet should logs `100, 200`:

  ```js
  setInterval(() => {
    console.log(Date.now);
  }, 100);
  ```

The `tick()` method is going to run the scheduled functions without waiting. In other words, the scheduled functions don't need to actually wait the specified amount of time before they start execution. In addition, for this problem, I don't need to handle multiple `setInterval()` calls or nested `setInterval()`, which means initially there is only one callback function.

#

### Approach

In the constructor of the `FakeTimer` class, I am going to first store the original implementation of `setInterval()`, `clearInterval()` and `Date.now()`. Then I will initialize a property called `taskQueue` to be an empty array that is going to store the scheduled functions. I am also going to initialize a property named `currTime` that is going to keep track of the 'current' time; initially set it to `0`.

In the `install()` method, I am going to define my implementation of these three functions and set `window.setInterval()`, `window.clearInterval` and `Date.now` to my implementation:

- `setInterval()` is going to take in a callback function and the amount of the time to delay. I am going to append the callback function to the `taskQueue` array and store its delay as well as its id alongside with it. The id is going to be equal to the length of the `taskQueue` array. At the very end of the function I will return the id.

- `clearInterval(id)` is going to delete all the scheduled functions with that specified `id` in the `taskQueue` array.

- `Date.now()` is going to just return the `currTime`.

In the `uninstall()` method, I am going to set `window.setInterval()`, `window.clearInterval` and `Date.now` back to the original implementation that I have stored.

In the `tick()` method, first I am going to pop the oldest callback function off the `taskQueue` array and set the `currTime` to its delay. Then I would update the delay by adding `currTime` to it, and append the callback function to the `taskQueue` with the updated delay. Lastly, I would execute the callback function. The reason that I call the callback function after pushing it back to the `taskQueue` array is that if inside the callback function `clearInterval()` is called, the newly pushed callback function can be also deleted, so that the infinite loops can be prevented. While the `taskQueue` array is not empty, continue this process.

### Solution

```js
class FakeTimer {
  constructor() {
    this.origSetInterval = window.setInterval;
    this.origClearInterval = window.clearInterval;
    this.origDateNow = Date.now;

    this.taskQueue = [];
    this.currTime = 0;
  }

  install() {
    window.setInterval = (fn, delay) => {
      const id = this.taskQueue.length;
      this.taskQueue.push({
        id,
        fn,
        delay,
      });
      return id;
    };

    window.clearInterval = (id) => {
      this.taskQueue = this.taskQueue.filter((task) => task.id !== id);
    };

    Date.now = () => this.currTime;
  }

  uninstall() {
    window.setInterval = this.origSetInterval;
    window.clearInterval = this.origClearInterval;
    Date.now = this.origDateNow;
  }

  tick() {
    while (this.taskQueue.length > 0) {
      const task = this.taskQueue.shift();
      this.currTime = task.delay;
      this.taskQueue.push({
        ...task,
        delay: this.currTime + task.delay,
      });
      task.fn();
    }
  }
}
```



# 85.implement-lodash-get.md

### Problem Description

[\_.get(object, path, [defaultValue])](https://lodash.com/docs/4.17.15#get) is a handy method to help retrieving data from an arbitrary object. if the resolved value from `path` is `undefined`, `defaultValue` is returned.

Can you create your own `get()`?

```js
const obj = {
  a: {
    b: {
      c: [1, 2, 3],
    },
  },
};

get(obj, 'a.b.c'); // [1,2,3]
get(obj, 'a.b.c.0'); // 1
get(obj, 'a.b.c[1]'); // 2
get(obj, ['a', 'b', 'c', '2']); // 3
get(obj, 'a.b.c[3]'); // undefined
get(obj, 'a.c', 'bfe'); // 'bfe'
```

#

### Understanding the problem

I am asked to write a function called `get` that will take in an source object, a `path` parameter which is either a string or an array of strings, and optionally a `defaultValue` parameter, which defaults to `undefined`. The function `get()` is going to retrieve the value at the specified path of the provided object.

#

### Approach

First I am going to check if the `path` is a string, if it is, then split the string into an array with `'.'` or `'['` or `']'` as separator. Then I would traverse the source object based on the path to get the value. I could traverse the source object either iteratively or recursively. For both approach, I would initialize a pointer that is going to keep track of the property name I am currently at in the path array.

To traverse the source object recursively, I would define a function that would be my actual recursive function. It will take in the current source I need to traverse, which is initially the source object, the path array, a pointer, which is initially set to 0, and also the `defaultValue`. I will call the recursive function on each property name in the path array, recursively traversing the source object, until either the property doesn't exist in the source object or I eventually reach the very end of the path array. In the recursive function, I am going to check if the index is equal to the length of the path array, if it is, either return the current source, which is the source passed to the recursive function in the previous step, or the `defaultValue` if the current source is `undefined`; then I am going to make sure that the current source is not `undefined`, if it is, return the `defaultValue`; these are both of my base cases in the recursive function. Then I will get the current property name that is what the pointer currently points at in the path array, set the current source to the value of the current property, increase the pointer by one, so make the pointer point to the next property name, and then call the recursive function passing in the updated arguments.

To traverse the source object iteratively, I would initialize a variable that is going to keep track of the current source I need to traverse; initially, set it to the source object. I will also initialize a pointer to 0. While the current source is not `undefined`, meaning the previous property is a key in the source object, and the pointer is less than the length of the path array, meaning I still have properties to look through, get the property name that the current pointer points at in the path array, retrieve the value of that property in the current source and update the current source to that value. Once I get out of the while loop, return the current source or the `defaultValue` if the current source is `undefined`.

🙋‍♀️🙋‍♂️ In both approach, I didn't handle the case in which `path` is an empty array.

### Recursive Solution

```js
/**
 * @param {object} source
 * @param {string | string[]} path
 * @param {any} [defaultValue]
 * @return {any}
 */
function get(source, path, defaultValue = undefined) {
  path = Array.isArray(path) ? path : path.split(/\.|\[|\]/);

  if (path[path.length - 1] === '') path.pop();

  return getImpl(source, path, 0, defaultValue);
}

function getImpl(source, path, idx, defaultValue) {
  if (path.length === 0 || source === undefined) {
    return defaultValue;
  }

  if (idx === path.length) return source || defaultValue;

  const property = path[idx];
  return getImpl(source[property], path, idx + 1, defaultValue);
}
```

### Iterative Solution

```js
/**
 * @param {object} source
 * @param {string | string[]} path
 * @param {any} [defaultValue]
 * @return {any}
 */
function get(source, path, defaultValue = undefined) {
  path = Array.isArray(path) ? path : path.split(/\.|\[|\]/);

  if (path[path.length - 1] === '') path.pop();

  if (path.length === 0) return defaultValue;

  let currentSource = source;
  let idx = 0;

  while (currentSource && idx < path.length) {
    const property = path[idx];
    currentSource = currentSource[property];
    idx++;
  }

  return currentSource || defaultValue;
}
```

### Iterative Solution with Reduce

```js
/**
 * @param {object} source
 * @param {string | string[]} path
 * @param {any} [defaultValue]
 * @return {any}
 */
function get(source, path, defaultValue = undefined) {
  path = Array.isArray(path) ? path : path.split(/\.|\[|\]/);

  if (path[path.length - 1] === '') path.pop();

  if (path.length === 0) return defaultValue;

  const value = path.reduce(
    (currentSource, property) => currentSource[property],
    source
  );
  return value || defaultValue;
}
```



# 86.Generate-Fibonacci-Number.md

### Problem Description

```js
0
1
1 = 0 + 1
2 = 1 + 1
3 = 1 + 2
5 = 2 + 3
8 = 3 + 5
13 = 5 + 8
....

[0, 1, 1, 2, 3, 5, 8, 13, ...]
```

Given 2 initial numbers, we can generate a sequence by adding the sum of last two numbers as a new element.

This is [Fibonacci number](https://en.wikipedia.org/wiki/Fibonacci_number).

You are asked to create a `fib(n)` function, which generate the n-th Fibonacci number.

What is the time & space cost of your solution?

#

### Understanding the problem

Given a non-negative integer `n`, I am asked to write a function that is going to return the nth Fibonacci number in the Fibonacci sequence. In this problem, zero based indexing will be used.

#

### Iterative Approach

I am going to initialize an array that is going to keep track of the last two Fibonacci numbers. Initially, it is going to contain the first two numbers of the Fibonacci sequence, which are `0` and `1`. To get the next Fibonacci number, I am going to add the previous two Fibonacci numbers up. I will also create a variable named `counter` that is going to keep track of how many new Fibonacci numbers I have calculated. Since I already have the first two numbers of the Fibonacci sequence, initialize the `counter` to 2. While the `counter` is not greater than the integer `n`, keep calculating the next Fibonacci number and keep updating the previous two Fibonacci numbers as well as the counter. Once I get out of the while loop, return the last Fibonacci number if `n` is greater than 0, otherwise return the first number of the Fibonacci sequence.

### Time & Space Complexity

O(n) time | O(1) space, where n is the input number.

### Iterative Solution

```js
/**
 * @param {number} n - non-negative integer
 * @return {number}
 */
function fib(n) {
  const lastTwoFibs = [0, 1];
  let counter = 2;
  while (counter <= n) {
    const nextFib = lastTwoFibs[0] + lastTwoFibs[1];
    lastTwoFibs[0] = lastTwoFibs[1];
    lastTwoFibs[1] = nextFib;
    counter++;
  }
  return n === 0 ? lastTwoFibs[0] : lastTwoFibs[1];
}
```



# 87.longest-substring-with-unique-characters.md

### Problem Description

Given a string, please find the **longest substring that has no repeated characters**.

If there are multiple result, any one substring is fine.

```js
longestUniqueSubstr('aaaaa');
// 'a'
longestUniqueSubstr('abcabc');
// 'abc', or 'bca', or 'cab'
longestUniqueSubstr('a12#2');
// 'a12#'
```

_Follow-up_

What is the time & space cost of your solution ? Could you do it better?

#

### Understanding the problem

Given a string, I am asked to write a function that is going to return the longest substring that doesn't contain any duplicate characters. If there are multiple such substrings, I only need to return one of them.

#

### Approach

My initial thought was that I could solve this problem using two pointers approach. However, this approach doesn't work when the input string is `'a12#2'`, because with this approach I can only detect the duplicate of the first character in the input string.

Instead, I would initialize a `Map` that is going to keep track of the characters that I've seen so far, where the keys are the characters and each value is the index of each character. In addition, I am going to initialize a variable `substrStart` to `0` that is going to keep track of the substrStart index of the current substring that has no duplicate characters, and a variable `longestSubstrStart` to `0` that is going to keep track of the substrStart index of the longest substring by far as well as a variable `longestSubstrEnd` to `1` that is going to keep track of the end index of the current longest substring. I would then traverse through the input string. For each character, first I am going to check if it is already a key in the `Map`, which means check if I've already seen the character:

- If it is not in the `Map`, store it and its index into the `Map`, calculate the length of the current substring by subtracting its index with the `substrStart` plus one, if the result is greater than the length of the current longest substring, which is `longestSubstrEnd` minus `longestSubstrStart`, then update the `longestSubstrEnd` as well as the `longestSubstrStart` if it is not equal to the `substrStart`.

- Otherwise, retrieve the previous index of the character from the `Map`, update the `substrStart` to be equal to the previous index plus one. Then compare the length of the current substring to the length of current longest substring, if the current substring is longer than the longest substring, update the `longestSubstrStart` and the `longestSubstrEnd`.

Once I reach the very end of the input string, return the substring that starts at the `longestSubstrStart` and ends at the `longestSubstrEnd`, `longestSubstrEnd` is exclusive.

🙋‍♀️🙋‍♂️ Although this approach passed all of the test cases, it will fail when the input string is, for instance, `'thecatinthecat'`.

Suppose I am currently at index `9` in the example string. The character at index `9` is `h`, which I've already seen at index `1`. The start index of the current substring is `6`. If I simply set `2` as the start index of the new substring, then the new substring will contain duplicate `t`s.

```
0 1 2 3 4 5 6 7 8 9 10 11 12 13
t h e c a t i n t h  e  c  a  t
                  i

substrStart = 6
seenChars = {
  t: 7,
  h: 1,
  e: 2,
  c: 3,
  a: 4,
  i: 5,
  n: 6,
}
```

To solve this issue, I need to compare the previous index of a duplicate character plus one to the start index of the current substring and only update the start index if the previous index plus one is greater than the start index.

### Time & Space Complexity

O(n) time | O(min(n, d)) space, where n is the number of characters in the input string and d is the number of distinct characters in the input string.

For instance, if the input string is `'aaab11#b#cd'`, then d is going to be equal to 6, because we only need to store each character in our `Map` once.

### Solution

```js
/**
 * @param {string} str
 * @return {string}
 */
function longestUniqueSubstr(str) {
  let longestSubstrStart = 0;
  let longestSubstrEnd = 1;
  let substrStart = 0;
  const seenChars = new Map();

  for (let idx = 0; idx < str.length; idx++) {
    const char = str[idx];

    if (seenChars.has(char)) {
      const prevIdx = seenChars.get(char);
      substrStart = Math.max(substrStart, prevIdx + 1);
    }

    seenChars.set(char, idx);

    const currSubstrLen = idx - substrStart + 1;
    const currLongestSubstrLen = longestSubstrEnd - longestSubstrStart;
    if (currSubstrLen > currLongestSubstrLen) {
      longestSubstrEnd = idx + 1;
      if (substrStart !== longestSubstrStart) {
        longestSubstrStart = substrStart;
      }
    }
  }

  return str.slice(longestSubstrStart, longestSubstrEnd);
}
```



# 88.support-negative-Array-index-in-JavaScript.md

### Problem Description

Python supports negative list index , while JavaScript doesn't.

Can you write a wrapper function to make **negative array index** possible?

```js
const originalArr = [1, 2, 3];
const arr = wrap(originalArr);

arr[0]; // 1
arr[1]; // 2
arr[2]; // 3
arr[3]; // undefined
arr[-1]; // 3
arr[-2]; // 2
arr[-3]; // 1
arr[-4]; // undefined
```

All methods on `arr` should be applied to the original array, which means

<!--prettier-ignore-->
```js
arr.push(4);
arr[3] // 4
originalArr[3] // 4

arr.shift();
arr[0] // 2
originalArr[0] // 2

arr.bfe = 'bfe';
originalArr.bfe // 'bfe'

arr[-1] = 5;
arr // [2,3,5]
originalArr // [2,3,5]

originalArr[2] = 6;
arr // [2,3,6]
originalArr // [2,3,6]
```

#

### Understanding the problem

I am asked to write a function that is going to wrap the input array to make it support negative array index, and return the result, which should be also an array.

```js
const arr = wrap([1, 2, 3]);

Array.isArray(arr); // true
```

It should support the bracket notation, meaning we can update an element at a specific index using bracket notation:

- If an element in the original array is updated using bracket notation, the change should also reflect in the wrapped array, and vice versa.
- Placing an element at a negative index that is out of bounds of the input array should end up in an error, for instance:

  ```js
  const arr = wrap([1, 2, 3]);

  arr[-5] = 'bfe'; // Error
  ```

In addition, all Array methods, i.e., `Array.prototype.push()` and `Array.prototype.shift()`, should be applied to the original array.

#

### Approach

To solve the problem, I am going to create a `Proxy` for the original array and return the proxy.

When we create a `Proxy` for an object or an array, any changes in the original one will reflect in the proxy:

```js
const obj = {
  a: 1,
  b: 2,
};

const objProxy = new Proxy(obj, {});
obj.c = 3;
console.log(objProxy.c); // 3
```

In addition, since a `Proxy` is an undetectable wrapper around an object/array, if we create a `Proxy` for an array, the `Proxy` is also going to be an array:

```js
const arr = [1, 2, 3];

const arrProxy = new Proxy(arr, {});
console.log(Array.isArray(arrProxy)); // true
```

I would implement a `get()` handler to intercept attempts to access an element at a specific index in the input array and would also provide a `set()` handler to intercept attempts to update an element at a specific index.

In the `get(target, prop, receiver)` handler, I will first make sure that the `prop` can be converted to a number, because besides indices an array has other properties, i.e. `.push`. If the `prop` cannot convert to a number, then simply return the `prop`'s value. Otherwise, check if the `prop` is negative, if it is, get the corresponding positive index by adding the `prop` to the length of the target and then return the element at that positive index; otherwise, simply return the element.

The `set(target, prop, value, receiver)` is going to be similar to the `get()` handler, however, rather than returning the element, I am going to update the `prop`'s value to the new value. In addition, after successfully converting the `prop` to a number and getting the corresponding positive index, I am going to make sure the index is within bounds. If the resulting positive index is greater than the length of the target, since I need to support the `Array,prototype.push` method, or is still negative, then it means the index is out of bounds, so throw an error.

### Solution

```js
/**
 * @param {any[]} arr
 * @returns {?} - sorry no type hint for this
 */
function wrap(arr) {
  const handler = {
    get(target, prop, receiver) {
      prop = getPositiveIdx(target.length, prop);

      return Reflect.get(target, prop, receiver);
    },
    set(target, prop, value, receiver) {
      prop = getPositiveIdx(target.length, prop);

      const idxIsOutOfBounds = prop > arr.length || prop < 0;
      if (idxIsOutOfBounds) {
        throw new Error('Array Index Overflow');
      }

      Reflect.set(target, prop, value, receiver);
      return true;
    },
  };

  return new Proxy(arr, handler);
}

function getPositiveIdx(arrLength, propName) {
  try {
    let idx = Number(propName);
    if (isNaN(idx)) return propName;

    return idx < 0 ? arrLength + idx : idx;
  } catch (err) {
    return propName;
  }
}
```



# 89.Next-Right-Sibling.md

### Problem Description

Given a DOM tree and a target element, please return the **next right sibling**.

![A DOM tree](https://i.imgur.com/DUICfa3.png 'A DOM tree')

Like above, the next right sibling of `<button/>` is the blue `<a/>`. Notice that **they don't necessarily have the same parent element**.

If no right sibling, then return `null`.

What is time & space cost of your solution ?

#

### Understanding the problem

I am given a DOM tree and a target element. I am tasked with finding the right sibling of the target element. The target element and its right sibling don't necessarily have the same parent element. If right sibling doesn't exit, return `null`.

#

### Approach with BFS

We could solve this problem with iterative Breadth First Search. With Breadth First Search, the element to right of the target element in the queue is either the right sibling or the first element in the next tree level. We can use `null` values as indication that we reach the end of a level. At the very beginning of our function, we initialize a queue and enqueue the root node into it. In addition, we also enqueue `null` into the queue, since there is only node in the first tree level. When we traverse the DOM tree, dequeue node out of the queue, and if we come across a `null` value, then we know we are at the end of a level, so we enqueue a `null` value into the queue to indicate the end of the next level.
For instance, imagine we have the following DOM tree:

```
                    <div/>
                  /    \    \
            <div/>   <div/>  <div/>
            /    \       \      \
          <p/>   <p/>    <p/>   <p/>
          /      /
        <a/>    <a/>
```

At the very beginning, we enqueue `<div/>` and `null` into the queue.

```
[<div/>, null];
```

We dequeue `<div/>` out of the queue, and push its child nodes into the queue.

```
[null, <div/>, <div/>, <div/>];
```

We dequeue the first element out of the queue again. Since it's `null`, we know we are at the end of the first level. Since there is no more node to explore in the current level, there is no more child node in the second level that we need to enqueue into the queue. So we enqueue `null` into the queue.

```
[<div/>, <div/>, <div/>, null];
```

We keep doing the same process until we get to the target element or there is no more node to explore. If it is the former case, we return the element at the front of the queue; otherwise return `null`.

### Solution

```js
/**
 * @param {HTMLElement} root
 * @param {HTMLElement} target
 * @return {HTMLElement | null}
 */
function nextRightSibling(root, target) {
  const queue = [root, null];

  while (queue.length > 0) {
    const node = queue.shift();

    if (node === target) {
      return queue[0];
    }

    if (node === null) {
      queue.push(null);
    } else {
      queue.push(...node.children);
    }
  }

  return null;
}
```

#

### Approach with DOM API

### Solution

```js
/**
 * @param {HTMLElement} root
 * @param {HTMLElement} target
 * @return {HTMLElement | null}
 */
function nextRightSibling(root, target) {
  if (!target) return null;
  if (target.nextElementSibling) {
    return target.nextElementSibling;
  }

  let parent = target.parentElement;
  let parentNextSibling = nextRightSibling(root, parent);
  while (parentNextSibling) {
    if (parentNextSibling && parentNextSibling.firstElementChild) {
      return parentNextSibling.firstElementChild;
    }
    parentNextSibling = nextRightSibling(root, parentNextSibling);
  }

  return null;
}
```

#

### Reference

[BFS with explanation](https://bigfrontend.dev/problem/Next-Right-Sibiling/discuss/948)
[Element.nextElementSibling](https://developer.mozilla.org/en-US/docs/Web/API/Element/nextElementSibling)



# 9.decode-message.md

### Problem Description

Your are given a 2-D array of characters. There is a hidden message in it.

```
I B C A L K A
D R F C A E A
G H O E L A D
```

The way to collect the message is as follows

1. start at top left
2. move diagonally down right
3. when cannot move any more, try to switch to diagonally up right
4. when cannot move any more, try switch to diagonally down right, repeat 3
5. stop when cannot neither move down right or up right. the character on the path is the message
   for the input above, `IROCLED` should be returned.

**notes**

if no characters could be collected, return empty string

#

### Solution

```js
/**
 * @param {string[][]} message
 * @return {string}
 */
function decode(message) {
  const result = [];
  let x = 0;
  let y = 0;
  let dir = 'down';

  while (message[x] && message[x][y]) {
    result.push(message[x][y]);

    if (dir === 'down') {
      x++;
    } else {
      x--;
    }

    if (x === 0) {
      dir = 'down';
    } else if (x === message.length - 1) {
      dir = 'up';
    }

    y++;
  }

  return result.join('');
}
```



# 91.invert-a-binary-tree.md

### Problem Description

Can you invert a binary tree and get an offer from Google?

Inverting a node means swapping its left child and right child. You need to apply this to all nodes. As following figure illustrates.

```
        1                            1
      /   \                        /   \
     2     3                      3     2
    /       \      Invert        /       \
   4         5  ------------>   5         4
  / \       /                    \       / \
 6   7     8                      8     7   6
      \                                /
       9                              9
```

#

### Understanding the problem

I am asked to write a function that is going to invert a Binary Tree and return the inverted version.

#

### Approach

I am going to recursively traverse the Binary Tree. At each node, I am going to swap its child nodes and return the current node. If I get to a node that is equal to `null`, then it means I've reached a leaf node, just return the node. This is going to be the base case of the recursive function.

### Solution

```js
/**
 * @param {Node} node
 * @returns {Node}
 */
function invert(node) {
  if (!node) return node;

  const { left } = node;
  node.left = invert(node.right);
  node.right = invert(left);

  return node;
}
```



# 96.count-1-in-binary-form.md

### Problem Description

Given an integer, count "1" in its binary form.

```js
countOne(1); // 1,  "1"
countOne(257799); // 12, "111110111100000111"
```

1. If you use built-in string methods in JavaScript, please do understand the time complexity, they are not free.
2. Actually this could be easily done by counting the digit one by one. Could you think of some other approaches?

#

### Solution 1

```js
/**
 * @param {number} num - integer
 * @return {number} count of 1 bit
 */
function countOne(num) {
  return num.toString(2).replace(/0/g, '').length;
}
```

#

### Solution 2

```js
/**
 * @param {number} num - integer
 * @return {number} count of 1 bit
 */
function countOne(num) {
  let numOfOneBits = 0;
  let mask = 1;

  for (let i = 0; i < 64; i++) {
    if ((num & mask) !== 0) {
      numOfOneBits++;
    }

    mask <<= 1;
  }

  return numOfOneBits;
}
```

#

### Solution 3: Bit Manipulation

```js
/**
 * @param {number} num - integer
 * @return {number} count of 1 bit
 */
function countOne(num) {
  let count = 0;
  while (num) {
    num &= num - 1;
    count++;
  }
  return count;
}
```

#

### Reference

[How can I check Hamming Weight without converting to binary?](https://stackoverflow.com/questions/843828/how-can-i-check-hamming-weight-without-converting-to-binary)

