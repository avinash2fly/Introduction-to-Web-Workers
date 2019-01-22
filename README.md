# Introduction to Web Workers


## Background

1. Concurrency has always been an hot topic for programming languages
2. Devs performing lots of hack in JS to mimic Concurrency like **_setTimeout(), setInterval(), XMLHttpRequest, and event handlers_**
3. Even to improve performance, we have optimised JS engines.

### What's the problem??

**Hint:** JS itself, why? (_single threaded_)

** Solution -**  Web Workers

## Web Workers

### Dedicated Web Workers

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

##### Alternatives

```javascript
self.addEventListener('message', function(e) {
  postMessage(1/x); // Intentional error.
};
```

#### Error Handling

```javascript
var myWorker = new Worker('worker.js');

myWorker.onerror = function() {
  console.log('There is an error with your worker!');
}
```

##### Alternatives

```javascript
  worker.addEventListener('error', function(e) {
      ...
    });
```

#### Terminate Worker

##### Main file

```javascript
worker.terminate()
```

##### worker file

```javascript
self.close();
```

### Applying Theory to Practice

#### Main.js

```javascript
<button onclick="sayHI()">Say HI</button>
<button onclick="unknownCmd()">Send unknown command</button>
<button onclick="stop()">Stop worker</button>
<output id="result"></output>

<script>
  function sayHI() {
    worker.postMessage({'cmd': 'start', 'msg': 'Hi'});
  }

  function stop() {
    // worker.terminate() from this script would also stop the worker.
    worker.postMessage({'cmd': 'stop', 'msg': 'Bye'});
  }

  function unknownCmd() {
    worker.postMessage({'cmd': 'foobard', 'msg': '???'});
  }

  var worker = new Worker('doWork2.js');

  worker.addEventListener('message', function(e) {
    document.getElementById('result').textContent = e.data;
  }, false);
</script>
```

#### worker.js

```javascript
self.addEventListener('message', function(e) {
  var data = e.data;
  switch (data.cmd) {
    case 'start':
      self.postMessage('WORKER STARTED: ' + data.msg);
      break;
    case 'stop':
      self.postMessage('WORKER STOPPED: ' + data.msg +
                       '. (buttons will no longer work)');
      self.close(); // Terminates the worker.
      break;
    default:
      self.postMessage('Unknown command: ' + data.msg);
  };
}, false);
```

### Issues with web Workers

- Less performant, as it's copies data from main thread to worker thread

solution : [Transferable Objects](https://developers.google.com/web/updates/2011/12/Transferable-Objects-Lightning-Fast)


### Worker scope

Web workers work in shared-nothing fashion i.e. it cannot access other workers data

[Sharing variables between web workers?](ttps://stackoverflow.com/questions/2262681/sharing-variables-between-web-workers-global-variables)

### Importing scripts inside a worker

```javascript
importScripts('script1.js');
importScripts('script2.js');
```

### Inline Worker

We don't need to create a separate worker file instead we can create a Blob object

```javascript
var blob = new Blob([
    "onmessage = function(e) { postMessage('msg from worker'); }"]);

// Obtain a blob URL reference to our worker 'file'.
var blobURL = window.URL.createObjectURL(blob);

var worker = new Worker(blobURL);
worker.onmessage = function(e) {
  // e.data == 'msg from worker'
};
worker.postMessage(); // Start the worker.
```

The magic comes with the call to window.URL.createObjectURL(). This method creates a simple URL string which can be used to reference data stored in a DOM File or Blob object.


## Limitations

### What workers can do

- The navigator object
- The location object (read-only)
- XMLHttpRequest
- setTimeout()/clearTimeout() and setInterval()/clearInterval()
- The Application Cache
- Importing external scripts using the importScripts() method
- Spawning other web workers (Subworkers must be hosted within the same origin as the parent page.)

### What workers can't do

- The DOM (it's not thread-safe)
- The window object
- The document object
- The parent object

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

## Read More

#### [Shared Worker](https://developer.mozilla.org/en-US/docs/Web/API/SharedWorker)
#### [Service Worker](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorker)

## Demo
**[click here](https://cedpoilly.github.io/web-worker/)**

## References:

- [Web Workers API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API)
- [Why Web Workers?](https://www.codementor.io/cedpoilly/why-web-workers-hy9e7pjjb)
- [The Basics of Web Workers](https://www.html5rocks.com/en/tutorials/workers/basics/#toc-introduction-jsconcurrency)
