# vue-eventBus

## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn run serve
```

### Compiles and minifies for production
```
yarn run build
```

### Run your tests
```
yarn run test
```

### Lints and fixes files
```
yarn run lint
```


**(1)通常父子组件的通信使用的是 :**
>     父组件传值给子组件 ： props,或在父组件上调用子组件内的方法
>     子组件传值给父组件 ：$emit $on  ($emit(eventName)   是发射,触发事件的意思，$on(eventName)为监听事件，当然子组件广播出去，父组件听不听就是父组件的事了)，或 在父组件上获取子组件的内的数据 


**(2)通常非父子组件通信使用的是:**
-     Vuex 或者是 eventbus  

### 1-1 props

父组件给子组件传子，使用props（[官网](https://cn.vuejs.org/v2/guide/components.html#%E9%80%9A%E8%BF%87-Prop-%E5%90%91%E5%AD%90%E7%BB%84%E4%BB%B6%E4%BC%A0%E9%80%92%E6%95%B0%E6%8D%AE)）


```
//父组件：parent.vue
<template>
    <div>
        <child :vals = "msg"></child>
    </div>
</template>
<script>
import child from "./child";
export default {
    data(){
        return {
            msg:"我是父组件的数据，将传给子组件"
        }
    },
    components:{
        child
    }
}
</script>


//子组件：child.vue
<template>
    <div>
        {{vals}}
    </div>
</template>
<script>
export default {
      props:{ //父组件传值 可以是一个数组，对象
        vals:{
            type:String,//类型为字符窜
          default:"123" //可以设置默认值
        }
    },
}
</script>
```
## 1-1-1 在父组件上调用子组件内的方法

使用ref 具体请查看官网文档--[子组件索引](https://cn.vuejs.org/v2/guide/components.html#%E5%AD%90%E7%BB%84%E4%BB%B6%E7%B4%A2%E5%BC%95%EF%BC%89)

```

<!--父组件 template-->
<div id="parent">
  <!--子组件-->
  <user-profile ref="profile"></user-profile>
</div>

// 父组件 script
this.$refs.profile.someMethod();


```
**注意**：如果在子组件上设置ref属性，则可以通过this.$refs获取该子组件对象，不过你得考虑一下组件的耦合度的问题，如果耦合度严重，对其他组件数据也会产生很大的影响（注：组件耦合严重，会增加维护成本，若是公共组件，你用了ref，维护组件的人想改方法名字，或者删掉方法的时候，怎么办呢？你调用的时候直接就报错了吧？）这个问题可以说明尽量少使用在子组件使用ref，如果在普通的html标签上设置ref属性 则获取的是Dom节点,即它主要的作用就当doucument.getElementById()方法来使用。

## 1-2 $emit $on  子组件传值给父组件

这里有一个问题，
> - 1、究竟是由子组件内部主动传数据给父组件，由父组件监听接收（由子组件中操作决定什么时候传值）
> - 2、还是通过父组件决定子组件什么时候传值给父组件，然后再监听接收 （由父组件中操作决定什么时候传值）
> - 两种情况都有
> - 2.1 : $meit事件触发,通过子组件内部的事件触发自定义事件$emit
> - 2.2 : $meit事件触发， 可以通过父组件操作子组件 (ref)的事件来触发 自定义事件$emit
> 第一种情况：


```
//父组件：parent.vue
<template>
    <div>
        <child  v-on:childevent='wathChildEvent'></child>
        <div>子组件的数据为：{{msg}}</div>
    </div>
</template>
<script>
import child from "./child";
export default {
    data(){
        return{
            msg:""
        }
    },
    components:{
        child
    },
    methods:{
        wathChildEvent:function(vals){//直接监听 又子组件触发的事件，参数为子组件的传来的数据
            console.log(vals);//结果：这是子组件的数据，将有子组件操作触发传给父组件
            this.msg = vlas;
        } 
    }
}
</script>

//子组件：child.vue
<template>
    <div>
       <input type="button" value="子组件触发" @click="target">
    </div>
</template>
<script>
export default {
    data(){
            return {
            texts:'这是子组件的数据，将有子组件操作触发传给父组件'
            }
    },
    methods:{
        target:function(){ //有子组件的事件触发 自定义事件childevent
            this.$emit('childevent',this.texts);//触发一个在子组件中声明的事件 childEvnet
        }
    },
}
</script>
```


第二种情况：

```
/父组件：parent.vue
<template>
    <div>
        <child  v-on:childevent='wathChildEvent' ref="childcomp"></child>
        <input type="button" @click="parentEnvet" value="父组件触发" />
        <div>子组件的数据为：{{msg}}</div>
    </div>
</template>
<script>
import child from "./child";
export default {
    data(){
        return{
            msg:""
        }
    },
    components:{
        child
    },
    methods:{
        wathChildEvent:function(vals){//直接监听 又子组件触发的事件，参数为子组件的传来的数据
            console.log(vals);//这是子组件的数据，将有子组件操作触发传给父组件
            this.msg = vlas;
        },
        parentEnvet:function(){
            this.$refs['childcomp'].target(); //通过refs属性获取子组件实例，又父组件操作子组件的方法触发事件$meit
        }
    }
}
</script>

//子组件：child.vue
<template>
    <div>
      <!-- dothing..... -->
    </div>
</template>
<script>
export default {
    data(){
            return {
            texts:'这是子组件的数据，将有子组件操作触发传给父组件'
            }
    },
    methods:{
        target:function(){ //又子组件的事件触发 自定义事件childevent
            this.$emit('childevent',this.texts);//触发一个在子组件中声明的事件 childEvnet
        }
    },
}
</script>
```


将两者情况对比，区别就在于$emit 自定义事件的触发是有父组件还是子组件去触发

第一种，是在子组件中定义一个click点击事件来触发自定义事件$emit,然后在父组件监听

第二种，是在父组件中第一一个click点击事件，在组件中通过refs获取实例方法来直接触发事件,然后在父组件中监听

## 1-2-1 在父组件上获取子组件内的数据


```
同上，也是利用 ref

// 父组件 script
let childData = this.$refs.profile.someData;

```


## (2)eventbus用于兄弟组件通信
注意：注意兄弟组件相互路由跳转的时候 使用用eventbus 无效 这是因为路由跳转的之后，之前的组件的生命周期就已经注销了（待解决）？



下面有first ,second兄弟组件如下代码 ：

1-0  添加全局eventbus 插件（老王的插件中使用 普通不需要使用这步）

```
import Vue from 'vue'
import App from './App'
import router from './router'

//import EventProxy from 'vue-event-proxy'
import {eventBus} from './until/eventBus'

Vue.config.productionTip = false

 Vue.prototype.bus = eventBus
//Vue.use(EventProxy)


/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})
```



1-1 创建eventbus.js文件

```
import Vue from 'vue'
const eventBus = new Vue()
export { eventBus }

```
这个案例中没设置全局所以组件都都要添加

1-2 app.vue


```
template>
 <div class="app">
    <!-- <router-link to="/first">first</router-link>
    <router-link to="/second">second</router-link>
    <router-view></router-view> -->
<First></First>
<Second></Second>
  </div>
</template>
```

1-3 first组件 


```
emplate>
  <div>  <button @click="method1">触发</button>
  <h2>{{num}}</h2>
  </div>
</template>

<script>
import {eventBus} from '../until/eventBus.js'
export default {
  data () {
    return {
      num: ''
    }
  },
  methods: {
    method1: function () {
      eventBus.$on('eventBusName', (val) => {
        console.log('dd' + val)
        this.num = val
      })
    //   this.$on('global:EVENT_NAME', (val) => {
    //     console.log('dd' + val)
    //     this.num = val
    //   })
    //   console.log('dhdhh')
    // }
  }
}
</script>
<style lang='scss' scoped>
</style>

```

1-4 second.vue组件


```
<template>
   <div>
     <h1>{{ num }}</h1>
    <button @click="handleAdd">随机增加</button>
    </div>
</template>

<script>
import {eventBus} from '../until/eventBus.js'
export default {
  data () {
    return {
      num: 0
    }
  },

  methods: {
    handleAdd () {
      this.num = Math.floor(Math.random() * 100 + 1)
      // this.$emit('global:EVENT_NAME', this.num)
      eventBus.$emit('eventBusName', this.num)
    }
  }
}

</script>
<style lang='scss' scoped>
</style>

```
注：eventbus插件是在注释中的


##### 更新18-10-13：
###### 解决方法：在上面的如果是两个兄弟组件下面的案例中 之间传值 但由于组件之间路由跳转，组件销毁 使之间的值传不出来  现在开始解决。为啥会出现上面的问题** 是因为vue-router 在切换的时候 ，先假装新的组件，等新组件渲染好了但是还未挂载前，销毁旧的组件，然后再挂载新的组件。 **

在路由切换时 执行的方法依次是：


```
新组件： beforeCreate
新组件： created
新组件： beforeMount
旧组件： beforeDestrory
旧组件： destroy
新组件： mounted
```

贴代码：
- 创建一个eventBus单独文件
 eventBus.js

```
import Vue from 'vue'
/**
 * 创建一个新 eventbus 就是创建一个新的vue() 实例
 * */
const eventBus = new Vue()
export { eventBus }

```
- 全局注册
main.js

```
import { eventBus } from './until/eventBus'

Vue.prototype.bus = eventBus
```



- 准备两个组件（通过路由跳转的组件）

first.js

```
<template>
  <div class="first">
   <button @click="handleAdd">随机增加</button>
   <h1>{{ num }}</h1>
  </div>
</template>

<script>
// @ is an alias to /src
import { eventBus } from '../until/eventBus.js'
export default {

  name: 'home',
  components: {
    
  },
  data () {
    return {
      num: 0
    }
  },
  methods: {
    handleAdd: function () {
      this.num = Math.floor(Math.random() * 100 + 1)
    }
  },
  destroyed () {
    eventBus.$emit('eventBusName', this.num)
  }

}
</script>

```
second.js


```
<template>
  <div class="second">
    <h1>This is an second page</h1>
  <h2>{{num}}</h2>
  </div>
</template>
<script>
import { eventBus } from '../until/eventBus.js'
export default {

  data () {
    return {
      num: 0
    }
  },
  created () {
    eventBus.$on('eventBusName', (val) => {
      console.log(`about ${val}`)
      this.num = val
      console.log(this.num)
    })
  }
}

</script>

```















---


##### (3)简单分析一下[eventbus插件源码 ](https://github.com/jser-club/vue-event-proxy/blob/master/src/index.js)

```
function plugin(Vue) {
  //获取Vue的版本号并存入变量
  const version = Number(Vue.version.split('.')[0]);
  //定义一个入参函数
  const NOOP = () => {};
  //判断版本号是否小于2版本
  if (version < 2) {
    console.error('[vue-event-proxy] only support Vue 2.0+');
    return;
  }

  // Exit if the plugin has already been installed.
  if (plugin.installed) {
    return;
  }

  const eventMap = {};
  const vmEventMap = {};
  const globalRE = /^global:/

  //创建一个event混入函数
  function mixinEvents(Vue) {
   //将Vue原型上的on事件存在一个定值上
    const on = Vue.prototype.$on;
    //原型on事件重写一个代理函数 因为每个组件（也就是即vm实例）有自己的_uid作为唯一标识
    Vue.prototype.$on = function proxyOn(eventName, fn = NOOP) {
      const vm = this;
      //判断传入的参数是否是一个Array
      if (Array.isArray(eventName)) {
       //对输入的数组即事件执行一次监听事件
        eventName.forEach((item) => {
          vm.$on(item, fn)
        });
      } else {
        //判读这个参数是否有global 
        if (globalRE.test(eventName)) {
            //将组件的唯一标识存放到对象中
          (vmEventMap[vm._uid] || (vmEventMap[vm._uid] = [])).push(eventName);
          (eventMap[eventName] || (eventMap[eventName] = [])).push(vm);
        }
        on.call(vm, eventName, fn);
      }
      return vm;
    };


    //将Vue原型的发射事件存到一个定值上
    const emit = Vue.prototype.$emit;
    //原型emit事件重写一个代理发射事件 将输入的不定数量的参数表示一个数组
    Vue.prototype.$emit = function proxyEmit(eventName, ...args) {
      const vm = this;
      //判断事先有没有要发射方事件，开启全局事件的状态 没有将要发射的事件存到对象中 并将要发射的参数以数组的形式发射出去，发射完成并关闭全局事件的抓太
      if (!vm._fromGlobalEvent && globalRE.test(eventName)) {
        const vmList = eventMap[eventName] || [];
        vmList.forEach((item) => {
          item._fromGlobalEvent = true;
          item.$emit(eventName, ...args);
          item._fromGlobalEvent = false;
        });
      } else {
        emit.apply(vm, [eventName, ...args]);
      }
      return vm;
    }
  }
  //应用混入
  function applyMixin(Vue) {
  //使用vue内部的全局混入事件
    Vue.mixin({
    //在在注册一个混入事件 先关闭全局事件状态
      beforeCreate() {
        // Fix for warnNonPresent
        this._fromGlobalEvent = false;
      },
      //销毁混入事件
      beforeDestroy() {
        const vm = this;
        const events = vmEventMap[vm._uid] || [];
        events.forEach((event) => {
          const targetIdx = eventMap[event].findIndex(item => item._uid === vm._uid);
          eventMap[event].splice(targetIdx, 1);
        });
        delete vmEventMap[vm._uid];
      },
    });
  }

  mixinEvents(Vue);
  applyMixin(Vue);
}

export default plugin;
```

参阅点[Array.isArray](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/isArray)：确定传递的值是否是一个 Array。
查阅点[...args](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Rest_parameters) ： 将输入的不定数量的参数表示一个数组
查阅点 [Vue.mixin](https://cn.vuejs.org/v2/api/#Vue-mixin):
全局注册一个混入，影响注册之后所有创建的每个 Vue 实例。




事件重复触发以及未销毁eventbus 造成占用大量内存的问题

- 一

正当你开心的准备玩耍的时候却发现好像有哪里不对劲，怎么事件会重复触发了，而且每次切换过路由后，事件执行次数就会加一，这怎么行，假如用户非常频繁的切换页面，那事件执行次数不是会越来越多，到最后不是要爆炸。

一番搜索后终于找到了原因，原来这是因为我们的事件是全局的，它并不会随着组件的销毁而自动注销，需要我们手动调用注销方法来注销。知道了问题原因就好办了，我们可以在组件的 beforeDestroy ,或 destroy 生命周期中执行注销方法，手动注销事件。

       
```
beforeDestroy() {
            //组件销毁前需要解绑事件。否则会出现重复触发事件的问题
            this.bus.$off(this.$route.path);
        },
```


这样就完成了事件的注销操作，可以注销掉当前事件。

- 二

你以为这样就可以好好玩耍了吗，NO，NO，NO。 
虽然我们在生命周期中注销了事件，然而还是发现事件会多次执行，问题依旧在，那是什么原因呢？ 
经过打印日志后发现，问题出在事件名上面，由于我是用的 this.route.path作为事件名，在注销的时候也是想当然的用this.route.path作为事件名，在注销的时候也是想当然的用this.toure.path 作为注销事件名。观察日志后发现，在 beforeDestroy 中， this.$route.path 根本就不是我们发送和响应事件时候的路由了，而是将要跳转页面的路由。

这其实就是生命周期的问题了，在 beforeDestroy 和 destroy 生命周期中，用 this.$route.path 获取到的其实是下一个页面的 path ，注意这一点，问题即可解决。解决方案也很简单，就是在当前页面用一个变量将当前路由存下来，用这个变量作为事件名注销事件即可。



注意销毁：


```
//created,或者unted中创建监听
 
created: function () {
 
    	eventBus.$on("node",this.busHandle)
 
},
 
//beforeDestroy中销毁监听
 
beforeDestroy: function () {
 
    eventBus.$off('node', this.busHandle)
 
}

```