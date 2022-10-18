---
title:  插件
date: 2022-06-17 08:23:10
tags: vue
categories: 
- vue
- v-model
---
## 一、Vue2 中 v-model 的使用

v-model 是[语法糖](https://so.csdn.net/so/search?q=语法糖&spm=1001.2101.3001.7020)，本质还是父子组件间的通信。父子组件通信时有两种方式：

1. 父给子传值：通过 props
2. 子给父传值：通过 Events up，使用 $emit 触发事件。

<!--more-->

**1.1、父组件给子组件传值**

父组件代码

```xml
<child  :msg="msg"  />

<script>

 export default{
  data(){
   return{
    msg:"父组件给子组件传值"
   }
  }
 }
</script>
```

子组件代码

```xml
<template>
 <div>
  {{msg}} 
 </div>  
</template>

<script>
 export default{
  props:{
   msg:{
    type:String,
    default:""
   }
  } 
 }

</script>
```

props 是单向数据流，子组件只能读取，并不能修改 msg 。对象类型可以，但不建议修改。

**1.2、父子组件双向绑定**

子组件同步一个数据的时候，子组件既要使用，还要修改这个数据，以前需要通过 props Down 和 Events Up 来实现。现为了方便，提供了特殊的语法糖 v-model 。

父组件代码：

```python
{{msg}}

<set-input v-model="msg" />
```

子组件 SetInput.vue 文件代码

```xml
<template>

 <div>

  <input type="text" v-model="newValue">

 </div>

</template>

<script>


export default {

 props:{

  value:{

   type:String,

   default:''

  }

 },


 computed:{

  newValue:{

   get:function(){

    return this.value

   },

   set:function(value){

    this.$emit('input',value)

   }
  }

 }

}

</script>
```

v-model = "msg" 可以翻译为：

```ruby
:value="msg"


@input="msg=$events"
```

所以父组件的代码可以翻译为：

```python
<set-input :value="msg" @input="msg=$events" />
```

在一个组件上，v-model 只能使用一次，如果想要对多个数据同步绑定，则可以使用.sync修饰符。

## 二、.sync修饰符

.sync 和 v-model 都是语法糖，本质还是父子组件间的通信。使用 .sync 修饰符实现父子组件多个数据双向绑定。

因为 vue2 中组件只能定义一个 v-model，如果父子需要实现多个数据双向绑定，就需要借助 .sync 修饰符。

.sync 使用原理：

```ruby
<child-comp v-model="msg" :foo.sync="foo"  />


//可翻译为



<child-comp 

 :value="msg" @input="msg=$event"   

 :foo="foo" @update:foo="foo=$event"  />
/*********** 一个完整的代码示例 *************/

//父组件代码


<child-comp v-model="msg" :foo.sync="foo"  />
    

//子组件代码

<template>

 <div>
  <input type="text" v-model="newFoo">
  <input type="text" v-model="newValue">
 </div>

</template>

<script>

export default {

 props:{

  value:{

   type:String,

   default:''
  },
  foo:{

   type:String,

   default:""
  }
 },


 computed:{

  newValue:{

   get:function(){

    return this.value
   },

   set:function(value){

    this.$emit('input',value)

   }

  },


 newFoo:{

  get:function(){

   return this.foo

  },

  set:function(value){

   this.$emit('update:foo',value)

  }

 }

 }

}



</script>  
```

## 三、Vue3.x 使用 v-model

vue2 中的 v-model 和 .sync 功能重叠，容易混淆，因此 vue3 做了统一，一个组件可以多次使用 v-model 。

**3.1、vue 3 的v-model使用原理：**

```ruby
<child-comp v-model="msg" /> 


//可翻译为



<child-comp :modelValue="msg" @update:modelValue="msg=$event" /> 
```

单个数据双向绑定完整示例

```vue
//父组件代码



<child-comp v-model="name" />




子组件代码：



<template>


 <input type="text" v-model="newValue">

</template>


<script>

export default {

 props:{

  modelValue:{

   type:String,

   default:''

  }

 },

 computed:{

  newValue:{

   get:function(){

    return this.modelValue

   },
   set:function(value){

    this.$emit('update:modelValue',value)
   }

  }

 }

}

</script>
```

vue3 使用特定的 modelValue ，避免 value 的占用，通过 update:modelValue 实现数据双向绑定。值得注意的是，vue3 移除了 model 选项。

**3.2、多个 v-model 使用**

在 vue3 一个组件可以使用多个 v-model ，统一了 vue2 的 v-model 和 .sync 修饰符。

使用原理：

```ruby
<child-comp v-model:name="name" v-model:age="age" /> 


  //可翻译为



<child-comp 



  :name="name" @update:name="name=$event"



  :age="age" @update:age="age=$event" /> 
```

实现多个数据双向绑定完整实例：

```vue
//父组件代码

<child-comp v-model:name="name" v-model:age="age" /> 

 //子组件代码


<template>

 <div>
  <input type="text" v-model="newName">

  <input type="text" v-model="newAge">

 </div>

</template>

<script>

export default {

 props:{

  name:{

   type:String,

   default:''

  },

  age:{
   type:String,

   default:""

  }

 },


 emits:['update:name','update:age'],

 computed:{

  newName:{

   get:function(){

    return this.name

   },

  set:function(value){

    this.$emit('update:name',value)

   }

 },

 newAge:{

  get:function(){

   return this.age

  },

  set:function(value){

   this.$emit('update:age',value)

  }
  }

 }

}



</script>  
```

需要注意的是 script 中多了一个 emits 选项，你发现了吗？

vue3 组件的自定义事件需要定义在 emits 选项中，只要是自定义事件，就需要添加在 emits 中，否则会有警告。它的优点：

1. 如果与原生事件相同名时，事件就会被触发两次，如果在 emits 选项中加入时，当作自定义事件处理，只会触发一次。
2. 更好地指示组件的工作方式。
3. 可以校验对象形式的事件。

## 四、总结

从 vue2.0 开始，组件上自定义 v-model 进行数据双向绑定，不断改进它的使用。过程如下：

```xml
<child-comp v-model="msg" /> 
```

**4.1、vue2.0 可以翻译为：**

```ruby
<child-comp :value="msg" @input="msg=$event" />
```

存在问题：v-model 和 value 有一个强绑定关系，如果子组件中有一个 input 原生标签，此时就会影响原生标签的 value 和 input 事件。

**4.2、vue2.2版本**

引入了 model 组件选项，允许开发者任意指定 v-model 的 props 和 event 。这样就解决了 vue2.0 中的 v-model 和 value 的强绑定关系。但是还是存在一个问题，就是多个数据双向绑定时，还是需要借助 .sync 修饰符。

**4.3、vue3.x 版本**

Vue3 可以翻译为：

```ruby
<child-comp :modelValue="msg" @update:modelValue="msg=$event" /> 
```

vue3 统一 使用 v-model 进行多个数据双向绑定，废除了 model 组件选项。



[外部链接](https://blog.csdn.net/weixin_43880397/article/details/121093240)

