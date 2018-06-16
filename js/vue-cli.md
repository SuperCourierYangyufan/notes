# vue-cli 
1. ##### 模板构成
    * vue init webpack 项目名称
    * 进入项目 npm install
    * 启动 npm run dev
    * 打包项目 npm run build
    * 对后台打包
        * 进入 build/webpack.prod.conf.js
        * 加入
        ```
            output:{
            .......
            publicPath: '/该项目名字/'
            
            }
        ```
        * 放入tomcat中就可以运行了
    * 忽略检查
        * .eslintignore中加入*.js *.vue
    * 取消文件压缩 webpack.prod.conf.js
        * css OptimizeCSSPlugin内容块注释
        * js UglifyJsPlugin内容块注释
        * html HtmlWebpackPlugin下的collapseWhitespace改为false
2. ##### 基本使用步骤
    * 将单个页面分为三个大的步骤 多个部件(.vue)——>主部件(app.vue)——>主js文件中(.js)
    * data在vue-cli中必须以函数的方式声明
        * 多个部件
            * 构写自己的模板,CSS,以及js
        * 主部件
            * 导入需要的部件 import 部件名 from "./路径.vue";
            * components{  
                部件名1,  
                部件名2,  
            }
            * 在模板中拼接,使用方式为<部件名/>变将部件导入至主部件
        * 主js文件中
            * 需导入vue,与主部件.请注意大小写
            * el为页面中的id
            * components,template写法基本一致
            ```
                import Vue from "vue";
                import App from "./app";
                
                new Vue({
                  el:"#app",
                  components:{
                    App
                  },
                  template:'<App/>'
                });

            ```
    * 组件之间的参数传递
        * 发送值
            * <部件名 :子组件接收名="父组件data中需要传递的值"
            * 对于方法的传递依旧如此 不需要加(参数),直接子组件接收名="方法名字"
        * 接收值
            * 接收代码
            ```
                props:{
                        子组件接收名:Object(类型),
                        子组件接收名:Function(类型),
                      },
            ```
        * 对于多组件的值传递,中间层也需要在props接收
        * 使用自定义事件通信传递方法(子组件可向父组件传递)
            * 绑定事件  <部件名 @组件接收名="函数名"/>
            * 子组件 this.$emit("组件接收名",方法所需要的值);
        * 使用消息订阅与发布
            * cnpm install --save pubsub-js
            * 导入  import PubSub from "pubsub-js";
            * 发布消息
                * PubSub.publish("消息名",传递值);
            * 订阅消息
                * PubSub.subscribe("消息名",(msg,data)=>{});
3. ##### 数据的缓存
    * 获取,在data中
        * const json = window.localStorage.getItem(key) //获得为字符串
        * Json.parse(json); 若为空值则为null
    * 存入,进行监视
        ```
            watch:{
                监视属性:{
                    deep:true, //深度监视
                    handler:function(newValue,oldValue){ //监视属性的value
                        //封装对象
                        //转为json   
                        const value = JSON.stringify(对象);
                        window.localStorage.setItem(key,value);
                    }
                }
            },
        ```
4. ##### ajax
    * cnpm install axios --save
    * import axios from "axios"; //导入
    ```
     mounted(){
              //Ajax
            const url = `http://api.github.com/search/repositories?q=vue&sort=stars`;
            axios.get(url).then(
              response =>{
                  const result = response.data; //获得数据
              }
            ).catch(error=>{
                alert("请求失败")
            });
          }
    ```