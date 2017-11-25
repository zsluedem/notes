Nodejs 全局对象
======

1.Buffer
---------
buffer 就是相似于普通的int array但是是固定size的

```js
// Creates a zero-filled Buffer of length 10.
const buf1 = Buffer.alloc(10);

// Creates a Buffer of length 10, filled with 0x1.
const buf2 = Buffer.alloc(10, 1);

// Creates an uninitialized buffer of length 10.
// This is faster than calling Buffer.alloc() but the returned
// Buffer instance might contain old data that needs to be
// overwritten using either fill() or write().
const buf3 = Buffer.allocUnsafe(10);

// Creates a Buffer containing [0x1, 0x2, 0x3].
const buf4 = Buffer.from([1, 2, 3]);

// Creates a Buffer containing UTF-8 bytes [0x74, 0xc3, 0xa9, 0x73, 0x74].
const buf5 = Buffer.from('tést');

// Creates a Buffer containing Latin-1 bytes [0x74, 0xe9, 0x73, 0x74].
const buf6 = Buffer.from('tést', 'latin1');
```

2.__dirname
-----
表示当前module的绝对路径，相当于`path.dirname(__filename)`

```es6
console.log(__dirname);
// Prints: /Users/mjr
console.log(path.dirname(__filename));
// Prints: /Users/mjr
```

3.__filename
------
表示当前module解析后的绝对路径（包含文件名）

```es6
console.log(__filename);
// Prints: /Users/mjr/example.js
console.log(__dirname);
// Prints: /Users/mjr
```

4.exports 
-------
相当于module.exports

5.console
--------
提供debug输出的信息

```
console.log('hello world');
// Prints: hello world, to stdout
console.log('hello %s', 'world');
// Prints: hello world, to stdout
console.error(new Error('Whoops, something bad happened'));
// Prints: [Error: Whoops, something bad happened], to stderr

const name = 'Will Robinson';
console.warn(`Danger ${name}! Danger!`);
// Prints: Danger Will Robinson! Danger!, to stderr
```

6.require
------
引入包的关键字

7.global
------
全局的对象

8.process
------
process是一个全局对象，提供当前nodejs 进程的一些信息，如环境变量等等

9.timer的一些函数
-------
1. clearImmediate(immediateObject)
2. clearInterval(intervalObject)
3. clearTimeout(timeoutObject)
4. setImmediate(callback[, ...args])
5. setInterval(callback, delay[, ...args])
6. setTimeout(callback, delay[, ...args])

都是用于提供一些api给nodejs 将来一段时间调用
