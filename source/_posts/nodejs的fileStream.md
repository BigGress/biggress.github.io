---
title: nodejs的fileStream
date: 2020-03-16 22:20:47
tags:
---

## 流是什么?

在流是一种数据的集合体, 如同TCP中的数据包一样, 将一个数据切分成多个数据包, 逐个发送给目标用户, 这个行为就称为Stream。而在node中所有的IO行为都可以是一个Stream, 因为一份文件、一个请求、一个输出都可以非常大, 所以它们可以被抽象成Stream。

流的四种类型
- Readable - 可读取数据的流
- Writable - 可写入数据的流
- Duplex - 可读又可写的流
- Transform - 在读写过程中可以修改或转换数据的 Duplex 流

## 流的作用是什么?

正如上面所说的, 如果让node一次读取一份巨大文件, 那么可能会直接占满所有的内存(甚至可能不够)。这时候Stream就非常有用了, 它可以只读取文件的一部分, 交由node处理, 处理完立马释放内存, 所以它自然可以处理大文件。

## 怎么用

今天主要就说说文件流怎么用~~~

- ### 读取文件

``` javascript
const fs = require('fs');
/**
 * test.js为你要读取的测试文件, 建议使用文本文件来测试
 * 这就是一个可读流
 */
const fileStream = fs.createReadStream('./test.md');
fileStream.on('open', function(fd){
  console.log('文件已打开');
});

fileStream.on('data', function(data){
  console.log('收到文件数据');
  console.log(data.toString());
});
```

- ### 写入文件

``` javascript
const fs = require('fs');

const writeStream = fs.createWriteStream('./test.md');

writeStream.write('test');
```

- ### 可读又可写流

``` javascript
const { Duplex } = require('stream');

const duplex = new Duplex();
const FILE_PATH = 'test.md';

duplex._read = function () {
  if (this._data && this._data.length > 1) {
    this.push(this._data);
    this._data = null;
  } else {
    this.push(null);
  }
}

duplex._write = function (buf, enc, next) {
  this._data = buf.toString();
  fs.appendFileSync(FILE_PATH, buf.toString());
  next()
}

duplex.on('data', (chunk) => {
  console.log(chunk.toString());
})

duplex.write('hahaha');
duplex.end();
```

- ### 转换流

``` javascript
const { Transform } = require('stream');

const transform = Transform({
    transform(chunk, encoding, cb){
        // 把数据转换成小写字母，然后push到缓存区
        this.push(chunk.toString().toLowerCase());
        cb();
    }
});

transform.write('D');

transform.on('data', (chunk) => {
  console.log(chunk.toString());
})
```

单个Stream本身没有什么特别大的作用, 但Stream强大的地方在于可以通过`pipe`方法将其进行组合, 下面进行演示。

## 组合使用

- ### 复制文件

``` javascript
const fs = require('fs');
const fileStream = fs.createReadStream('./test.md');
const writeStream = fs.createWriteStream('./back.md');

fileStream.pipe(writeStream);
```

- ### 将process.stdin输出文件中

``` javascript
const fs = require('fs');
const writeStream = fs.createWriteStream('./test.md');
process.stdin.pipe(writeStream);
```

- ### 读取其他命令行

``` javascript
const { Writable } = require('stream');
const { spawn } = require('child_process');
const lscmd = spawn('ls');

const writeStream = new Writable({
  write(chunk, encode, callback) {
    console.log(chunk.toString());
    callback();
  }
});
lscmd.stdout.pipe(writeStream);
```

- ### 将文件的大写字母变成小写

``` javascript
const fs = require('fs');
const { Transform } = require('stream');
const fileStream = fs.createReadStream('./back.md');


const transform = Transform({
    transform(chunk, encoding, cb){
        this.push(chunk.toString().toLowerCase());
        cb();
    }
});

fileStream.pipe(transform).pipe(process.stdout);
```

- ### node内置的转换流

1. zlib
``` javascript
const fs = require('fs');
const zlib = require('zlib');

fs.createReadStream('test.md')
  .pipe(zlib.createGzip())
  .pipe(fs.createWriteStream(file + '.gz'));
```

2. crypto
``` javascript
const fs = require('fs');
const crypto = require('crypto');
const password = '用于生成密钥的密码';
const key = crypto.scryptSync(password, '盐值', 24);
const iv = Buffer.alloc(16, 0);

const cipherStream = crypto.createCipheriv('aes-192-cbc', key, iv);
fs.createReadStream('gogo.js')
  .pipe(cipherStream)
  .pipe(fs.createWriteStream('./back.md'))
  .on('finish', () => console.log('Done'));
```