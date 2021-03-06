# Asynchronous Programming with Promises and Async/Await

This repo contains small exercises to get more accustom with two ways of dealing with asynchronous code in JavaScript: Promises and the new Async/Await syntax.

This repo contains four big chapters:

- [Chapter 0 - The Event Loop](#chapter-0---the-event-loop)
- [Chapter 1 - Callbacks](#chapter-1---callbacks)
- [Chapter 2 - Promises](#chapter-2---promises9)
- [Chapter 3 - Async/Await](#chapter-3---asyncawait)

Each chapter contains exercises and questions which will walk you threw the journey from callbacks to Promises and Async/Await.

## Chapter 0 - The Event Loop

Before everything, we need to talk about JavaScript's asynchronous model, or how does asynchronous programming work in JavaScript.

The most important question we will answer: **Is JavaScript an asynchronous programming language?**

### JavaScript as a language

We all heard about [EcmaScript](https://www.ecma-international.org/ecma-262/8.0/index.html) right (or in its other forms: *ES5*, *ES6*, *ES2015* etc.)? EcmaScript is the standard which describes how the language should look and work. This is a very big document which contains every tiny little detail about the language: how objects intereact, what functions they should contain and how they should work etc.. This official document is written so that future JavaScript engines know how to be built. 

We all heard of JavaScript engines like: Chrome's [V8](https://developers.google.com/v8), Microsoft's [ChakraCore](https://github.com/Microsoft/ChakraCore) or Mozilla's [SpiderMonkey](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/SpiderMonkey). These engines all follow the [EcmaScript](https://www.ecma-international.org/ecma-262/8.0/index.html) standard.

A JavaScript engine has two important internal parts:

![JavaScript Engine](./assets/JS_engine.png)

- **Heap**: is the engine's memory where objects, functions etc. get allocated and managed
- **Call Stack**: because JavaScript is single threaded, it can do only one thing at a time. The call stack manages how functions get executed, in a *LIFO* kind of fashion.

For funsies, let's open the [EcmaScript standard](https://www.ecma-international.org/ecma-262/8.0/index.html) and search for a very common function like: `setTimeout`. It should be there, right? 
No - we will not find that function in the document. Why you ask? The answer is pretty simple. JavaScript as a language, is a synchronous programming language. However, besides JavaScript engines there is another player we need to talk about, and that player is: **JavaScript Runtimes**.

A JavaScript runtime is a higher layer over the JavaScript engine which provides extra functionality like libraries, event loops etc. A few examples of runtimes are: Browsers (Chrome, Firefox etc.), NodeJS. A runtime may implement any engine it chooses. This is where the event loop is found, in the JavaScript runtime.

### What is the event loop?

The Event Loop is a runtime level implementation which offers asyncrhonous code running abilities:

![Event Loop Representation](./assets/JS_Runtime.png)

## Chapter 1 - Callbacks
**Callbacks** are the most *low level*ish way of handling asynchonous code. A callback is a function passed as an argument to another function. The power of this pattern kicks in when combined with an asynchronous function:

```javascript
function foo(callback) {
	setTimeout(() => {
		callback()
	}, 1000);
}
```

### Error handling
We can also use callbacks to handle errors:

```javascript
function hello(success, failure) {
	setTimeout(() => {
		const chance = Math.ceil(Math.random() * 2);

		if (chance == 2) {
			success()
		} else {
			failure();
		}
	});
}

const succ = () => console.log('yay!');
const failure = () => console.log('I should not play the Loto');

hello(succ, failure);
```

<details><summary>Solution example 2</summary>
<p>
	
	
	function all(callback, arr) {
	    let counter = 0;
	    arr.forEach(fn => fn(() => counter++));

	    const interval = setInterval(() => { 
	      if (counter === arr.length) {
		   clearInterval(interval);
		   callback();
	      }
	    }, 250);
	}

	const fn1 = cb => setTimeout(() => {
	   cb();
	}, 1000);

	const fn2 = cb => setTimeout(() => {
	   cb();
	}, 1000);

	all(() => console.log('done'), [fn1, fn2]);

</p>
</details>

## Chapter 2 - Promises[[9]](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules)

What will be the output and why?

```javascript
console.log('script start');

setTimeout(function() {
  console.log('setTimeout');
}, 0);

Promise.resolve().then(function() {
  console.log('promise1');
}).then(function() {
  console.log('promise2');
});

console.log('script end');
```

<details><summary>Explanation</summary>
<p>

With Promises there came a new way of executing async code called: **Microtask**. This new concept was needed because Promises can guarantee an order of execution even though they are asynchronous. This means that the event loop has another queue for scheduling these kind of microtasks. The main difference between a **task** and a **microtask** is that a task different actions can happen between them. After a task is run, the next task can only pushed to the call stack only if nothing else in mid-execution or the call stack is empty and ready to take in another task. 

With **microtasks** however, they have a **priority** over tasks. If a microtask was queued, it will run before taking in any tasks. Even more, microtasks are processed as long as there are any on the microstack queue. This means that microstack processing can cause a block on the thread as long as you keep schedule them.

This can be better explained through these two examples:

#### Infinite task creating is non-blocking

```javascript
function createTask() {
	console.log('new task');
	setTimeout(createTask);
}	

createTask(0);
```

#### Infinite microtask creaking blocks the main thread

```javascript
function createMicrotask() {
	console.log()
	Promise.resolve().then(createMicrotask);
}

createMicrotask();
```

</p>
</details>

### The Event Loop with Microtasks

![JavaScript Runtime Event Loop with Microtasks](./assets/JS_Runtime_with_Promises.png)


## Chapter 3 - Async/Await

## Bibliography:
1. [Eloquent JavaScript - Chapter 11: Asynchronous Programming](https://eloquentjavascript.net/11_async.html)
2. [MDN - Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
3. [MDN - async function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) 
4. [JSConf EU 2014 - Philip Roberts: What the heck is the event loop anyway?](https://www.youtube.com/watch?v=8aGhZQkoFbQ)
5. [Message Queue Wiki](https://en.wikipedia.org/wiki/Message_queue)
6. [MDN - Concurrency model and Event Loop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop)
7. [Alexander Zlatkov - How JavaScript works: an overview of the engine, the runtime, and the call stack](https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf)
8. [How JavaScript Works: Event Loop and the rist of Async Programming](https://blog.sessionstack.com/how-javascript-works-event-loop-and-the-rise-of-async-programming-5-ways-to-better-coding-with-2f077c4438b5)
9. [Jake Archibald - Tasks, microtasks, queues and schedules](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/)
10. [Jake Archibald: In The Loop - JSConf.Asia 2018](https://www.youtube.com/watch?v=cCOL7MC4Pl0)
