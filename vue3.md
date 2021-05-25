# setup()和ref()函数

- setup()：可以代替Vue2中的date和methods属性，直接把逻辑写在setup里就可以
- ref()，需要在`template`里面使用的变量，都要使用`ref`包裹
- `return`出去的数据和方法，在模板中才可以使用，这样可以精准二点控制暴漏的变量和方法

```javascript
// 使用ref的时候，取值赋值要使用 .value 的形式进行，在template里面不需要加
setup() {
    const choseProject = ref('')
    const projectList = ref(['Java', 'C++', 'JavaScript', 'HTML', 'CSS', 'NodeJs'])
    const projectClick = (index) => {
        console.log(projectList.value[index])
        choseProject.value = projectList.value[index]
    }
    return {
        choseProject,
        projectList,
        projectClick
    }
}
```

# reactive函数的使用

> 使用`ref`的时候，取值赋值的时候需要加一个`.value`，比较麻烦，所以可以使用`reavtive`方法，该方法接收一个对象
>
> 如代码中所示，可以直接返回`data`即可，但是在`template`中使用的话就需要在值前面加一个`data`，因为`data`是一个对象。

```typescript
<script lang="ts">
    import { reactive } from 'vue'
    export default {
      name: 'demo01',
      setup() {
        // 给data定义接口
        interface DataProp {
          text: string;
          list: string[];
          choseProject: string;
          projectList: string[];
          projectClick: (index: number) => void;
        }
        const data: DataProp = reactive({
          text: '测试文本1',
          list: ['星期1', '星期2', '星期3', '星期4', '星期5'],
          choseProject: '',
          projectList: ['Java', 'C++', 'JavaScript', 'HTML', 'CSS', 'NodeJs'],
          projectClick: (index) => {
            data.choseProject = data.projectList[index]
          }
        })
        return {
          data
        }
      }
    }
</script>
```

# vue3中模块化的使用

> 在vue2中，如果有相同的模块需要多个组件使用的话，通常使用`mixins`，在vue3中的模块使用方式更加简单。

- 比如一个显示时间的模块，可以单独创建一个`useNowTime.ts`模块，将逻辑写在其中，并导出。

```typescript
import { ref } from 'vue'

const nowTime = ref('')
const getNowTime = () => {
  const date = new Date()
  const hours = date.getHours() < 10 ? '0' + date.getHours() : date.getHours()
  const minutes = date.getMinutes() < 10 ? '0' + date.getMinutes() : date.getMinutes()
  const seconds = date.getSeconds() < 10 ? '0' + date.getSeconds() : date.getSeconds()
  nowTime.value = hours + ':' + minutes + ':' + seconds
  setTimeout(getNowTime, 1000)
}

export { nowTime, getNowTime }
```

- 使用的时候直接引入，并在setup中返回，即可在模板中使用

```html
<template>
  <div>
    {{ nowTime }}
    <div>
      <button @click="getNowTime">显示时间</button>
    </div>
  </div>
</template>
<script lang="ts">
import { defineComponent } from 'vue'
import { nowTime, getNowTime } from '@/hooks/useNowTime'
export default defineComponent({
  setup () {
    return {
      nowTime,
      getNowTime
    }
  }
})
</script>
```

# teleport组件

> 可以将组件内部的元素全部挂载到指定的节点上去，通过`to`属性实现

```html
<!-- 将弹窗组件挂载到body上 -->
<teleport to='body'>
    <div class="model">
        我是一个弹窗组件
    </div>
</teleport>
```

