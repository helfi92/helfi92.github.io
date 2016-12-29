---
layout: post
title: How a One Liner Doubled The Unified Log Viewer Performance
description: "How a One Liner Doubled The Unified Log Viewer Performance"
modified: 2016-10-25T15:27:45-04:00
tags: [taskcluster, logviewer, react]
image:
  feature: log-viewer/fast-bikes.jpg
  credit: 
  creditlink: 
---

While I was working on the Unified Log Viewer, a project initiated by [Eli Perelman](http://eliperelman.com/), I came to realize how a single threaded environment can easily be impacted. For the sake of understanding, an example that explains the meat of the problem without necessarily diving into the specifics of the code will be used.

```javascript
const update = (response) => {
	if (!response) {
		return;
	}
	...
};

xhr.responseType = 'arraybuffer';
xhr.overrideMimeType('text/plain; charset=utf-8');

// xhr.response will be null on a progress event [1]
xhr.addEventListener('progress', () => update(xhr.response));

// xhr.response is not null
xhr.addEventListener('load', () => update(xhr.response));
```

To explain what's happening in the code above, we have a buffer that fires the `progress` event whenever new data comes and later triggers the `load` event when all data have been transmitted. For both events handlers, there is a call to an anonymous function stored in `update`. The `update` function makes a quick check on `response` before proceeding with the execution. If `response` is empty, it returns.

Note that `progress` gives a value of `undefined` as a parameter to `update`, since `xhr.response` is `null` when dealing with array buffers [1]. `load` gives a response with a value i.e., not `undefined`. This code was generating performance issues. This problem can happen when many events are being triggered and a bunch of function calls are being made as a result of those events.

JavaScript has a concurrency model based on "event loop". This can be represented by the following:

* Stack (Functions)
* Queue (Events)
* Heap (Objects are allocated in a heap)

Let me grab your attention on the Stack and Queue. You can ignore the heap for now. A queue adds a message any time an event occurs and there is an event listener attached to it. To continue, whenever there is a function call, a layer gets added to the stack. The catch here is that **a message from the queue cannot be processed unless the stack is empty.** This means if you have a script that takes a while to execute for example, any events that happen won’t run until the script is done. Since `progress` was being called many times, it was initiating a call to `update`, which was adding many layers to the stack, hence causing performance issues.

### Solution
The solution is quite simple. When you want your events to be processed fast, make sure your function stack stays as clean as possible or else they will have to wait until all functions on the stack are fully executed.

```javascript
const update = (response) => {
	... // No need to check if the response is empty anymore
};

xhr.responseType = 'arraybuffer';
xhr.overrideMimeType('text/plain; charset=utf-8');
xhr.addEventListener('progress', () => {
	if (xhr.response) {
		update(xhr.response);
	}	
}
xhr.addEventListener('load', () => update(xhr.response));
```

### Conclusion
When performance depends heavily on how quick your events are being processed, it is important not to cluster the stack model. Remember that event messages are not processed unless the stack model is empty. As a result, I saw huge performance improvements (~ 2 times).

### Resources
[1] [https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/response](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/response)
