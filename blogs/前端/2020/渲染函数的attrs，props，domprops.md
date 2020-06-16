---
title: 渲染函数的attrs，domprops, props，
date: 2020-06-16
tags:
 - vue
categories:
 - 前端
---


## 事例

attrs用于定义普通的 HTML attribute，domprops用于定义DOM property, prop用于定义组件的prop，但似乎domProps是万能的？对于渲染函数不是很了解。

### 父组件

```vue
<template>
  <div class="about">
    <h1>This is an about page</h1>
    <!-- 使用 -->
    <RenderComponent :value = 'name' />
  </div>
</template>
<script>
//引入子组件
import RenderComponent from '@/components/RenderComponent.js'

export default {
  name: 'Home',
  data () {
    return {
      name: 'galen'
    }
  },
  components: {
    RenderComponent
  }
}

</script>
```

### 使用attrs子组件

给它添加了innerHTML，id，style，其中id是由父组件传过来的

```js

export default {
    data(){
        return {
            // id : 'dataId'
        }
    },
    props : ['value'],
    render(h){
        let self = this
        return h(
            'a',
            {
                attrs : {
                    innerHTML : 'renderComponents-props-data',
                    id : self.value,
                    style : 'color : red',
                },
            },
            'renderComponents-data'
        )
    }
}
```

### 使用attrs子组件的渲染结果

结果显示，id，style正常渲染，而innerHTML则直接添加到了自定义属性，即attrs只是用于定义普通的 HTML attribute。

```html
<a innerhtml="renderComponents-props-data" id="galen" style="color : red">renderComponents-data</a>

```

### 使用domProps子组件

给它添加了innerHTML，id，style，其中id是由父组件传过来的

```js

export default {
    data(){
        return {
            // id : 'dataId'
        }
    },
    props : ['value'],
    render(h){
        let self = this
        return h(
            'a',
            {
                domProps : {
                    innerHTML : 'renderComponents-domProps-data',
                    id : self.value,
                    style : 'color : red',
                },
            },
            'renderComponents-data'
        )
    }
}
```

### 使用domProps子组件的渲染结果

结果显示，id，style正常渲染,innerHTML覆盖了本来的文本，即domProps用于定义DOM property。

```html
<a id="galen" style="color: red;">renderComponents-domProps-data</a>
```
