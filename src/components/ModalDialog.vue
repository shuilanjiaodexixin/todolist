<template>
    <div class="modal-container">
        <div class="form-row">
          <div class="status-labels">

            <label for="" class="status-label" v-for="item in options" :key="item.value">
              <input type="radio" v-model="todoCopy.state" :value="item.value">
              <span>{{item.name}}</span>
            </label>

          </div>
        </div>

        <div class="from-row">
          <input type="text" class="input-text" v-model="todoCopy.content">
        </div>

        <div class="from-row">
          <textare maxlength="1000" rows="3" v-model="todoCopy.note"/>
        </div>

        <div class="modal-footer">
          <button class="btn-regular btn-model" @click.stop="okHandler">OK</button>
          <button class="btn-gray btn-model" @click.stop="cancelHandler">Cancel</button>
        </div>

      </div>

</template>


<script>
import { TaskState } from '../assets/Todo'
export default {
  name:'ModalDialog',
  props:{
    todo: Object
  },
  data(){
    return {
      options: TaskState,
      todoCopy: Object.assign({},this.todo)
    } 
  },
  methods:{
    okHandler() {
      this.$store.commit('updateTask', this.todoCopy)
      this.$emit('close');
    },
    cancelHandler() {
      this.$emit('close');
    }
    }
}
</script>

<style scoped>
/* 编辑的弹框 */
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.1s ease;
}
  .modal-container {
    width: 500px;
    min-height: 150px;
    margin: 30px auto 0;
    padding: 20px 30px;
    background-color: #fff;
    border-radius: 2px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
    transition: all 0.3s ease;
    
  }

  .form-row {
    margin: 10px 0;
  }

  .modal-footer {
    margin: 5px 0;
    height: 20px;
  }

  .input-text {
    width: 100%;
    line-height: 1.5;
    padding: 2px 4px;
    font-size: 1rem;
  }

  textarea {
    width: 100%;
    resize: none;
    padding: 5px 10px;
    font-size: 14px;
    line-height: 1.5;
  }

  .btn-modal {
    margin-left: 10px;
    float: right;
  }

  .btn-gray {
    padding: .25rem .5rem;
    color: black;
    background-color: #aca8a8;
    border: 1px solid #aca8a8;
    border-radius: 0;
    font-size: .875rem;
    line-height: 1.5;
  }

  .status-labels {
    display: flex;
    justify-content: space-between;
  }

  .status-label span {
    margin: 0 5px;
  }

</style>