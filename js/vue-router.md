# vue-router
1. ##### 基本使用
    * cnpm install vue-router --save
    * 构写路由组件
    * 构写路由js
        ```
            import Vue from "vue";
            import VueRouter from "vue-router";
            import About from "../views/About.vue";
            Vue.use(VueRouter)
            
            export default new VueRouter({
              //n个路由
              routes:[
                {
                  path:"/about",  //路径
                  component:About //模块
                },
                {
                  path:"/",       //初始化
                  redirect:"/about" //初始化转发
                }
              ]
            });
        ```
    * main.js
        * 导入import router from "./router/index.js"
        * Vue新增属性router:router
    * 页面中
        * <router-link to="/about">about</router-link> //约等于<a>标签
        * <router-view></router-view> 为对应展示的组件
2. ##### 嵌套路由
    * 在父组件的路由配置中添加children属性
    * children:[{path: component},{}]
    * path属性要在路径之前加上父组件路径,或不加/
    * 若想初始化显示某组件,与基本相似,path:""便可
3. ##### 路由缓存
    * 在<router-view>外包裹一层<keep-alive>
4. ##### 参数传递
    * 路径携带id后台查询 this.$route为当前组件
        * 在path中可以以path:"/about/:id"来进行占位
        * <router-link :to="`/about/${val.id}`"> 以es6的方式进行动态赋值
        * 子组件可通过this.$route.params.id*1 获取id,通过ajax查询
        * 通过监视watch:{$route:function(value){重新去查询对象}} //因为同一个组件中mounted只初始化一次
    * 通过标签携带
        *<router-view :变量名:值></router-view>
        * props接收
5. ##### 编程式路由导航  this.$router为路由器
        * this.$router.(push|rereplace)(路由路径);
        * push:可以回退为上一次的操作组件,rereplace:为新路由替换当前路由,为不可回退
        * $router.back() //回退上一个组件
    