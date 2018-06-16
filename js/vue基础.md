# vue

## vue基本
1. ##### MVVM核心图
    ![mvvm](https://github.com/SuperCourierYangyufan/notes/blob/master/img/mvvm.PNG)
2. ##### 基本语法
    * {{}}动态从vue的data获取数据
        * 可以进行js操作,如{{msg.toUpperCase()}} 
    * 进行强制数据绑定(v-bind)
        * 在data中获取值,在属性前加:号
        * 可以对class,img这些进行强制绑定,然后进行动态更换
        ```
            <img :src="imgurl">                                     //在data写 imgurl:"值"
            <p :class="{class1:true,class2:false}">Class样式选择</p> //可以使用class1,不能使用class2
            <p :class="{class1:isA,class2:isB}">Class样式选择</p>    //当通过触发事件,函数内this.is(A|B) = (true|false)
            <h2 :style="{color: optioncolor}">2. style绑定</h2>     //在data写optioncolor:"值"
        ```
    * 进行事件监听(v-on)
       * @事件操作="函数",在vue内methods中构写函数
       * test(msg)函数中参数可以传入data中的数据
        ```
       <button @click="test()">test1</button>
       .....vue内
       methods:{
                   test(msg){
                       alert(msg);
                   }
               }
        ```
    * 数据计算
        * 动态获取data数据进行操作
        * firstName,lastName,fullName1,2均为v-model的值
        ```
                    computed:{
                        fullName1(){
                            return this.firstName+' '+this.lastName;
                        }
                    },
        ```
    * 监视
        * 当监视的对象发生改变则进行操作
        * 监视对象:操作函数
        * newvalue:改变后的值,oldvalue:改变前的值
        ```
             watch:{
                        firstName: function (newvalue,oldvalue) {
                            this.fullName2 = newvalue+'   '+this.lastName;
                        }
                     }
        ```
        * 通过vue的实例vm执行同样的操作
        ```
         vm.$watch("lastName",function (newvalue,oldvalue) { 
                 this.fullName3 = newvalue+'   '+this.lastName;
             });
        ```
    * 双向数据调用改变
        * 每个属性都有get,set方法
        * get(),当需要读取当前属性时回调
        * set(value),当属性值发生改变时回调,value就是最新属性值
        ```
            computed:{
                fullName3:{ //设置为属性,而不是方法
                    get(){
                        return this.firstName+' '+this.lastName;
                    },
                    set(value){ 
                        const arr = value.split(' ')
                        this.firstName = arr[0];
                        this.lastName = arr[1];
                    }
    
                }       
            }
        ```
3. ##### 基本标签
    * 判断标签
        * 通过在data修改ok(布尔类型)的值,从而显示不同的结构
        ```
                <p v-if="ok">OK</p>
                <p v-else>ON</p>
                <!--if 为删除标签-->
                <p v-show="ok">上</p>
                <p v-show="!ok">下</p>
                <!--show 为隐藏标签-->
        ```
    * 循环标签
        * v-for="(每个值,坐标) in 循环对象" :key="坐标"
        * 在对数组进行操作时,vue只对对象本身进行监视,而对象里面的值改变时不会刷新,所以arr[0]=1;这种操作不会页面刷新,所以需要使用方法函数
        * splice(index,1) //删除|splice(index,1,NewVal)//修改|splice(index,0,NewVal)//添加
        ```
                     <ul>
                        <li v-for="(val,index) in persons" :key="index">
                            {{index}}--{{val.name}}--{{val.age}}
                            ---<button @click="deleteP(index)">删除</button>
                            ---<button @click="updateP(index,{name:'cat',age:val.age})">更新</button>
                        </li>
                    </ul>
        ```
    * 双向数据绑定 v-model
    * 元素标示
        * 对一个元素 ref="自定义名字"
        * this.$refs.自定义名字 便可以拿到该标签
4. ##### 事件处理
    * 当事件 @click="fun()"中,参数为空时,会自带一个event属性,通过event.target.(innerHTML|value) 获取值
    * 若参数不为空,想获取event对象,则在参数中添加$event即可
    * 当要取消事件的冒泡,在里面的事件@click.stop="fun()"便可   
    * 当要取消事件的默认行为,@click.prevent="fun()"
    * 当要按键触发事件,@keyup.按键的keyword|按键的名字 = "fun()"
5. ##### 生命周期方法
    * 初始化显示之后立即调用 mounted(){使用箭头函数} //顶层属性
    * 在销毁之前的方法,如去除定时器beforeDestroy(){} //顶层属性
    * 销毁mounted.进行事件触发后调用 this.$destroy();
6. ##### 动画
    * 将需要进行动画效果的样式用`<transition name="自定义名字">`包裹
    * 将动画效果分为四个阶段
        * 动画开始->隐藏的过度效果->隐藏时的样式->显示过度效果
        * css中设置以下样式
            * .自定义名字-leave-active{隐藏的过度效果}
            * .自定义名字-entry,.自定义名字-leave-to{隐藏时的样式}
            * .自定义名字-entry-active{显示过度效果}
7. ##### 过滤器
    * date为通过new Date();获取的时间
    * Vue为大写,修改的为对象属性
    ```
        <p>{{date | dateToString}}</p>
    
        Vue.filter("dateToString",(value)=>{
                return moment(value).format("YYYY-MM-DD HH:mm:ss");
            });
            
        //moment需要导入库
    ```
    

