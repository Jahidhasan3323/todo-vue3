<template>
  <div class="modal fade show" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="exampleModalLabel">Add New Todo</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <form id="todoForm" v-on:submit.prevent>
            <div class="mb-3">
              <label for="title" class="form-label">Title</label>
              <input type="text" class="form-control" id="title" v-model="title" name="title" required>
            </div>
            <button type="button" @click="submitTodo" class="btn btn-primary">Submit</button>
          </form>
        </div>
        <div class="modal-footer">
          <button ref="modalCloseBtn" type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import {defineEmits, reactive, ref, watch} from "vue";

export default {
  name: "createTodo",

  props: ['editableData'],
  setup(props, context) {
    const title = ref(null);
    const id = ref(null);
    const modalCloseBtn = ref(null);

    const submitTodo = () => {
      let formData = new FormData(document.getElementById('todoForm'));
      if (Object.keys(props.editableData).length) {
        updateTodo(formData)
      } else {
        storeTodo(formData)
      }
    }
    const storeTodo = (formData) => {
      axios.post('http://todo-backend.local/api/todo', formData)
          .then((res) => {
            modalCloseBtn.value.click();
            title.value = null;
            context.emit('newTodoEmit', JSON.parse(res.data.data));
          })
    }
    const updateTodo = (formData, id) => {
      axios.post('http://todo-backend.local/api/todo/update/' + props.editableData.id, formData)
          .then((res) => {
            modalCloseBtn.value.click();
            title.value = null;
            context.emit('updateTodoEmit', JSON.parse(res.data.data));
          })
    }
    watch(() => props.editableData, () => {
      id.value = props.editableData.id;
      title.value = props.editableData.title;
    })
    return {
      title,
      id,
      submitTodo,
      modalCloseBtn
    }
  }
}
</script>

<style scoped>

</style>