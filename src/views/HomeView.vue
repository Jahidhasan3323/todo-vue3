<template>
  <div>
    <div class="container">
      <div class="row">
        <div class="col-md-8 offset-md-2">
          <div class="card mt-5">
            <div class="card-header ">
              Todo list
              <button class="btn btn-info float-end text-white" @click="createTodo">+
                Add New
              </button>
            </div>
            <div class="card-body">
              <form>
                <div class="mb-3">
                  <input type="text" class="form-control" id="search" placeholder="search here ...">
                </div>
              </form>
              <div class="todo-list">
                <ul class="list-group">
                  <li class="list-group-item bg-info text-white" aria-current="true"><b>Todo list</b></li>
                  <li class="list-group-item" v-if="todoList.data" v-for="(todo, index) in todoList.data"
                      :key="todo.id">
                    {{ todo.title }}
                    <button @click="deleteTodo(index, todo.id)" class="btn btn-danger float-end ">x</button>
                    <button @click="editTodo(todo)" class="btn btn-info float-end text-white mx-1">Edit</button>
                  </li>
<!--                  <li class="list-group-item" v-else>-->
<!--                    No data found-->
<!--                  </li>-->
                </ul>
              </div>
            </div>
          </div>

        </div>
      </div>
    </div>
  </div>
  <!-- Modal -->
  <button ref="modal" class="btn btn-info float-end text-white d-none" data-bs-toggle="modal" data-bs-target="#exampleModal">+
    Add New
  </button>
  <CreateTodo :editableData="editableData" @newTodoEmit="newTodoEmit" @updateTodoEmit="updateTodoEmit"></CreateTodo>
</template>

<script>
// @ is an alias to /src

import {ref} from "vue";
import CreateTodo from "@/components/todo/CreateTodo";

export default {
  name: 'HomeView',
  components: {CreateTodo},
  setup(props, context) {
    const todoList = ref([]);
    const editableData = ref({});
    const modal = ref(null);

    const getData = () => {
      axios.get('http://todo-backend.local/api/todo')
          .then((res) => {
            todoList.value = res.data;
          })
    }
    const deleteTodo = (index, id) => {
      axios.delete('http://todo-backend.local/api/todo/'+id)
          .then((res) => {
            todoList.value.data.splice(index,1)
          })
    }
    const newTodoEmit = (value) => {
      todoList.value.data.unshift(value)
    }
    const updateTodoEmit = (value) => {
      getData()
    }
    const editTodo = (value) => {
      editableData.value = value
      modal.value.click();
    }
    const createTodo = () => {
      editableData.value = {}
      modal.value.click();
    }
    getData();
    return {
      todoList,
      newTodoEmit,
      deleteTodo,
      editTodo,
      modal,
      createTodo,
      editableData,
      updateTodoEmit
    }
  }
}
</script>
