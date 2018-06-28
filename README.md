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

