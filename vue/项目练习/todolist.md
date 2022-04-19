简介

Vue3 todolist完成简单的todolist的增加删除展示

+ vue3安装
+ element UI安装

```
npm install element-plus --save
```

```
import Element from "element-plus";
import "element-plus/dist/index.css";

createApp(App).use(Element).mount("#app");
```

代码实现

```js
<template>
  <!-- eslint-disable -->
  <div class="container">
    <el-card class="box-card">
      <template #header>
        <div class="card-header">
          <span>以下事件未完成</span>
        </div>
      </template>
      <div
        v-for="(item, index) in state.allItemList"
        :key="index"
        class="text item"
      >
        {{index}}---{{ item.content }}
        <el-button type="danger" @click="delItem(item.id)">删除</el-button>
      </div>
      <div class="text item">
        <el-input
          v-model="state.textarea"
          :rows="2"
          type="textarea"
          placeholder="Please input"
        />
        <el-button type="danger" @click="addItem()">添加</el-button>
      </div>
    </el-card>

  </div>
</template>

<script lang="ts">
/* eslint-disable */
import { defineComponent, reactive, ref} from "vue";
export default defineComponent({
  name: "TodoList",
  setup() {
    // @ts-ignore
    const state = reactive({
      allItemList: [{
        id: 1,
        content: '学习数据库',
      },{
        id: 2,
        content: '性能优化',
      },{
        id: 3,
        content: '学习JS',
      }],
      textarea:''
    });

    // @ts-ignore
    const delItem = (id) =>{
       for(let i = 0; i<state.allItemList.length;i++){
         if(state.allItemList[i].id==id){
           state.allItemList.splice(i,1)
         }
       }
    }
    const addItem = () =>{
       const addId = state.allItemList[state.allItemList.length-1].id
       state.allItemList.push({
         id: +addId+1,
         content: state.textarea,
       })
    }
    return {
      state,
      delItem,
      addItem
    };
  },
});
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.card-header {
  display: flex;
  align-items: center;
  justify-content: flex-start;
}

.text {
  font-size: 14px;
}

.item {
  margin-bottom: 18px;
  text-align: left;
}

.box-card {
  width: 480px;
}
</style>

```

