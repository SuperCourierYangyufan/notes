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



