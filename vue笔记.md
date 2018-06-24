## 1.vue知识点

### 1.生命周期函数

- new Vue创建一个实例对象
- 当执行到beforeCreate函数的时候，
  - data数据还没有初始化好
  - 此时methods中的方法也没有初始化
- created(){}这是一个第二个声明周期函数，创建阶段
  - data和methods已经初始化完成，此时可以随意访问其中的数据和方法
- 这阶段中Vue正在编译模板页面
  - 当这个阶段完成后，我们在浏览器的内存中就已经有编译好的模板页面，但是这个编译好的模板结构，只能是在内存中，此时页面是空白的
- beforeMount(){创建阶段的第三个声明周期函数，即将挂载} 
  - 当模板页面被编译好之后，就会立即执行beforeMount这个函数，此时我们的html代码结构已经在内存中创建好了，但是尚未挂载到页面中


- 在这个函数中页面上的DOM元素是原始的差值表达式之类的代码

- mounted({创建阶段的第四个阶段函数，表示页面已经完成了渲染})

  - mounted当执行完函数后，页面就已经渲染完毕，此时Vue的实例创建阶段即将结束

  - 结论：

    如果引用第三方的插件操作dom元素，最好在mounted中操作dom元素，因为这时候才是真正的页面

### 2.vue脚手架安装less，sass

1.安装包

```
安装sass
cnpm install node-sass --save-dev
cnpm install sass-loader --save-dev
cnpm install style-loader --save-dev 
安装less
cnpm install less less-loader --save
```

2.打开webpack.base.config.js在loaders里面加上

```
rules: [
      {
        test: /\.vue$/,
        loader: 'vue-loader',
        options: vueLoaderConfig
      },
      {
        test: /\.js$/,
        loader: 'babel-loader',
        include: [resolve('src'), resolve('test')]
      },
      {
        test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
        loader: 'url-loader',
        options: {
          limit: 10000,
          name: utils.assetsPath('img/[name].[hash:7].[ext]')
        }
      },
      //配置
     {  
        test: /\.scss$/,
        loaders: ["style", "css", "sass"]
      },
      {  
        test: /\.less$/,
        loaders: ["style", "css", "less"]
      },
      {
        test: /\.(mp4|webm|ogg|mp3|wav|flac|aac)(\?.*)?$/,
        loader: 'url-loader',
        options: {
          limit: 10000,
          name: utils.assetsPath('media/[name].[hash:7].[ext]')
        }
      },
      {
        test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/,
        loader: 'url-loader',
        options: {
          limit: 10000,
          name: utils.assetsPath('fonts/[name].[hash:7].[ext]')
        }
      }     
    ]
```

3.如果需要在vue文件style标签使用scss的话，需要声明一下：

```
<style lang="scss" ></style>
```

### 3.Vuex

#### 1.为什么有Vuex

- 1.在vue中需要处理各种各样的数据，但是这些数据虽然多，但是从本质上分为两种数据
  - 组件内部私有数据（组件之间不会共享私有数据 ）
  - 组件之间共享的数据（父组件要共享子组件的数据，子组件要共享父组件的数据，兄弟组件之间传值）
- 2.当组件之间要共享数据的时候最好使用一个全局的数据存储对象来进行控制

#### 2.什么是Vuex

就是为了实现组件之间的数据共享的一种机制

#### 3.为什么有vuex

- 因为父子组件传值麻烦，不好管理

- 当有vuex想要共享数据，只需要把数据挂载到vuex上，想要数据直接从vuex身上去拿

- 当vuex中的数据修改后，其他引用次数据的组件也会被同步更新

  **注意：只有组件间共享数据才有必要存储到vuex中 ，组件自己的私有数据还是存储在自己的data中**

#### 4.使用vuex

- 新建一个文件store.js文件


- 下载

  ```
   cnpm i vuex -S 
  ```

- 项目中导入vuex

  ```
  import Vuex from 'vuex'
  ```

- 把vuex安装到vue上

  ```
  Vue.use(Vuex)
  ```

- 创建一个公共的状态对象

  ```
  //这个store中的state大家可以想象成组件对象中的data 属性
  const store = new Vuex.Store({

  //这个state中存储的是数据，就是我们要全局共享的状态（数据）
    state:{
      属性名称：属性值              //在全局就共享一个count值
      count：0
    },
    
    //1.作用专门用来修改state中的数据，如果修改state中的数据必须调用mutations中的方法
    //2.是同步的方法，但是虽然说vue官网不要在mutations中书写异步的方法，但是如果不按照官方的走，
    //在mutations中书写异步的方法，可以正常执行，但是vue-tool调试工具无法监听到数据的变化
    mutations:{  
    	//mutations中的方法第一个参数永远都是state
    	//可以传递一个参数,如果想传递多个参数可以是一个数组或者对象
      add(state,step){
        	state.属性名称
        	state.count++ 
      }
    },
    
    //getters类似于计算属性，同时也可以认为它是过滤器
    //不会修改原来的数据，只是会把原来的数据按照我们的要求重新组织和包装
    getters:{
      countinfo:function(state){
        	return '---'+state.count+'---'
      }
    },
    //1.异步执行的方法
    actions:{
    	//定义一个新的方法
    	//在actions中可以定义一些方法，来提交对应的mutations中的方法
    	//context是一个形式参数，任何一个都可以
      newadd(context){
      //在actions中不能直接操作state中的数据，如果想修改state的值只能通过在actions中提交对应的		//mutations
        context.commit('add')
      }
    }
    
  })
  ```

- 将创建的共享状态对象挂载到vue实例中，这样所有的组件就可以直接从store中获取全局的数据了

  ```
  new Vue({
  //当把store挂载到vm上之后，所有的组件中都可以直接使用this.$store来访问全局的数据了
    store
  })
  ```

  #### 1.在组件中访问store中的state上的属性

  #### 第一种方式

  ```
  this.$store.state.属性名称	
  ```

  #### 第二种方式

  - 在组件中按需导入mapState辅助函数

    ```
    import {mapState} from 'vuex';
    ```

  - 创建一个computed属性，通过mapState把需要的状态映射到组件的计算属性中

    ```
     export default{
            data(){
                return{

                }
            },
            methods:{
               
            },
            //创建一个computed属性，通过mapState把需要的状态映射到组件的计算属性中
            computed:mapState(['属性名','属性名'])
        }
    ```

  #### 第三种方式

  - ```
    <template>
      <div>
          <h3>增减{{count}}</h3>
          <input type="button" value="+1" @click="increment">
      </div>
    </template>
    <script>
    ```

    在组件中按需导入mapState辅助函数

    ```
    import {mapState} from 'vuex'
    ```

  - 创建一个computed属性，通过mapState结合...展开运算符把需要的状态映射到组件的计算属性中

    ```
    export default{
      data(){
        return{
          mag:0
        }
      },
       methods:{
       	increment(){
      		//如果在组件中要调用mutations中的方法,只能通过this.$store.commit('方法名') 调用
      		this.$store.commit('add')
       	}
       },
       
      computed:{
      	//自定义计算属性
        newMag:function(){
          return '---'+this.mag+'---'
        },
       //... 展开运算符
       //通过展开运算符，把state中的数据映射为计算属性，这样能够让自己的计算属性和store中的属性并存
        ...mapState(['属性名'])
      }
    }
    ```

    #### 2.修改store中的state值，调用mutations中的方法

    1.第一种方式

    ```
    this.$store.commit('方法名')
    ```

    2.第二种方式

    ```
    import {mapMutations } from 'vuex'

    //映射mutations中的方法到methods中

    <template>
    	//可以传递一个参数
    	<input type="button" value="+1" @click="方法名()">
    </template>

    <script>
        methods：{
        //将store中的方法映射到methods中，在methods中就有了映射的方法
          ...mapMutations(['方法名'])
        }
    </script>
    ```

    #### 3.定义和使用getters

    1.第一种方式

    ```
    this.$store.getters.countinfo
    ```

    2.第二种方式

    ```
    <template>
      <div>
        <h3>{{ countinfo }}</h3>
      </div>
    </template>

    import { mapState, mapGetters } from "vuex";

    export default {
      data() {
        return {};
      },
      methods: {},
      computed: {
        ...mapState(["count"]),
        ...mapGetters(["countinfo"])
      }
    };
    ```

    #### 4.使用actions

    ```
    import { mapState, mapActions } from "vuex";
    export default {
      data() {
        return {};
      },
      methods: {
        ...mapActions(['newadd'])
      },
      computed: {
        ...mapState(["count"])
      }
    };
    ```

### 4.http常用的请求方式

- get         查询
- post       添加
- put         更新
- delete    删除

### 5.url标准格式

```
schema:host:port/path?query#fragment

schema:协议 http/https/ftp/file   唯一确定互联网上的哪台电脑

host:域名或者ip地址

port：端口 用来唯一确定计算机上的一个网络应用程序

path:路径，端口之后，参数之前的部分

query:参数,查询字符串 key=value&key1=value1

fragment：hash 锚点，定位页面的某一位置

```

###6.计算属性

- 计算属性：一种带有行为的属性，本质是方法，但不能当做方法来使用，必须当做属性来使用
- 计算属性只能当做属性来使用，不能用于事件处理函数
- 优点：相比方法优势在于会缓存计算结果，效率很高

~~~
<div id="app">
        <input type="text" v-model="num1">
        <input type="button" value="+">
        <input type="text" v-model="num2">
        <input type="button" value="=">
        <!-- 当成属性进行使用 -->
        <input type="text" v-model="sum">

</div>
    <script src="../node_modules/vue/dist/vue.js"></script>
    <script>
        new Vue({
            el:"#app",
            data:{
                num1:0,
                num2:0
            },
            //计算属性
            //只会执行一次
            //计算属性：一种带有行为的属性，本质是方法，但不能当做方法来使用，必须当做属性来使用
            //计算属性只能当做属性来使用，不能用于事件处理函数
            //优点：相比方法优势在于会缓存计算结果，效率很高
            computed: {
                sum:{
                	//自动调用get方法
                	get(){
                      return parseFloat(this.num1)+parseFloat(this.num2)
                	},
                	//修改
                	set(){
                      
                	}
                }
            }
        })
    
    </script>
~~~

### 7.watch

~~~
 new Vue({
            el:"#app",
            data:{
                num1:0,
                num2:0
            },
            //普通监听
            watch:{
              监听数据:function(val,old){
                
              }，
              //深度监听
              监听数据:{
                handler:function(val,old){
                  
                },
                deep: true
              }
            }
 }）
~~~

### 8.路由守卫

- 这是全局的路由钩子函数,
- 所有的路由都会经过这里我们可以做一写特殊的处理
  在router>inedx.js文件中

~~~
import Vue from 'vue'
import Router from 'vue-router'
Vue.use(Router)
let router = new Router({
  routes: [
    {
      path: '/login',
      component: login
    }
})
//路由守卫
router.beforeEach((to, from, next) => {
//next()表示放行,如果不加next()代码就会停留在这里
  next()
})
export default router
~~~



## 2.es6

### 1.Promise

- 概念：

  #### promise是一种规范，该规范用来规定异步编程模式

- 方法：

  - 静态方法：

    ​all

    ​race

  - 实例方法：

    ​then

    ​catch

- 语法：

  ```
  var p = new Promise(function(resolve,reject){
    setIimeout(function(){
      var flag = true;
      if(flag){
        resolve('处理成功数据')
      }else{
        reject('处理失败数据')
      }
    })
  })
  //处理成功业务
  p.then(function(data){
    console.log(data)
  })
  //处理失败业务
  .catch(function(){
    console.log(data)
  })
  ```

- 链式编程

```
	 function fn1(){
            var p = new Promise(function(resolve){
                setTimeout(function(){
                    console.log(1000)
                    resolve('你好1000')
                },1000)
            })
            return p;
        }
        function fn2(){
            var p = new Promise(function(resolve){
                setTimeout(function(){
                    console.log(2000)
                    resolve('你好2000')
                },2000)
            })
            return p;
        }
        function fn3(){
            var p = new Promise(function(resolve){
                setTimeout(function(){
                    console.log(3000)
                    resolve('你好3000')
                },3000)
            })
            return p;
        }
        
        //then默认返回的是promise对象，如果显示的是返回的promise对象,那么下一个then中的data就是				resolve的结果；
        //如果显示的是返回实际的数据，那么下一个then中的data就是该数据
        
        fn1().then(function(data){
            console.log(data)
            return fn2()
        }).then(function(data){
            console.log(data) //返回的是fn2()中的值
            // return fn3()
            var flag = {name:"小明"};
            return flag;
        }).then(function(data){
            console.log(data.name) //小明
        })
    

```

**Promise编程模式：每一个异步任务封装一个独立的方法，然后通过then的方式串联起来，保证异步任务的串行（依次执行）**

### 2.es6导入和导出

- export导出(可以出现多次)

  ```
  //文件名：demo.js
  export const add = (a,b)=>{
      return a+b;
  }
  export const reduce = (a,b)=>{
      return a-b;
  }
  export function add(a,b){
      return a+b;
  }

  ```

- import from导入

  - 采用{方法名}的方式

  ```
  //导入add
  import { add ,reduce } from './demo.js'
  //调用
  add(1,2)；
  reduce(1,2)

  ```

- export default导出一个函数（只能出现一次）

  ```
  //文件名demo.js
  export default function add(a,b){
      return a+b
  }

  ```

- import from导入

  ```
  import add  from './demo.js'
  //调用
  add(1,2)

  ```

- export default导出多个函数

  ```
  export default{
      add(a,b){
          return a+b
      },
      reduce(a,b){
          return a-b
      }
  }

  ```

- import from导入

  ```
  import obj from './demo'
  //调用
  //返回的函数是obj的方法
  obj.add(1,2);
  obj.reduce(1,2)

  ```

### 3.new Set()进行数据去重

```
通过new Set()进行数据去重，参数为数组，返回的是new Set的实例对象
let array = [1,2,3,2,1];

let arr = new Set(array）;

[...arr]  通过'延展运算符'转成数组

```

###4.map方法

```
    //map循环
    var arr = [1,2,3,4,5];
      arr = arr.map(function(item,index,arr){
        return item*2
      })
      console.log(arr)
      
     //forEach
     var newArr = [];
      arr.forEach(function(item,index,arr){
        newArr.push(item*2)
      })
      console.log(newArr)
```

- map和forEach的区别
  1、map速度比foreach快
  2、map会返回一个新数组，不对原数组产生影响,foreach不会产生新数组，
  3、forEach:用来遍历数组中的每一项；这个方法执行是没有返回值的，对原来数组也没有影响；
  4、map因为返回数组所以可以链式操作，foreach不能
  5, map里可以用return ,而foreach里用return不起作用，foreach不能用break，会直接报错

### 5.async函数

~~~
//1.
function  fn(callback){
    setTimeout(function(){
      let num = 123
        callback(num)
    },10)
  }
  fn(function(num){
    console.log(num)
  })
  
  //2
 function fn(){
    return new Promise(function(resolve){
      setTimeout(function(){
        var num = 123;
        resolve(num)
      })
    })
  }
  // .then是将new Promise对象返回
  fn().then(res=>{
    console.log(res)
  })
  
  
  //3.
   function fn(){
    return new Promise(function(resolve){
      setTimeout(function(){
        var num = 123;
        resolve(num)
      })
    })
  }
  //async  书写在await包裹的方法上
  //await
  async function fn(){
     let num = await fn()
     console.log(num)
  }
  
 //发起请求
  methods:{
      async get(){
        //data为返回的数据
        //await等待请求数据完成才会执行
        const {data} await  axios.get('路径')
        this.list = data.list
      }
}
~~~

###6.every方法

- 只有数组中的每一项全部满足条件才会返回true
- 其中有一项不满足返回false

例：

    1.当其中有一项不满足返回false
    let arr = [1,2,3,4];
    arr.every((item)=>{
      return item%2==0
    })
    arr.every(item=>
      item%2==0
    )
    返回false
    
    2.只有全部满足条件返回true
    let arr = [2,4,6,8];
    arr.every(item=>{
      return item%2==0
    })
    arr.every(item=>
      item%2==0
    )
    返回true

### 7.some方法

- 只要其中有一项满足就返回true

- 每一项都不满足才会返回false

    ~~~
    //1.只要其中有意向满足就返回true
    let arr = [2,5,7]
    let a = arr.some(item=>
    	item%2==0
    )

    返回true

    //2.每一项都不满足才会返回false

    let arr = [3,5,7]

    let a = arr.some(item=>

    	item%2==0

    )

    返回false

    ~~~

    ​

计算属性

## 3.项目

### 1.moment时间过滤器

```
安装moment插件
npm install moment -S
// 导入格式化时间的插件
import moment from 'moment'
// 定义全局的过滤器
//第一个是过滤器的名称
//第二个参数是格式化的数据
//第三个是格式
Vue.filter('dateFormat', function (dataStr, pattern = "YYYY-MM-DD HH:mm:ss") {
  return moment(dataStr).format(pattern)
})
//第一个参数是时间
//通过管道符间隔 | 
//管道符后面是时间过滤器的名称
 <span>发表时间：{{ item.add_time | dateFormat }}</span>
 //年月日
 {{info.add_time | dateFormat("YYYY-MM-DD")}}
```

### 2.路由页面传参

1.环境：

​	通过新闻列表页跳转到对应的详情页面

​	思路:

​		通过新闻列表传递id值
 		详情页面接收渲染相对应的页面

（1）使用元素导航：使用 router-link 元素实现的导航

```
1.新闻列表模板：
<template>
	<li v-for="item in newlist">
	//改造路由
	// 1.通过   :to="'/news/newinfo/'+item.id" 进行传递参数，将每一项的id传入
      <router-link :to="'/news/newinfo/'+item.id">

       </router-link>
 	</li>  
 </template>
 
  2.配置路由规则
 
 new Router({
  routes: [
      {
        //新闻详情，需要添加参数:id值
        path:"/news/newinfo/:id",
        component: newinfo
      }
   ]
}) 
3.新闻详情页接收传递参数，渲染数据
<script>
  export default{
      data(){
          return{
              //初始化
              id:0
          }
      },
      created () {
          //通过this.$route.params.id接收传过来的值
          //id为自定义的路由的值
          this.id = this.$route.params.id
      }
  }

</script>
```

（2）使用编程式导航：就是使用JS的代码的方式，来实现路由的跳转

```
 <template>
   <div>
   		//按钮定义一个函数fn
  		<mt-button type="primary" size="large" plain @click="fn">图文介绍</mt-button>
  	<div id="tmp">
  </template>
  <script>
      export default{
       data(){
            return{
                goodinfo:{} //数据存放对象
        }
       },
       methods:{
           fn(){
          		// 点击按钮，跳转到 商品的描述页面
      			// 注意： 在 this 这个组件身上，有 this.$route 和 this.$router
      			// this.$route 是专门用来获取路由中参数的；
      			// this.$router 是专门来实现编程式导航的；
      			//跳转到/good/gooddesc/并将参数带过去
               this.$router.push('/good/gooddesc/'+this.goodinfo.id)
           }
       }
    }
</script>

 2.配置路由规则
 
 引入组件
 import gooddesc from '@/components/good/gooddesc'

 new Router({
  routes: [
      {
      path:"/good/gooddesc/:id",
      component:gooddesc
    }
   ]
}) 


跳转到页面进行接收
<template>
    <div id="tmp">
        {{id}}
    </div>
</template>
<script>
    export default{
        data(){
            return{
                id:0
            }
        },
        created () {
        	//进行接收
            this.id = this.$route.params.id
        }
    }
</script>
<style scoped>

</style>
  
```



### 3.父组件引入子组件以及父子组件传值

1.父组件

```
<template>
      <div id="tmp">
      	//3. 将子组件的名称写到父组件中
      	//4. :id  是定义的自定义属性
      	//   ：id='id' 后面的id为父传入子组件的值
          <comment :id='id' @fn='get'></comment>
      </div>
</template>
	1.引入子组件
	import comment from '../subcomponents/comment.vue'
	export default {
      //2.定义私有组件
      components:{
      //组件的名称
        comment:comment
	}
      data(){
        return {
          id:0
        }
      },
     methods:{
     //子组件传入父的值
       get(data){
         console.log(data)
       }
     }
	}
	
```

子组件

```
<template>
  <div id="tmp">
  	<p @click="set"></p>
  //在页面中可以获取id
    {{id}}
  </div>
</template>
<script>
    export default{
    //子组件通过props：['id']获取父组件传过来的值
    //'id' 为在父组件身上定义的自定义属性
        props:["id"],
        data(){
          return{
          //也可拿到id值
            this.id,
             list:[
                   	{id:1,name:"小明"}
                  ]
          }
        },
         methods:{
         	set(){
         	//fn为自定义属性
         	//this.list为传入父组件的值
         		this.$emit('fn',this.list)
         	}
         }
</script>
```

### 4.兄弟之间传值

```
 <div id="app">
 		//组件
        <bar></bar>
        <mag></mag>
    </div>
    <template id="bar">
        <div>
            <h3>bar组件</h3>
            {{num}}
        </div>
    </template>
    <template id="mag">
        <div>
            <h3>mag组件</h3>
            <input type="button" value="改变兄弟组件的num值" @click="add">
        </div>
    </template>
```

```
 <script src="../lib/vue-2.5.9.js"></script>
    <script>
        //1.定义一个空组件
        let bus = new Vue()
        new Vue({
            el:"#app",
            components:{
                bar:{
                    template:"#bar",
                    data(){
                        return{
                            num:0
                        }
                    },
                    created () {
                        //2.在a组件中订阅事件
                        bus.$on('increment',()=>{
                           // console.log(this.num)
                            this.num++
                        })
                    }
                },
                mag:{
                    template:"#mag",
                    methods:{
                        add(){
                            //3.在b组件中发布
                            bus.$emit('increment')
                        }
                    }
                }
            }
        })
    
    </script>
```

### 5.返回按钮

需求：首页没有返回按钮

​	任何一个子页面都有返回按钮

App.vue

```
 <template>
   <!-- mint-ui的头部 -->
     <mt-header fixed title="黑马程序员·Vue项目">
      <span slot="left" @click="goBack" v-show="isflag">
        <mt-button icon="back">返回</mt-button>
      </span>
    </mt-header>
</template>

<script>
export default {
    data(){
        return{
        	//定义变量值
            isflag:false
        }
    },
    created () {
    	//通过this.$route.path获取路由值
        const path = this.$route.path
        //如果path值为'/home'主页，将 this.isflag变成false值
		if (path === '/home') {
                this.isflag = false
            }else {
                this.isflag = true
        }
	},
	methods:{
      goBack() {
      	// 点击返回按钮，向后跳转
      	this.$router.go(-1);
      	console.log(this);
      }
    },
	 //监听路由变化
  watch: {
        '$route': function (newVal, oldVal) {
          if (newVal.path === '/home') {
              this.isflag = false
          }else{
              this.isflag = true
          }
        }
    }
}
</script>
```

### 6.路由重定向

```
export default new Router({
  routes: [
  //重定向
  //如果路由为/,定向为'/home'
    {
      path:"/",
      redirect:'/home'
    },
    {
      path:"/home",
      component:home
    }
  ]
})
```

### 7.点击当前样式选中

- 通过设定一个flag标志，用来记录当前是第几个
- act为当前样式

```
 <template>
     <li v-for='(item,index) in navlist' :key='item.id' @click="navbtn(item.id)" :class="			{'act':item.id==flag}">
      {{item.categoryName}}
    </li>
 </template>
 export default{
     data(){
         return{
            flag:0 
         }
     },
     methods:{
         navbtn(id){
             this.flag = id
         }
     }
 }
 

```

### 8.返回上一级

~~~
 this.$router.go(-1);
~~~

### 9.跳转回之前页面

- 业务逻辑：
  - 添加购物车，判断登录，没有登录跳转到登录页面
  - 登录页面，登录成功，跳转回添加购物车页面
- 购物车页面，跳转到登录页面

```
 this.$router.replace({
 	name:'login',
 	query: {redirect: this.$router.currentRoute.fullPath}
 })

```

- 登录成功，跳转回购物车页面

```
this.$router.push(this.$route.query.redirect || '/')

```

### 10.进度条效果

- 下载

  ~~~
  $ npm install --save nprogress
  ~~~

- main.js文件引入

  ~~~
  //引入nprogress进度条的效果的css文件
  import 'nprogress/nprogress.css'
  ~~~

- 因为每一个路由切换都需要添加进度条效果，所以听过路由守卫在router>index.js文件中使用

  ~~~
  import Vue from 'vue'
  import Router from 'vue-router'
  //nrogress的js文件
  import nrogress from 'nprogress/nprogress.js'
  Vue.use(Router)
  let router = new Router({
    routes: [
      {
        path: '/login',
        component: login
      }
  })
  //路由守卫
  //这是全局的路由钩子函数
  //所有的路由都会经过这里我们可以在这里做一些特殊的处理
  //next()表示放行,如果不加next()代码就会停留在这里
  router.beforeEach((to, from, next) => {
    //因为我们么每一个路由都需要添加进度条,所以讲进度条放到这里
    //进度条的开始
    nrogress.start();
    next()
    //进度条结束
    nrogress.done();

  })
  export default router
  ~~~

### 11.tab切换

#### 	1.导航固定，详情由接口获取

~~~
<template >
    <div class="tmp">
        <ul>
            <li @click="getpic(flag=0)" :class="{'act':flag==0}">24热门</li>
            <li @click='getpic(flag=1)' :class="{'act':flag==1}">七日热门</li>
            <li @click="getpic(flag=2)" :class="{'act':flag==2}">三十日热门</li>
        </ul>
    </div>
</template>
<script>
    export default{
        data(){
            return{
                flag:0
            }
        },
        created(){
            this.getpic(this.flag)
        },
        methods:{
            getpic(){
                this.$http.get('/api/getimages/'+this.flag)
                .then(res=>{
                    console.log(res)
                })
            }
        }
    }
</script>
<style scoped>
.tmp{
    padding:40px 0 55px 0;
}
ul {
    overflow: hidden;
    border-bottom:1px solid #ccc;
}
ul li{
    float: left;
    width: 33.33%;
    line-height: 30px;
    font-size: 14px;
    text-align: center;
}
.act{
    border-bottom:1px solid #f40;
}
</style>
~~~

####2.导航固定，内容固定

~~~
<template >
    <div class="tmp">
        <ul>
            <li @click="flag=0" :class="{'act':flag==0}">24热门</li>
            <li @click='flag=1' :class="{'act':flag==1}">七日热门</li>
            <li @click="flag=2" :class="{'act':flag==2}">三十日热门</li>
        </ul>
        <div class="content">
            <div v-show='flag==0'>24热门24热门24热门24热门24热门24热门24热门24热门</div>
            <div v-show='flag==1'>七日热门七日热门七日热门七日热门七日热门七日热门</div>
            <div v-show='flag==2'>三十日热门三十日热门三十日热门三十日热门三十日热门</div>
        </div>
    </div>
</template>
<script>
    export default{
        data(){
            return{
                flag:0
            }
        },
        created(){
            
        },
        methods:{
           
        }
    }
</script>
<style scoped>
.tmp{
    padding:40px 0 55px 0;
}
ul {
    overflow: hidden;
    border-bottom:1px solid #ccc;
}
ul li{
    float: left;
    width: 33.33%;
    line-height: 30px;
    font-size: 14px;
    text-align: center;
}
.act{
    border-bottom:1px solid #f40;
}
.content div{
    width: 100%;
    height: 200px;
    background: lightblue;
}
</style>


~~~

#### 3.导航和内容接口获取

~~~
<template>
    <div class="tmp">
        <div class="left_nav">
            <ul>
                <li v-for='(item,index) in navlist' :key='item.id' @click="navbtn(item.id)" :class="{'act':item.id==flag}">
                    {{item.categoryName}}
                </li>
            </ul>
        </div>
        <div class="right_pic">
            <ul>
                <li v-for='item in category' :key='item.id'>
                    <img :src="baseURL+item.brandLogo" alt="">
                    <p>{{item.brandName}}</p>
                </li>
            </ul>
        </div>
    </div>
</template>
<script>
export default{
    data(){
        return{
            navlist:[],
            category:[],
            flag:1,
            baseURL:'http://127.0.0.1:3000'
        }
    },
    created(){
        this.getLeft()
        this.navbtn(1)
    },
    methods:{
    	//获取导航
        getLeft(){
            this.$http.get('/category/queryTopCategory')
            .then(res=>{
                console.log(res)
                if(res.status==200){
                    this.navlist = res.data.rows
                }  
            })
        },
        //根据id获取内容
        navbtn(id){
            this.flag = id
            console.log(id)
            this.$http.get('/category/querySecondCategory',{params:{id:id}})
            .then(res=>{
                console.log(res)
                if(res.status==200){
                    this.category = res.data.rows
                }
            })
        }
    }
}
</script>
<style scoped>
.tmp{
    padding:40px 0 60px 0;
}
.left_nav{
    width: 90px;
    overflow: hidden;
    float: left;
}
.left_nav li{
    text-align: center;
    line-height: 60px;
    border-right:1px solid #ccc;
    border-bottom:1px solid #ccc;
}
.left_nav li.act{
    border-right: none;
}
.right_pic{
    padding:10px;
    overflow: hidden;
}
.right_pic li{
    width: 33.333%;
    float: left;
    text-align: center;
}
.right_pic li img{
    width: 100%;
    height: 100%;
}
</style>

~~~

### 12.vue-Preview（缩略图）

- 下载

  ~~~
  npm i vue-preview -S
  ~~~

- 导入（在main.js中）

  ~~~
  import VuePreview from 'vue-preview'

  Vue.use(VuePreview)

  import './assets/css/base.css'
  ~~~

- 在组件中使用

  ~~~
  <template>
      <div class="thumbs">
      	<vue-preview :slides="curimg" @close="handleClose"></vue-preview>
       </div>
   </template>
   
   <script>
      export default{
          data(){
              return{
                  id:0,
                  curimg:[]
              }
          },
          created(){
              this.id = this.$route.params.id
              this.getcurimg()
          },
          methods:{
              async getcurimg(){
                  let data = await this.$http.get('/api/getthumimages/'+this.id)
                  console.log(data)
                  if(data.status==200){
                      data.data.message.forEach(item=>{
                          item.w = 600
                          item.h = 600
                          item.msrc = item.src
                      })
                      this.curimg = data.data.message
                  }
              },
              handleClose(){

              }
          }
      }

  </script>
   
  ~~~

- 在全局css中配置样式（base.css）

  ~~~
  .thumbs{
  	display: flex
  }
  .thumbs>div{
  	width:100%;
  }
  .thumbs .my-gallery{
  	display: flex;
  	flex-wrap: wrap
  }
  .thumbs .my-gallery figure{
  	margin:10px;
  	box-shadow: 0 0 10px #ccc;
  }
  .thumbs .my-gallery figure img{
  	width: 100px;
  	vertical-align: middle;
  }

  ~~~


### 13 图片懒加载（vue-lazyload）

​      https://github.com/hilongjw/vue-lazyload

- 下载

  ~~~
  npm install vue-lazyload -D 
  ~~~

-  在main.js中

  ~~~
  import Vue from 'vue'
  import App from './App.vue'
  import VueLazyload from 'vue-lazyload'
  Vue.use(VueLazyload, {
    preLoad: 1.3,
    error: 'dist/error.png',
    loading: 'dist/loading.gif',
    attempt: 1
  })
  ~~~

- vue组件中

  ~~~
  <ul>
    <li v-for="img in list">
      <img v-lazy="img.src" >
    </li>
  </ul>
  ~~~

###14.排列字母

~~~
 var arr = ['A','C','E','B','D','F'];
 arr.sort((a,b)=>{
 return a.charCodeAt(0)-b.charCodeAt(0)
 })
 console.log(arr)
~~~

### 15.直接在{{}}调用方法

~~~
<template>
    <div class="song-list">
        <ul>
            <li v-for='(item,index) in songs' :key='index'>
                <h3>{{item.name}}</h3>
                //直接在表达式中使用
                <p>{{getinit(item)}}</p>
            </li>
        </ul>
    </div>
</template>
<script>
    export default{
        methods:{
        	//定义的方法
            getinit(item){
            	//返回处理好的数据
               return item.singer+'·'+item.album
            }
        }
       
    }
</script>
<style scoped>

</style>


~~~

​		


