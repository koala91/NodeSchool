## NodeSchool
> nodeschool里面介绍node的课程，13个例子学习node基本知识。
---
1. 你好，世界
编写一个程序，在终端打印出'HELLO WORLD'
```
console.log('HELLO WORLD')
```
2. 婴儿学步
编写一个简单的程序，使其能接受一个或者多个命令行参数，并且在终端中打出这些参数的总和
> 可以通过 process 这个全局对象来获取命令行中的参数。process 对象拥有一个名为 argv的属性，该属性是一个数组，数组中包含了整条命令的所有部分。process.argv数组的第一个元素永远是node,第二个元素总是指向程序的路径。第三个才是参数。
```
let result = 0;
for (var i = 2; i < process.argv.length; i++) {
    result += Number(process.argv[i])
}
console.log(result)
```
3. 第一个I/O
编写一个程序，执行一个同步的文件操作系统，读取一个文件，并且在终端打印出这个文件中的内容的行(\n)数。读取文件的路径在命令行第一个参数提供。
> 对一个文件系统的操作，你将会用到fs这个node核心模块。
```
let fs = require('fs')
let data = fs.readFileSync(process.argv[2]).toString();
console.log(data.split('\n').length - 1)
```
4. 第一个异步I/O
同第三个问题一样，编写符合node.js风格的方式解决: 异步。
```
let fs = require('fs')
let file = process.argv[2]
fs.readFile(file, 'utf8', (err, data) => {
	if (err) throw console.log(err)
	console.log(data.split('\n').length -1)
})
```
5. LS过滤器
编写一个程序来打印指定目录下面的文件列表，并且已特定的文件名扩展名来过滤这个列表。这次会有两个参数提供给你，第一个是所给文件目录路径，第二个则是需要过滤出来的文件的扩展名。
> fs.readdir()方法接受两个参数，第一个是路径，第二个是回调函数。回调函数成功的参数包含的是每个文件的文件名。
```
let fs = require('fs')
let path = require('path')
fs.readdir(process.argv[2], (err, files) => {
	if(err) throw console.log(err)
	for(let i of files) {
		if(path.extname(i) === '.' + process.argv[3]) {
			console.log(i)
		}
	}
})
```
6. 使其模块化
问题和第五个一样，但是需要创建两个文件来解决问题
> 编写一个模块文件去做大部分的事情，这个模块必须导出(export)一个函数，这个函数将接受三个参数：目录名 文件扩展名 回调函数。绝对不能在模块文件中把结果打印到终端，只能在原始程序文件中编写打印结果的代码。
```
<!-- 6_1.js -->
let fs = require('fs')
let path = require('path')
module.exports = function (dir, filterStr, callback) {
  fs.readdir(dir, function (err, list) {
    if (err) return callback(err)
    let lists = list.filter((file) => {
    	return path.extname(file) === '.' + filterStr
    })
    callback(null, lists)
  })
}

<!-- 6_2.js -->
let filterFn = require('./6_1.js')
let dir = process.argv[2]
let filterStr = process.argv[3]
filterFn(dir, filterStr, function (err, list) {
  if (err) return console.error('There was an error:', err)
  list.forEach((file) => {
  	console.log(file)
  })
})
```
7. HTTP客户端
编写一个程序发起一个HTTP GET请求，所请求的URL为命令行参数的第一个，然后将每一个"data"事件所得的数据以字符串形式在终端的新的一行打印出来
```
let http = require('http')
let url = process.argv[2]

http.get(url, (res) => {
  res.setEncoding('utf8');
  res.on('data', (data) => { console.log(data) });
  res.on('error', (error) => { console.error(error) })
}).on('error', console.error)
```
8. 
编写一个程序，发起一个HTTP请求，请求的url为所提供给你的命令行参数的第一个，收集所有服务器所返回的数据，然后在终端用两行打印出来。打印的内容，第一行应该是一个整数，表示收到字符串内容长度，第二行则是服务器返回给你的完整的字符串结果。
```
let http = require('http')
let bl = require('bl')

let url = process.argv[2]

http.get(url, (res) => {
	res.pipe(bl((err, data) => {
		if (err) return console.error(err)
		console.log(data.toString().length)
		console.log(data.toString())
	}))
	
})
```
9. 玩转异步
这一次的问题和之前HTTP收集器很像，也需要hhtp.get()方法，然而，这一次由三个url作为前三个命令行参数给你提供。需要收集每一个URL所返回的完整内容，然后把他们在终端打印出来。必须按照这些URL在参数列表中的顺序将相应的内容排列打印出来才算完成。
```
let http = require('http')
let bl = require('bl')
let results = []
let count = 0

function printResults () {
  for (let i = 0; i < 3; i++)
    console.log(results[i])
}

function httpGet (index) {
  http.get(process.argv[2 + index], res => {
    res.pipe(bl( (err, data) => {
      if (err)
        return console.error(err)

      results[index] = data.toString()
      count++

      if (count == 3)
        printResults()
    }))
  })
}

for (let i = 0; i < 3; i++){
  httpGet(i)
}
```
10. 授时服务器
编写一个TCP时间服务器，你的服务器应当去监听一个端口，以获取一些TCP连接，这个端口会经由第一个命令行参数传递给你的程序。针对每一个TCP连接，你都必须写入当前的时间和24小时制的时间。如下格式"2017-12-14 23:20"。
```
var net = require('net')

function now () {
  var d = new Date()
  return d.getFullYear() + '-'
    + (d.getMonth() + 1).toString().padStart(2, '0') + '-'
    + (d.getDate()).toString().padStart(2, '0') + ' '
    + (d.getHours()).toString().padStart(2, '0') + ':'
    + (d.getMinutes()).toString().padStart(2, '0')
}
 var server = net.createServer(function (socket) {
   // socket 处理逻辑
   socket.end(now () + '\n')
 })
 server.listen(Number(process.argv[2]))
```
11. HTTP文件服务器
编写一个HTTP文件服务器，用于将每次所请求的文件返回给客户端。服务器需要监听所提供给你的第一个命令行参数所制定的端口。同时，第二个会提供给你的程序的参数则是所需要响应的文本文件的位置，这儿必须使用fs.createReadStream()方法以stream的形式作出请求响应。
```
let http = require('http');
let fs = require('fs');

let server = http.createServer(function (request, response) {
  // response.writeHead(200, { 'content-type': 'text/plain' })
  fs.createReadStream(process.argv[3]).pipe(response)
})
server.listen(Number(process.argv[2]))
```
12. HTTP大写转换器
编写一个HTTP服务器，他只接受POST形式的请求，并且将POST请求主体所带的字符转换成大写形式，然后返回给客户端。你的服务器需要监听由第一个命令行参数所指定的端口。
```
let http = require('http');
let map = require('through2-map');

let server = http.createServer(function (req, res) {
  if (req.method != 'POST')
      return res.end('send me a POST\n')

  req.pipe(map(function (chunk) {
    return chunk.toString().toUpperCase()
  })).pipe(res)
})
server.listen(Number(process.argv[2]))
```

