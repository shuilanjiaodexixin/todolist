<template>
  <div id="app">
    <!-- 顶部 -->
    <div class="fixed-top">
      <div class="container">
        <!-- 添加待办事项 -->
        <form class="input-form" @submit.prevent="addTask">
          <label class="form-label" for="content">Todo</label>
          <input type="text" ref="content">
          <button type="submit" class="btn-regular">Add</button>
        </form>
        <!-- 筛选项 -->
        <div class="status-box">
          <label v-for="item in options" :key="item.value">
            <input type="checkbox" :value="item.value" v-model="filterOption">
            <span class="status-name">{{item.name}}</span>
            <span :class="['badge', 'badge-'+item.color]">{{todoCounts(item.value)}}</span>
          </label>
    
        </div>
      </div>
      
    </div>
    <!-- 任务列表 -->
    <div class="main-content">
      <div class="list-group">
        <draggable handle=".todo-move" @end="onDragEnd">
        <todo-item v-for="item in filteredTodos" 
        :key="item.id" 
        :todo="item" @edit="editTask(item.id)">
        
        </todo-item>
        </draggable>
      </div>
    </div>
    
    <!-- 弹框 -->
    
    <modal-dialog v-if="isEditing" :todo="editingItem" @close="closeModal"/>
    
  </div>
</template>

<script>
import TodoItem from './components/TodoItem'
import ModalDialog from './components/ModalDialog'
import { TaskState } from './assets/Todo.js'
import draggable from 'vuedraggable'


export default {
  name: 'App',
  components:{
    TodoItem,
    ModalDialog,
    draggable
  },
  data(){
    return{
      isEditing: false,
      options: TaskState,
      filterOption: [TaskState[0].value, TaskState[1].value],
      editingItem: null,
    }
  },
  computed: {
    filteredTodos() {
      return this.$store.getters.getFilteredTodos(this.filterOption)
    }
  },
  methods:{
    onDragEnd(e) {
      // 拖拽之前和之后的index没变，就直接return
      if (e.newIndex == e.oldIndex) {
        return
      }
      let params = {
        oldIndex: e.oldIndex,
        newIndex: e.newIndex,
        option: this.filterOption
      }
      //提交到store中
      this.$store.commit('changeOrder', params)
    },
    addTask(){
      let content = this.$refs.content;
      if(!content.value.trim().length) return;
      //提交mutataion
      this.$store.commit('addTask', content.value);
      //添加完之后，清空输入框
      content.value=''
    },
    // 获取各状态的任务数量
    todoCounts(state) {
      return this.$store.getters.getTaskCount(state)
    },
    editTask(id){
      this.isEditing = true
      this.editingItem=this.$store.getters.getTodoById(id)
    },
    // 关闭编辑弹框
    closeModal() {
      this.isEditing = false;
    },

  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  /* -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center; */
  color: #212529;
  margin-top: 80px;
}
*, ::after, ::before{
  box-sizing: border-box;
  padding: 0;
  margin: 0;
}
.container{
  width: 100%;
  max-width: 720px;
  padding: 0 15px;
  margin: 0 auto;
}
.btn-regular{
  padding: .25rem .5rem;
  color: #fff;
  background-color: #007bff;
  border:1px solid #007bff;
  border-radius: 0;
  font-size: .875rem;
  line-height: 1.5;
}
button{
  cursor: pointer;
}
.fixed-top{
  position:fixed;
  top:0;
  left:0;
  width: 100%;

}
.input-form{
  display: flex;
  width: 100%;
  margin-top: 15px;
}
.form-label{
      display: flex;
      font-size: 1rem;
      border: 1px solid#979797;
      border-right: none;
      background: #faf9f9;
      line-height: 1.5;
      padding: .25rem .5rem;
}
input[type="text"]{
  padding: 2px 4px;
  font-size: 1rem;
  flex-grow: 1;
}

/* <!-- 筛选项 --> */
.status-box {
  display: flex;
  justify-content: space-between;
  margin-top: 10px;
}
label{
    display: flex;
    align-items:center;
  }
.status-name {
  margin: 0 5px;
}

.badge {
      display: inline-block;
      padding: 2px 5px;
      text-align: center;
      border-radius: .25rem;
      font-size: 75%;
      white-space: nowrap;
      font-weight: bold;
      vertical-align: baseline;
    }




</style>
