# Node.JS
1. ##### 执行js文件(cmd)  
	* node 文件名.js
2. ##### 模块
    * require("引入文件");来引入模块 ps:必须以./(当前目录)或../开头
    * 每个js文件中的内容都包括在一个函数中,不是全局作用域
    * 若想暴露属性或方法,需要exports.属性
        * exports.add = function(){...};
        * 可以同时暴露多个module.exports={  
            .......................  
        }
    * 若是核心模块(别人提供的) require("名字")
        * 首先在node_modules中寻找
        * 若没有则会一直去上一级的node_modules去寻找
3. ##### npm
    * npm search 搜索内容
    * 下载 
        * npm init 创建package.json
        * npm install 名字
        * 若不创建package.json 就不会放在当前目录
        * npm install 名字 --save 下载并依赖
    * npm remove 删除的包
    * npm install 当前下载所依赖的包
    * npm install 包名 -g       全局安装包(共)
    * cnpm 使用为淘宝镜像
4. ##### Buff缓冲区,存储二进制,数组
    * var buff = Buffer.from("字符串");
    * var buff = Buffer.alloc(大小);
    * buff中存储的都是二进制
    * buff的大小一旦确定,不能修改
    * buff中每个点最大只能存储255
5. ##### File System 文件系统  var fs = require("fs");
    * 所有方法都有同步(阻塞程序的执行,带Sync)和异步的实现(带有callback回调函数)
    * w为重头写  a为追加 
    * 同步
        *  打开    var fd = fs.openSync("路径","打开操作类型(r 读 w 写)")  
        *  写入    fs.writeSync(fd,"写入文本",(起始位置));
        *  关闭    fs.closeSync(fd);
    * 异步 没有返回值
        * ```
                  fs.open("hello.txt","w",function (error,fd) { //有两个参数,错误优先
                      if(!error){ //没有出错
                          fs.write(fd,"hello",function (error) { //写入异步
                              if(!error){
                                  fs.close(fd,function (error) { //关闭异步
                                       if(error){
                                          console.log(error);
                                      }
                                  })
                              }else{
                                  console.log(error);
                              }
                          })
                      }else{
                          console.log(error);
                      }
                  });
          ```
    * 简单文件写入 fs.writeFileSync("路径","内容",{flag:"a"});| fs.writeFileSync
    * 流式文件写入 var ws = fs.createWriteStream("路径");  
                  ws.write("内容");  
                  。。。。。。。//可以一直写入 
                  ws.end(); //关闭
    * 简单文件读取 var text =fs.readFileSync("hello.txt").toString(); //直接读取为二进制文件
    * 流式文件
        var rs = fs.createReadStream("hello.txt");  
         rs.on("data",function (data) {流式文件写入});  
         rs.close();  
    * rs.pipe(ws);  //将可读流写到可读流,不需要打开或关闭语句了
                  
                  
    