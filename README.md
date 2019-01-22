# Introduction to Web Workers


## Background

1. Concurrency has always been an hot topic for programming languages
2. Devs performing lots of hack in JS to mimic Concurrency like **_setTimeout(), setInterval(), XMLHttpRequest, and event handlers_**
3. Even to improve performance, we have optimised JS engines.

### What's the problem??

**Hint:** JS itself, why? (_single threaded_)

** Solution -**  Web Workers

## Web Workers

### Basics

#### Creating Web worker Object

```javascript
var worker = new Worker('worker.js');
```

#### Starting worker

```javascript
worker.postMessage();
```

#### Receive Response/Request

```javascript
myWorker.onmessage = function(e) { ... }
```

### Applying Theory to Practice

#### Main.js

```javascript
var myWorker = new Worker('worker.js');

first.onchange = function() {
  myWorker.postMessage([first.value,second.value]);
  console.log('Message posted to worker');
}

myWorker.onmessage = function(e) {
  result.textContent = e.data;
  console.log('Message received from worker');
}
```

#### worker.js

```javascript
onmessage = function(e) {
  console.log('Message received from main script');
  var workerResult = 'Result: ' + (e.data[0] * e.data[1]);
  console.log('Posting message back to main script');
  postMessage(workerResult);
}
```


## Use Case

Performing Resource intensive computation on Client Side like

- Prefetching and/or caching data for later use
- Code syntax highlighting or other real-time text formatting
- Spell checker
- Analysing video or audio data
- Background I/O or polling of web-services
- Processing large arrays or humungous JSON responses
- Image filtering in <canvas\>
- Updating many rows of a local web database



[Demo Link](https://cedpoilly.github.io/web-worker/)


References:

- [Web Workers API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API)
- [Why Web Workers?](https://www.codementor.io/cedpoilly/why-web-workers-hy9e7pjjb)
- [The Basics of Web Workers](https://www.html5rocks.com/en/tutorials/workers/basics/#toc-introduction-jsconcurrency)
