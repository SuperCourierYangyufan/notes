# Es6
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