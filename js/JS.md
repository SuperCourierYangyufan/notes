# javascript基础
### 基础
1. ##### 字符串数字  Number(字符串)|parse(Int/Float)(字符串(,10转为10进制数字))
2. ##### ox123(16进制数字)  0123(8进制) 0b1234(2进制)
3. ##### Object 创建: 对象名 = new Object();  对象名.属性名 = 值; ......
    *  var 对象名 = {属性名:值,属性名:值,属性名:值....}
    *  读取: var 定义名 = 对象名.属性名
    *  修改  对象名.属性名 = 新值
    *  删除 delete 对象名.属性名
    *  判断是否存在某属性(true|false)  属性名 in 对象名
    *  arr.username == arr[username]
    *  var 变量 = "A";   对象名[变量]  ==  对象名[A] 对象名[A] = 值   通过变量动态获取值
4. ##### java中=全是值的拷贝,若是基本类型则复制一份值,若是字符串等引用类型,则是复制地址,所以当赋予对象值改变新创建一个值时,被赋予地址并不会改变|而javascript也是如此,不同的是javascript中的String是基本数据类型,所以会进行值传毒
5. ##### 因为javascript没有常量池,所以当创建两个Object类时,分别添加相同属性相同值时,会分别往两个对象中添加字符串,所以地址不相等
6. ##### for(var i in 对象名){console.log(对象名[i]);}  //循环遍历取出每个属性i再取出每个值
7. ##### 使用var声明变量(函数),会在所有代码执行之前声明，不会赋值。
8. ##### function 函数名(){} 会在所有代码执行之前就被创建.
9. ##### var arr = new Array(可选:长度); | var arr = [值1,值2,..];    //数组
    *  arr.length //数组长度        
    *  arr.push(参数,参数。。);// 添加参数,并返回长度
    *  arr.pop() //删除最后一个元素,并返回删除元素  
    *  arr.slice(start,(可选:end)) 从start开始截取 到end结束 来截取数组(不包含end)
    *  arr.forEach(function(value,index,Object)){}; value:当前遍历的元素 index:索引，0开始，Object:当前遍历的数组
10. ##### MyClass.prototype为原型对象,为一个公共区,所有构造函数当自身没有某值时,可以自动从原型对象中获得
11. ##### String() Number() Boolean() 可转化为包装类
12. ##### 正则表达式 var reg = new RegExp("正则表达式","匹配模式(i:忽略大小写,g:全局匹配)");  reg.test(string);检查是否符合正则表达式规则

### DOM
1. ##### window.onload = function () {页面加载完成后执行的代码}
2. ##### document对象方法 Translation()通过id获得一个|getElementsByTagName()通过标签名获得一组对象|getElementsByName()通过name属性获得一组对象
   * 通过具体元素节点调用
     * getElementByTagName()返回当前节点的指定标签后代属性childNodes 所有子节点|(first,last)Child 表示第一个子节点|最后一个
     * parentNode 父节点|previousSibling 前一个兄弟节点|nextSibling 后一个兄弟节点
   * 通过css选择器 .querySelectorAll("css选择器"); 多个,数组
   * 增删改 createElement("(li|input)"); //创建节点  createTextNode("文本内容") //创建一个文本节点  父.appendChild(子) //向一个父节点添加一个新的子节点
        *  父.insertBefore(新子节点,旧子节点)//向父节点中插入一个新子节点在旧子节点之前   父.removeChild(子) //删除子节点(自身.parentNode.removeChild(自身))
        *   父.innerHTML //自身里面的html代码 (父.innerHTML+='代码')
   * 修改css  元素.style.样式名 = "值";(CSS样式中含有-号,修改为驼峰命名法)(修改的为内联样式,读取也是内联的)
               *  -若修改非内联,获取当前生效的css样式的对象 window.getComputedStyle(对象id,null)获得的是对象, .修改属性
3. ##### js修改属性(img的src属性)便对象.属性 = 值(myimg.src="1.jpg");
4. ##### 对于事件可以传入event 可以得到x，y坐标等属性
5. ##### 事件冒泡:后代事件触发,祖先的相同事件也会触发 event.cancelBubble = true; //取消冒泡
6. ##### 元素.addEventListener("事件不加on",function () {},false);  //可以添加多个相同的事件

### BOM
1. ##### window -代表整个浏览器的窗口,同时window也是网页中的全局对象    Navigator -代表浏览器的信息,识别不同浏览器  Location - 代表浏览器的地址信息
   * History - 代表浏览器的历史记录    Screen  - 代表用户的屏幕信息，显示器的纤细
2. ##### location.reload();  刷新页面  若传入true会强制清除缓存刷新页面
3. ##### setInterval("函数()",毫秒);  每多少毫秒执行函数一次     setTimeout("函数()",毫秒);  多少毫秒后执行函数  前面分别带out的方法可以取消定时

### Es6
1. ##### 严格模式
    * 在js代码块中第一句加上 'use strict'; 来开启严格模式
    * 必须使用var声明变量
    * eval("解析js语句运行"); 严格模式会有自己的作用域,不会污染全局
2. ##### JSON
    * JSON.stringify(json类型对象);   //json类型数组->json
    * JSON.parse(json对象);          //json->json类型数组
3. ##### Object扩展
    * create方法
    ```
    var obj = {username:"admin","age":8};
    newobj = Object.create(obj,{
        sex:{value:'男',writable:true,configurable:true,enumerable:true}
        //新增属性,可以  修改           删除              可以 var i in 集合  循环
    });
    ```
    * obj1.__proto__ = obj2; //让obj2称为obj1的父类 (两个下划线)
4. ##### 数组的扩展
   * Array.from(arr) //将伪数组装为真数组
   * arr.indexOf(); //获取下标元素(1|2|3...)的值
   * arr.remove(); // 删除 ....................
   * arr.map(function(item,index){return item+1}); //数组元素所有+1
   * arr.filter(function(item,index){return item>3}) //获得一个新数组,值大于
5. ##### 函数的扩展
   * 函数.call(obj,值...);  函数.apply(obj,[值...]); //这两个方法均是将函数的this绑定为obj对象
   * bind(obj); 也是将函数的this绑定为obj,但是并不会立即调用
6. ##### 新关键字
   * let 来取代var
   * const 定义常量
7. ##### 解构赋值
    * let{赋值属性,赋值属性} = {解构属性,解构属性} ps:数组也ok
    * let{obj属性1,obj属性2} = obj
8. ##### 模板字符串
    * 模板字符串必须用 ``包含，而不是"".
    * 变化的部分用${}   -> ${obj.username} 
9. ##### 箭头函数
    * let fun = (形参) =>{函数体};
        * 每有参数()不可省略 
        * 一个参数可以不加()
        * 就一条语句函数体可以不加{},会自动return
        * 若有{},需要return
        * 箭头函数的this为定义时处在的对象,往上层看非箭头函数的对象
10. ##### ...运算符
    * function(a,...b){}
    * ...可收集多个参数,存入b数组中 
    * ...只能放在参数最后位置
    * ...arr 可以用来遍历数组
11. ##### Promise函数解决回调地狱
    ```
        let promise = new Promise((resolve,reject) => {
           // 执行异步操作,通常为ajax,定时器
            setTimeout(()=>{
                console.log("定时器起作用了");
                //通过异步任务结果修改promise状态 resolve();成功 | reject();失败
                let num = 3000;
                resolve(num);
            },3000);
        
        });
        promise.then((data)=>{//成功的回调
            console.log("num的值为"+data);
        },(error)=>{//失败的回调
            console.log("失败了");
        });
    ```
12. ##### Iterator遍历器
    * for(let i of arr){}    
    * arr可以是数组,字符串,arguments,set,map
    * 对象遍历还是用for(let i in arr){}
13. ##### Generator函数
    * function后跟一个*
    * 每个next语句就是执行一块代码块,到yield结束,没有next则一行代码也不执行
    * sx.next();的返回值为yield返回的值,字符串直接赋值,代码返回代码语句
    ```
        function* 函数名(){
            //代码块1
            yield 语句1
            。。。。
        }
        
        let sx = 函数名();
        sx.next();
    ```
14. ##### class
    * 一般
        ```
            class Person{
                constructor(name,age){ //原型方法,构造函数
                    this.name = name;
                    this.age = age;
                }
                showUser(){ //一般方法 自定义
                    console.log(this.age+"   "+this.name);
                }
            }
            
            let person = new Person("t",18);
            person.showUser();
    
        ```   
    * 继承
        ```
            class People extends Person{
                constructor(name,age,sex){
                    super(name,age); //调用父类,必须
                    this.sex = sex;
                }
                showPeople(){
                    console.log(this.name+this.age+this.sex);
                }
            }
            
            let people = new People("x",999,"男");
            people.showUser(); //调用父类方法
            people.showPeople();__
        ```
15. ##### set,map
    * let set = new Set(); //可放一个数组,会自动去重
        * set.add(value) ;
        * set.delete(value) ;
        * set.has(value); //看是否有没有
    * let map = new Map(); //可以放二维数组[[key:value],[key:value]......]
        * map.add(key,value);
        * map.delete(key);
        * map.get(key);
    * for(let i of set|map){};
### 模块化
1. ##### 安装
    * 需要兼容性时采用
    * cnpm install babel-cli browserify -g
    * cnpm install babel-preset-es2015 --save-dev
2. ##### 配置文件
    * 项目目录下 .babelrc
    ```
        {
          "presets":["es2015"]
        }
    ```
3. ##### 暴露
    * 分别暴露
        ```
            export function fun() {
                console.log("fun");
            }
            export let arr = [1,2,3];
            
        ```
    * 全局暴露
        ```
            let fun1 =  () => {
                console.log("fun1")
            }
            let fun2 = () =>console.log("fun2");
            export {fun1,fun2};
        ```
4. ##### 引入 `import {暴露的对象....fun1,fun2} from "路径";`
5. ##### 转为ES5 
    ```
        babel 需要转化为ES5的js文件夹(子模块与总和) -d 转化后存放地址
        browserify 转换后总和js -o 存放地址(需手动创建文件夹)
    ```
### Webpack
1. ##### 安装
    * cnpm install webpack@3.8.1 -g //全局安装    
    * cnpm install webpack@3.8.1 --save-dev    //局部安装,并且加入依赖,并且打包不包含
2.  ##### 全局引入
    ```
        "devDependencies": {
            "webpack": "^3.8.1"
          }
    ```   
3. ##### 基本使用
    * webpack 主模块js地址 打包后模块js地址
4. ##### 使用配置文件快速打包
    ```
        const path = require("path");
        module.exports={  //记得进入cd进入当前空间
            entry: "./index.js",   //记得前面./
            output: {
                filename: "bundle.js",
                path:path.resolve(__dirname,'dist/')
            }
        };
    ```     
5. ##### 对图片与css进行打包
   * 安装环境
       * cnpm install css-loader style-loader --save-dev
       * cnpm install file-loader url-loader --save-dev
   * 入门js文件导入css
       * import "./css/test.css";
   * webpack.config.js修改
   ```
    const path = require("path");
    module.exports={  //记得进入cd进入当前空间
        entry: "./index.js",   //记得前面./
     // publicPath:"../dist/",  //可选,为打包后的位置
        output: {
            filename: "bundle.js",
            path:path.resolve(__dirname,'dist/')
        },
        module: {
            rules: [
                {
                    test: /\.css$/,      //css
                    use: [ 'style-loader', 'css-loader' ]
                },
            // {
            //     test: /\.(png|jpg|gif)$/,   //图片
            //     use: [
            //         {
            //             loader: 'url-loader',
            //             options: {
            //                 limit: 8192
            //             }
            //         }
            //     ]
            // }
            ]
        }
    };
   ```
        
       
