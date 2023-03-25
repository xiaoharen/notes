# 模板

```vue
<template>
  <div class="contain">
    <div class="main">
      
    </div>
  </div>
</template>
  
<script>
export default {
  name: 'HelloVue',
  props:{
    
  },
  data() {
    return {

    }
  },
  methods: {

  }
}
</script>
<style>
* {
  margin: 0;
  padding: 0;
}

.contain {
  width: 100%;
  height: 850px;
  background-position: fixed;
  background-repeat: no-repeat;
  background-image: url(../assets/back1.png);
}

.main {
}
</style>
```

# 启动Vue项目

项目目录下,终端中输入指令:

> npm run serve



# 模板语句,条件渲染,列表渲染

```vue
<template>
  <div class="hello">
    <!-- 动态现实内容 -->
    <h1>{{ msg }}</h1><!-- 文本 -->
    <div v-html="url1"></div><!-- 网址 -->
    <div v-bind:id="sb1"></div><!-- 属性 -->
    <P>-------------------------------------------------------------</P><br/>
    <!-- 三元运算 -->
    {{ ok ? 'Yes' : 'No' }}
    <P>-------------------------------------------------------------</P><br/>

    <!-- 条件渲染 -->
    <p v-if="flag">I am a SB</p><!-- if-else判断 -->
    <p v-else>I am not a SB</p>

    <p v-show="flag">This is v-show part</p>
    <!-- v-if 和 v-show的区别? -->
    <P>-------------------------------------------------------------</P><br/>

    <!-- 列表渲染 -->
    <ul>
      <li v-for="item in list1" :key="item.id">
        {{ item.title }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data(){
    return {
      msg: "test",
      url1: "<a href='https://baidu.com'>sb</a>",
      sb1: "1001",
      ok: true,
      flag: true,
      list1:[
        {
          id:1,
          title:"sb01"
        },
        {
          id:2,
          title:"sb02"
        },
        {
          id:2,
          title:"sb03"
        }
      ]
    }
  }
}
</script>
```



# 事件处理

```vue
<template>
  <div class="hello">
  <!-- 时间处理:@ 或 v-on -->
  <button @click="nums += 1">add</button>
  <br/>
  <button @click="say">say</button>
  <p>{{ nums }}</p>
  <p>{{ sayData }}</p>
  </div>
</template>

<script>
export default {
  name: 'HelloVue',
  data(){
    return{
      nums: 0,
      sayData: 'sb'
    }
  },
  methods:{
    say(event){
      console.log(event);
    }
  }
}
</script>
```



# 表单输入绑定

```vue
<template>
  <div class="hello">
    <!-- 表单输入绑定: v-model
      trim: 自动过滤用户输入的首位空白字符
     -->
    <input type="text" v-model.trim="text1"/>
    <p>{{ text1 }}</p>
  </div>
</template>

<script>
export default {
  name: 'HelloVue',
  data(){
    return{
      text1: 'Empty'
    }
  },
  methods:{
    
  }
}
</script>
```

# 组件

vue代码由三部分组成:

template;script;style



vue中的组件通常会以组件树的形式来组织:比如在App.vue中引入默认的页面,也可以在默认的页面中再引入子组件
项目的页面可以通过App.vue来设置

## 向子组件传递元素

App向Test传递元素



App

```vue
<template>
  <Test :name="name" :age="age" :sons="sons"/>
</template>

<script>
import Test from './components/Test.vue'

export default {
  name: 'App',
  components: {
    Test
  },
  data() {
    return {
      name: 'Jaylen',
      age: 19,
      sons:['sb1','sb2','sb3']
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>

```

Test

```vue
<template>
  <div class="contain">
    <div class="main">
      <p>name:{{ name }}</p>
      <p>age:{{ age }}</p>
      <p>sons:{{ sons }}</p>
    </div>
  </div>
</template>
  
<script>
export default {
  name: 'HelloVue',
  props:{
    name:{
      type:String,
      default:""
    },
    age:{
      type:Number,
      default:0
    },
    sons:{
      type:Array,
      //数组和对象必须用函数返回
      default:function(){
        return []
      }
    }
  },
  data() {
    return {

    }
  },
  methods: {

  }
}
</script>
<style>
* {
  margin: 0;
  padding: 0;
}

.contain {
  width: 100%;
  height: 850px;
  background-position: fixed;
  background-repeat: no-repeat;
  background-image: url(../assets/back1.png);
}

.main {
}
</style>
```

## 向父组件传递元素

Test --> App

Test

```vue
<template>
  <div class="contain">
    <div class="main">
      <button @click="sentMessage">点击传递</button>
    </div>
  </div>
</template>
  
<script>
export default {
  name: 'HelloVue',
  props:{
    
  },
  data() {
    return {
      message:"我是component数据"
    }
  },
  methods: {
    sentMessage(){
      this.$emit("onEvent",this.message)
    }
  }
}
</script>
<style>
* {
  margin: 0;
  padding: 0;
}

.contain {
  width: 100%;
  height: 850px;
  background-position: fixed;
  background-repeat: no-repeat;
  background-image: url(../assets/back1.png);
}

.main {
}
</style>
```



App

```vue
<template>
  <Test @onEvent="getMessage"/>
  <p>{{ message }}</p>
</template>

<script>
import Test from './components/Test.vue'

export default {
  name: 'App',
  components: {
    Test
  },
  data() {
    return {
      message:""
    }
  },
  methods:{
    getMessage(data){
      this.message = data;
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>

```

