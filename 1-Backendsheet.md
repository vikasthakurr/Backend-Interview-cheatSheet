## 1. What is Node.js?

**Theory:**
Node.js is a runtime environment that allows executing JavaScript code outside the browser. It is built on Chrome's V8 engine and is widely used for building scalable network applications.

**Code Example:**

```js
console.log('Hello from Node.js');
```

---

## 2. What are the key features of Node.js?

**Theory:**

* Event-driven, non-blocking I/O.
* Single-threaded but supports concurrency.
* Cross-platform and lightweight.
* npm ecosystem for package management.

---

## 3. What is the difference between Node.js and JavaScript in the browser?

**Theory:**
Node.js runs on the server; browser JS runs in the client. Node.js provides modules for file system, networking, process management, which are not available in browser JS.

---

## 4. Explain the Node.js event loop.

**Theory:**
The event loop allows Node.js to perform non-blocking I/O operations. It checks the event queue and executes callbacks asynchronously.

**Code Example:**

```js
console.log('Start');
setTimeout(() => console.log('Callback'), 0);
console.log('End');
// Output: Start, End, Callback
```

---

## 5. What is a callback function?

**Theory:**
A callback is a function passed as an argument to another function and executed after an asynchronous operation completes.

**Code Example:**

```js
fs.readFile('file.txt', 'utf8', (err, data) => {
  if(err) console.error(err);
  else console.log(data);
});
```

---

## 6. Difference between synchronous and asynchronous functions?

**Theory:**
Synchronous functions block execution until complete. Asynchronous functions allow execution to continue while waiting for I/O operations.

**Code Example:**

```js
// Sync
const data = fs.readFileSync('file.txt', 'utf8');
console.log(data);

// Async
fs.readFile('file.txt', 'utf8', (err, data) => console.log(data));
```

---

## 7. What is the difference between process.nextTick() and setImmediate()?

**Theory:**

* `process.nextTick()` executes before the event loop continues.
* `setImmediate()` executes after the current poll phase.

**Code Example:**

```js
process.nextTick(() => console.log('Next Tick'));
setImmediate(() => console.log('Immediate'));
```

---

## 8. What is a Node.js module?

**Theory:**
A module is a reusable block of code in Node.js, either built-in, third-party (npm), or custom.

**Code Example:**

```js
// customModule.js
module.exports = { greet: () => console.log('Hello') };

// main.js
const { greet } = require('./customModule');
greet();
```

---

## 9. Difference between require() and import

**Theory:**

* `require()` is CommonJS module syntax used in Node.js.
* `import` is ES6 module syntax, supported with `type: module` in package.json or transpilers.

---

## 10. Explain the Node.js global objects.

**Theory:**
Global objects are available in all modules without importing:

* `__dirname`, `__filename`
* `global`
* `process`
* `console`

**Code Example:**

```js
console.log(__dirname);
console.log(process.version);
```

---

## 11. What is npm?

**Theory:**
Node Package Manager (npm) manages Node.js packages, allowing installation, versioning, and dependency management.

**Code Example:**

```bash
npm init
npm install express --save
```

---

## 12. Difference between local and global npm packages

**Theory:**

* Local: Installed in project directory, used only by that project.
* Global: Installed system-wide, accessible from CLI.

---

## 13. Explain EventEmitter

**Theory:**
EventEmitter allows emitting and listening to events in Node.js.

**Code Example:**

```js
const EventEmitter = require('events');
const emitter = new EventEmitter();
emitter.on('message', msg => console.log(msg));
emitter.emit('message', 'Hello Events');
```

---

## 14. Explain streams in Node.js

**Theory:**
Streams handle reading/writing data efficiently in chunks.

* Readable
* Writable
* Duplex
* Transform

**Code Example:**

```js
const fs = require('fs');
const readStream = fs.createReadStream('file.txt');
readStream.on('data', chunk => console.log(chunk.toString()));
```

---

## 15. Difference between Buffer and String

**Theory:**

* String: Sequence of characters.
* Buffer: Sequence of bytes, used for binary data.

**Code Example:**

```js
const buf = Buffer.from('Hello');
console.log(buf.toString());
```

---

## 16. What is REPL?

**Theory:**
Read-Eval-Print-Loop: Interactive shell to execute Node.js commands.

**Code Example:**

```bash
node
> console.log('Hello')
```

---

## 17. Explain \_\_dirname and \_\_filename

**Theory:**

* `__dirname`: Absolute path of current directory.
* `__filename`: Absolute path of current file.

**Code Example:**

```js
console.log(__dirname);
console.log(__filename);
```

---

## 18. How to exit a Node process?

**Theory:**
Use `process.exit(code)` to terminate the current process.

**Code Example:**

```js
process.exit(0);
```

---

## 19. Explain child processes

**Theory:**
Child processes allow running other programs or commands asynchronously.

**Code Example:**

```js
const { exec } = require('child_process');
exec('ls', (err, stdout) => console.log(stdout));
```

---

## 20. What are Node.js timers?

**Theory:**
Functions to execute code after delay or repeatedly:

* `setTimeout`
* `setInterval`
* `setImmediate`
* `process.nextTick`

**Code Example:**

```js
setTimeout(() => console.log('Timeout'), 1000);
```

# 02. Node.js Asynchronous Patterns

---

## 1. What is the Node.js event loop (detailed)?

**Theory:**
The event loop is the mechanism that allows Node.js to perform non-blocking I/O despite being single-threaded. It delegates I/O operations to the system (libuv), registers callbacks, and processes those callbacks in phases. Understanding phases (timers, pending callbacks, idle/prepare, poll, check, close) is crucial to reason about ordering and performance.

**Code Example:**

```js
console.log('start');
setTimeout(() => console.log('timer'), 0);
Promise.resolve().then(() => console.log('microtask'));
console.log('end');
// Output: start, end, microtask, timer
```

---

## 2. Microtasks vs Macrotasks

**Theory:**
Microtasks (e.g., `Promise.then`, `process.nextTick`) run after the current operation finishes but before the next macrotask. Macrotasks are scheduled by timers, setImmediate, I/O callbacks. In Node, `process.nextTick` runs before other microtasks.

**Code Example:**

```js
process.nextTick(() => console.log('nextTick'));
Promise.resolve().then(() => console.log('promise'));
setImmediate(() => console.log('setImmediate'));
// Output: nextTick, promise, setImmediate
```

---

## 3. Order: setTimeout(0) vs setImmediate

**Theory:**
`setTimeout(fn, 0)` schedules a timer in the timers phase. `setImmediate` queues a callback in the check phase. Order can differ depending on when called (I/O callbacks vs top-level). In I/O callbacks, `setImmediate` often runs sooner.

**Code Example:**

```js
const fs = require('fs');
fs.readFile(__filename, () => {
  setTimeout(() => console.log('timeout'), 0);
  setImmediate(() => console.log('immediate'));
});
```

---

## 4. process.nextTick() explained

**Theory:**
`process.nextTick` queues a callback to run immediately after the current operation, before the event loop continues. Overusing it can starve the event loop.

**Code Example:**

```js
process.nextTick(() => console.log('nextTick')); console.log('sync');
// Output: sync, nextTick
```

---

## 5. Blocking vs Non-blocking I/O

**Theory:**
Blocking I/O (e.g., `readFileSync`) halts the event loop until complete. Non-blocking I/O (`fs.readFile`) delegates operations and uses callbacks/promises, keeping the event loop free to handle other tasks.

**Code Example:**

```js
const fs = require('fs');
// Blocking
const data = fs.readFileSync('large.file');
// Non-blocking
fs.readFile('large.file', (err, data) => {});
```

---

## 6. Callback pattern and "error-first" callbacks

**Theory:**
Node uses error-first callbacks `(err, result)` where `err` is non-null on error. This standard simplifies error handling in async callbacks.

**Code Example:**

```js
fs.readFile('file', (err, data) => {
  if (err) return console.error(err);
  console.log(data.toString());
});
```

---

## 7. Promises – basics and chaining

**Theory:**
Promises represent eventual completion/failure of async operations and allow chaining via `then` and `catch`. They avoid deep callback nesting and provide clearer flow control.

**Code Example:**

```js
readFileAsync('file')
  .then(data => doWork(data))
  .then(result => console.log(result))
  .catch(err => console.error(err));
```

---

## 8. util.promisify and promisifying callbacks

**Theory:**
`util.promisify` converts Node-style callbacks into functions returning Promises, enabling `async/await` usage.

**Code Example:**

```js
const { promisify } = require('util');
const fs = require('fs');
const readFile = promisify(fs.readFile);
await readFile('file.txt','utf8');
```

---

## 9. async/await fundamentals

**Theory:**
`async` functions return Promises. `await` pauses execution until a promise resolves (without blocking the event loop). Use try/catch to handle rejections.

**Code Example:**

```js
async function fetchData() {
  try {
    const data = await getData();
    console.log(data);
  } catch(e) { console.error(e); }
}
```

---

## 10. Parallel vs Sequential async operations

**Theory:**
Running `await` inside a loop executes sequentially. For parallel execution, start multiple promises and use `Promise.all` to await them together.

**Code Example:**

```js
// sequential
for (const url of urls) { await fetch(url); }
// parallel
await Promise.all(urls.map(u => fetch(u)));
```

---

## 11. Promise.all vs Promise.allSettled vs Promise.race vs Promise.any

**Theory:**

* `Promise.all` rejects on first rejection.
* `Promise.allSettled` waits for all and returns statuses.
* `Promise.race` resolves/rejects on first settled promise.
* `Promise.any` resolves on first fulfillment, rejects if all reject.

**Code Example:**

```js
Promise.allSettled([p1,p2]).then(console.log);
```

---

## 12. Handling async errors properly

**Theory:**
Always handle rejections. For Promises use `.catch()` or `try/catch` with `async/await`. Unhandled rejections may crash or leak.

**Code Example:**

```js
process.on('unhandledRejection', (reason) => console.error('Unhandled', reason));
```

---

## 13. Unhandled promise rejections

**Theory:**
Node emits an `unhandledRejection` event. In recent Node versions, unhandled rejections may terminate the process in future. Always catch or handle promises.

**Code Example:**

```js
Promise.reject(new Error('fail'));
// add: process.on('unhandledRejection', fn)
```

---

## 14. async\_hooks — tracking async resources

**Theory:**
`async_hooks` provides lifecycle hooks for async resources. Useful for debugging, tracing, correlation IDs across async boundaries.

**Code Example:**

```js
const async_hooks = require('async_hooks');
const hooks = async_hooks.createHook({ init(id, type) { console.log('init', id, type); } });
hooks.enable();
```

---

## 15. Using streams with async/await

**Theory:**
Node streams are async by nature. Use `stream.pipeline` (with promisify) or `for await...of` on readable streams for async iteration.

**Code Example:**

```js
const { pipeline } = require('stream');
const { promisify } = require('util');
const pipe = promisify(pipeline);
await pipe(fs.createReadStream('in'), fs.createWriteStream('out'));
```

---

## 16. Backpressure in streams

**Theory:**
Backpressure prevents fast producers from overwhelming slow consumers. Streams manage it via `pause()`/`resume()` and `highWaterMark`.

**Code Example:**

```js
readable.on('data', chunk => {
  const ok = writable.write(chunk);
  if (!ok) readable.pause();
});
writable.on('drain', () => readable.resume());
```

---

## 17. Async Iterators (for await...of)

**Theory:**
Async iterators allow consuming async streams or data sources using `for await...of`, making code clearer than event handlers.

**Code Example:**

```js
for await (const chunk of fs.createReadStream('file')) {
  console.log(chunk);
}
```

---

## 18. Cancellation patterns in Node.js

**Theory:**
Promises lack built-in cancellation. Use `AbortController` (supported by fetch and some APIs), or libraries, or design cancellation tokens.

**Code Example:**

```js
const ac = new AbortController();
fetch(url, { signal: ac.signal });
ac.abort();
```

---

## 19. AbortController with async operations

**Theory:**
`AbortController` provides a `signal` to propagate cancellation. Many modern APIs accept it (e.g., `fetch`, some DB drivers).

**Code Example:**

```js
const controller = new AbortController();
setTimeout(()=>controller.abort(), 1000);
try { await fetch(url, { signal: controller.signal }); } catch(e) { console.log('aborted'); }
```

---

## 20. Implementing retries and exponential backoff

**Theory:**
Retry transient failures with exponential backoff to reduce load. Increase delay between attempts and cap retries.

**Code Example:**

```js
async function retry(fn, times=3){
  let delay=100; while(times--){ try{return await fn();}catch(e){ if(!times) throw e; await new Promise(r=>setTimeout(r,delay)); delay*=2; }}
}
```

---

## 21. Queueing and concurrency control

**Theory:**
Limit concurrent async operations via semaphores or libraries (p-limit). Useful to prevent resource exhaustion when calling many APIs.

**Code Example:**

```js
const pLimit = require('p-limit');
const limit = pLimit(5);
await Promise.all(urls.map(u => limit(() => fetch(u))));
```

---

## 22. Promise-based fs API (fs.promises)

**Theory:**
Node provides `fs.promises` for Promise-based file operations, simplifying `async/await` usage instead of callbacks.

**Code Example:**

```js
const fs = require('fs').promises;
const data = await fs.readFile('file.txt', 'utf8');
```

---

## 23. I/O starvation and CPU-bound tasks

**Theory:**
Long CPU-bound tasks block the event loop. Offload heavy computations to `worker_threads` or external services to keep responsiveness.

**Code Example:**

```js
const { Worker } = require('worker_threads');
new Worker('./heavy.js');
```

---

## 24. worker\_threads vs child\_process

**Theory:**

* `worker_threads` run in same process with separate V8 isolates and shared memory (via SharedArrayBuffer).
* `child_process` spawns separate OS processes for isolation and separate memory.

**Code Example:**

```js
// Worker
const { Worker } = require('worker_threads');
new Worker('./worker.js');
```

---

## 25. EventEmitter memory leak warnings

**Theory:**
Adding many listeners to an emitter may cause memory leak warnings. Use `emitter.setMaxListeners()` or remove listeners when done.

**Code Example:**

```js
emitter.setMaxListeners(50);
```

---

## 26. Using async/await with try/catch/finally

**Theory:**
Wrap `await` calls in `try/catch` to handle rejections and use `finally` for cleanup.

**Code Example:**

```js
try { await doAsync(); } catch(e) { console.error(e); } finally { cleanup(); }
```

---

## 27. Throttling and debouncing server requests

**Theory:**
Throttling limits request handling rate; debouncing groups rapid calls. Implement at API gateway or within request handlers to protect services.

**Code Example:**

```js
// simple token bucket pseudo
```

---

## 28. Using async with Express middleware

**Theory:**
Express supports async middleware; ensure errors are passed to `next(err)` or thrown and caught by error-handling middleware.

**Code Example:**

```js
app.get('/', async (req, res, next) => { try { const data = await db.query(); res.json(data); } catch(e){ next(e); } });
```

---

## 29. Error handling in async Express routes

**Theory:**
Wrap async route handlers so rejections are forwarded to Express error handlers or use helper wrappers to avoid repetitive try/catch.

**Code Example:**

```js
const wrap = fn => (req,res,next)=>Promise.resolve(fn(req,res,next)).catch(next);
app.get('/', wrap(async (req,res)=>{ /*...*/ }));
```

---

## 30. Streaming responses in Express

**Theory:**
Stream large responses (files, DB cursors) to the client to save memory and send data progressively.

**Code Example:**

```js
app.get('/file', (req, res) => fs.createReadStream('big').pipe(res));
```

---

## 31. Handling timeouts and slow clients

**Theory:**
Use request timeouts and server timeouts to avoid holding resources for slow or malicious clients.

**Code Example:**

```js
server.setTimeout(5000); // in ms
```

---

## 32. Graceful shutdown with async operations

**Theory:**
On SIGTERM, stop accepting new connections, wait for ongoing requests to finish, then close server and cleanup resources.

**Code Example:**

```js
server.close(()=>process.exit(0));
```

---

## 33. Using Performance API (performance.now)

**Theory:**
`perf_hooks.performance.now()` measures high-resolution timestamps to profile async operations.

**Code Example:**

```js
const { performance } = require('perf_hooks');
const t0 = performance.now(); await doAsync(); console.log(performance.now()-t0);
```

---

## 34. Detecting memory leaks in async code

**Theory:**
Memory leaks often arise from unreleased references (timers, caches, closures). Use heap snapshots and profiling to find leaks.

**Code Example:**

```js
// run Node with --inspect and take heap snapshots in DevTools
```

---

## 35. Using async iterators for paginated APIs

**Theory:**
Implement paginated data consumption using async iterators to process pages sequentially with `for await...of`.

**Code Example:**

```js
async function* pages() { let page=1; while(true){ const res=await fetchPage(page++); if(!res.length) break; yield res; } }
for await (const p of pages()) { process(p); }
```

---

## 36. async/await and stack traces

**Theory:**
Async stack traces may be less straightforward. Node's async hooks and `Error.captureStackTrace` help preserve or inspect async call chains.

**Code Example:**

```js
try { await fn(); } catch(e) { console.error(e.stack); }
```

---

## 37. Integrating AbortController with streams

**Theory:**
Abort signals can be used to stop streaming operations, e.g., abort fetch or custom stream logic.

**Code Example:**

```js
const ac = new AbortController(); readable.pipe(writable); ac.signal.addEventListener('abort', ()=>readable.destroy());
```

---

## 38. Promise utilities: allSettled usage patterns

**Theory:**
`Promise.allSettled` helps when you need results of all operations regardless of failures (bulk processing, logging successes and failures).

**Code Example:**

```js
const results = await Promise.allSettled(tasks.map(t=>t()));
```

---

## 39. Handling async initialization before server start

**Theory:**
Await necessary setup (DB, caches) before `app.listen` to ensure readiness and avoid runtime errors.

**Code Example:**

```js
await db.connect(); server.listen(3000);
```

---

## 40. Async resource leaking in event listeners

**Theory:**
Unremoved listeners on long-lived objects can leak memory. Remove listeners (`off`/`removeListener`) when done.

**Code Example:**

```js
emitter.on('event', handler); emitter.off('event', handler);
```

---

## 41. Combining callbacks, promises, and async/await safely

**Theory:**
When mixing patterns, wrap callback APIs with promisify and prefer `async/await` for clarity. Ensure error paths are handled and do not mix callback error handling with promise rejections unintentionally.

**Code Example:**

```js
const fn = util.promisify(old);
await fn();
```

---

## 42. Using setImmediate in long-running loops

**Theory:**
Yield to the event loop by scheduling `setImmediate` inside heavy loops to keep the server responsive.

**Code Example:**

```js
function heavy(items){ function next(){ if(!items.length) return; processItem(items.shift()); setImmediate(next); } next(); }
```

---

## 43. Async testing strategies (Jest/Mocha)

**Theory:**
Use `async/await` or return promises in tests. Mock timers or network, and ensure cleanup of async resources to avoid flakiness.

**Code Example:**

```js
test('async', async ()=>{ await expect(fetchData()).resolves.toBeDefined(); });
```

---

## 44. Handling race conditions in async code

**Theory:**
Race conditions arise when multiple async operations interact with shared mutable state. Use locks, atomic DB operations, or Redis-based locks to prevent races.

**Code Example:**

```js
// pseudo: acquire lock, check/update, release lock
```

---

## 45. Async logging and correlation IDs

**Theory:**
Correlate async operations with request IDs using middleware and propagate IDs across async boundaries (async\_hooks or explicit passing) to trace logs.

**Code Example:**

```js
req.id = uuid(); logger.info({ reqId: req.id }, 'start');
```

---

## 46. Async resource cleanup on process exit

**Theory:**
Handle signals (`SIGINT`, `SIGTERM`) to close DB connections, flush queues, and stop accepting new requests before exit.

**Code Example:**

```js
process.on('SIGTERM', async ()=>{ await db.close(); process.exit(0); });
```

---

## 47. Performance considerations for async DB queries

**Theory:**
Use connection pooling, prepared statements, batch queries, and limit parallelism to avoid overloading DB with concurrent async operations.

**Code Example:**

```js
// use pool.query instead of opening new connections per request
```

---

## 48. Async pagination patterns (cursor vs offset)

**Theory:**
Cursor-based pagination is more efficient and consistent for large datasets; offset can be simpler but suffers from inconsistency and performance issues.

**Code Example:**

```js
// cursor: query after last id
SELECT * FROM items WHERE id > $1 ORDER BY id LIMIT $2
```

---

## 49. Best practices for async error handling

**Theory:**
Centralize error handling, always catch promise rejections, use logging with context, return proper HTTP status codes for APIs, and avoid silent failures.

**Code Example:**

```js
app.use((err, req, res, next) => { console.error(err); res.status(500).json({error: 'Internal'}); });
```

---

## 50. Resources & debugging tools for async Node.js

**Theory:**
Use `node --inspect` for debugger, `async_hooks` for tracing, Chrome DevTools for CPU/memory profiling, `clinic` (Doctor/Flame/Heap) for performance analysis, and `0x`/`clinic` for flamegraphs.

**Code Example:**

```bash
node --inspect-brk index.js
npx clinic doctor -- node server.js
```


