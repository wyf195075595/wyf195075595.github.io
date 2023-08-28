---
title:  mixin 实战
date: 2023-07-20 08:23:10
tags: vue
categories: vue
---

> 一个简单的mixin 使用示例，用于管理 项目中的 字典，下拉数据

### 通用字典

<!--more-->

vuex 准备

```json
const state = {
  dict: new Array(),
};
const mutations = {
  SET_DICT: (state, { key, value }) => {
    if (key !== null && key !== "") {
      state.dict.push({
        key: key,
        value: value,
      });
    }
  },
  REMOVE_DICT: (state, key) => {
    try {
      for (let i = 0; i < state.dict.length; i++) {
        if (state.dict[i].key == key) {
          state.dict.splice(i, i);
          return true;
        }
      }
    } catch (e) {}
  },
  CLEAN_DICT: (state) => {
    state.dict = new Array();
  },
};

const actions = {
  // 设置字典
  setDict({ commit }, data) {
    commit("SET_DICT", data);
  },
  // 删除字典
  removeDict({ commit }, key) {
    commit("REMOVE_DICT", key);
  },
  // 清空字典
  cleanDict({ commit }) {
    commit("CLEAN_DICT");
  },
};

export default {
  namespaced: true,
  state,
  mutations,
  actions,
  getters: {
    getDict(state) {
        return state.dict;
    },
},
};

```

1、编写组件 dict.js

```js
const options = null;

const install = function (Vue, options ){
    if(options) {
        options = options
    }
    //全局注册组件 component-name
    // Vue.component('component-name',{
        
    // })
    //添加实例方法 $Notice
    Vue.prototype.$Notice = function(){
        
    }
    //添加全局方法或属性
    Vue.globalMethod = function(){
        
    }
    //添加全局混合
    Vue.mixin({
        data() {
            if (this.$options === undefined || this.$options.dicts === undefined || this.$options.dicts === null) {
                return {}
            }
            const dict = new Dict()
            dict.owner = this
            return {
                dict
            }

        },
        created() {
            if (!(this.dict instanceof Dict)) {
                return
            }
            console.log(this.dict);
            this.dict.init(this ,this.$options.dicts , options).then(res => {
                
                console.log(res);
            })
        }
    })
}

/**
 * @classdesc 字典
 * @property {Object} label 标签对象，内部属性名为字典类型名称
 * @property {Object} dict 字段数组，内部属性名为字典类型名称
 * @property {Array.<DictMeta>} _dictMetas 字典元数据数组
 */
class Dict {
    constructor() {
        // 当前组件实例
        this.owner = null
        /**
         * 
         * 用于将 code 回显成值
         *
         {
            sys_normal_disable: {
                0: "正常",
                1: "异常",
            }
         }
         *  */ 
        
        this.label = {}
        /**
         * DictData
         * 用于下拉框展示
         *
         {
            sys_normal_disable: [
                { labelName: "异常", labelValue: 0}
            ]
         }
         *  */ 
        this.type = {}
    }
    async init(Vue ,dicts, options) {
        let rs = [];
        for (let dictType of dicts) {
            try {
                let d = await options.request(dictType);
                rs.push(d)
                d.map(item => {
                    Vue.$set(Vue.dict.label, [item.dictValue], item.dictLabel)
                })
                Vue.$set(Vue.dict.type, dictType, d)
                
            } catch (error) {
                console.log(`load faild ${dictType},`,error);         
            }
        }
        
        return Promise.all(rs)
    }
    
}

export default {
    install
}
```

2、main.js 注册 组件

```js
// 从 store 中查询此字典是否已加载
function searchDictByKey(dict, key) {
  if (key == null && key == "") {
    return null
  }
  try {
    for (let i = 0; i < dict.length; i++) {
      if (dict[i].key == key) {
        return dict[i].value
      }
    }
  } catch (e) {
    return null
  }
}
Vue.use(dict, {
  labelField: 'dictLabel',
  valueField: 'dictValue',
  request(dictType) {
    const storeDict = searchDictByKey(store.getters["dictModule/getDict"], dictType)
    if (storeDict) {
      return new Promise(resolve => { resolve(storeDict) })
    } else {
      return new Promise((resolve, reject) => {
        fetch(`http://localhost:8081/api/system/dict/data/type/${dictType}`, {
            headers: {"Authorization": `Bearer eyJhbGciOiJIUzUxMiJ9.eyJ1c2VyX2lkIjoxLCJ1c2VyX2tleSI6IjFhZWNjYmM2LTJmNDItNDMyNS05ZTkxLTViNGEyZTc1MmUxMCIsInVzZXJuYW1lIjoiYWRtaW4ifQ.c8w0X5ydwbxBFANwfykVC3WjDS-0X6LCBXd_5nnc05I25YLxmkIyRRsR9fAaitkuNYqUHkcQXca99ZWbNxPKyw`}
        }).then(res=> res.json()).then(res=> {
          store.dispatch('dictModule/setDict', { key: dictType, value: res.data })
          resolve(res.data)
        }).catch(error => {
          reject(error)
        })
      })
    }
  },
})
```

3、在页面使用

```vue
<template>
  <div class="home">
      <el-select v-model="val" placeholder="系统内置" clearable>
          <el-option
              v-for="dict in dict.type.sys_yes_no"
              :key="dict.dictValue"
              :label="dict.dictLabel"
              :value="dict.dictValue"
          />
      </el-select>
  </div>
</template>

<script>
export default {
  name: 'HomeView',
  dicts: ['sys_normal_disable','sys_yes_no'],
  data() {
    return {
      val: ''
    }
  },
  components: {
    HelloWorld
  },
  mounted() {
    console.log("输出mixin 中的 dict", this.dict);
  }
}
</script>
```

> dicts 在组件 dict 中 通过 $options 获取，然后在 created 函数中 初始化 对应的字典，然后在 mixin 中 暴露 dict 变量，存放字典数据

