# vuex
1. ##### 架构
	* ![vuex](https://github.com/SuperCourierYangyufan/notes/blob/master/img/vuex.PNG)
2. ##### 使用
    * cnpm install --save vuex
    * 编写store.js
        * 导入模块,并声明使用
            ```
                //vuex核心管理模块
                import Vue from "vue";
                import Vuex from "vuex";
                Vue.use(Vuex);
            ```
        * 向外声明暴露
            ```
                export default new Vuex.Store({
                  state, //状态,初始化
                  mutations, //包含多个更新state的函数
                  actions, //包含多个对应事件回调函数
                  getters //包含多个计算属性函数,计算属性,computed
                });
            ```
        * 编写暴露的属性
            ```
                const state = {},
                const mutations = {},
                const actions = {},
                const getters = {}
            ```
    * 编写main.js
        * 导入模块
            * `import store from "./store/store.js";`
        * Vue里声明
            ```
                new Vue({
                .......
                  store:store
                })
            ```
    * 组件中构写
        * 使用公用属性值
            * `this.$store.state.count`
        * 使用公用属性计算赋值
            * 组件中
                ```
                computed:{
                    计算赋值的对象(){
                      return this.$store.getters.计算赋值的对象
                    },
                  },  
                ```
            * store.js中
                ```
                    const getters={
                      计算赋值的对象(state){
                        return 计算赋值操作,通过state.属性.来获得公用属性值
                      }
                    };
                ```
        * 更新公用属性操作
            * 组件中
                ```
                    函数名(){
                          this.$store.dispatch("函数名",携带参数);
                        },
                ```
            * store.js中
                ```
                    const mutations = {
                      改变公用属性的函数(state,{可传递值}){
                         改变公用属性的操作,直接使用可传递值
                        (如:state.公用属性+=可传递值;)
                      }
                    };
                    const actions ={ //state若不需要可不写,携带参数若没有可不写
                      lesscount({commit,state},携带参数){
                        逻辑操作
                        commit("改变公用属性的函数",{可传递值});
                      },
                    };
                ```
3.##### 代码简写
   * 在App.vue中
        ```
            import {mapState,mapGetters,mapActions} from "vuex";
            computed:{
                ...mapState(["公用属性值1","公用属性值2"]),
                ...mapGetters(["公用属性计算值1"....])
              },
            methods:{
                 ...mapActions(["更新公用属性函数1","更新公用属性函数2"....]), //若需要传值,则需要手动调用
            }
        ```
   * 在store.js中,this为空,需要什么对象里的值或方法,便传入该对象便可
            