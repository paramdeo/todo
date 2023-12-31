<script setup>
import { ref } from 'vue'

function id() {
  // all we really need is the first 8 chars for a unique ID, not the entire UUID
  return crypto.randomUUID().slice(0, 8)
}

const title = 'To-Do',

  todoList = ref([]),

  newTodo = ref(''),

  lastUpdated = ref(''),

  savedTodos = ref(localStorage.getItem('todos')),

  charLimit = 50,

  deleteIcon = `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="#ff0000" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-x-circle fs-4 float-end mt-1"><circle cx="12" cy="12" r="10"/><path d="m15 9-6 6"/><path d="m9 9 6 6"/></svg>`,

  addTodo = () => {
    // if the item is empty, don't add to list
    if (newTodo.value) {
      todoList.value.unshift({
        id: id(),
        title: newTodo.value,
      })
      // reset input text and time
      newTodo.value = ''
      updateTimestamp(Date.now())
    }
  },

  deleteTodo = (todoID) => {
    // if there's only one item left, also nuke the updated time
    if (todoList.value.length === 1) {
      lastUpdated.value = ''
    }
    todoList.value = todoList.value.filter(todo => todo.id !== todoID)
  },

  clearTodos = () => {
    // nuke the array items
    todoList.value = []
    lastUpdated.value = ''
  },

  saveTodos = () => {
    // localStorage can only store strings
    localStorage.setItem('todos', JSON.stringify(todoList.value))
    savedTodos.value = localStorage.getItem('todos')
  },

  loadTodos = () => {
    todoList.value = JSON.parse(localStorage.getItem('todos'))
  },

  updateTimestamp = () => {
    let timestamp = new Date(Date.now())
    lastUpdated.value = timestamp.toLocaleDateString() + ' @ ' + timestamp.getHours() + ':' + (timestamp.getMinutes() < 10 ? '0' + timestamp.getMinutes().toString() : timestamp.getMinutes())
  }
</script>

<template>
  <div class="container">

    <div class="row mt-5">

      <div class="col-3 d-none d-lg-block"></div>

      <div class="col">

        <h1 class="mb-3">{{ title }}</h1>

        <div class="card mb-3">
          <ul class="list-group list-group-flush">
            <li class="list-group-item fs-4 text-muted disabled" v-if="!todoList.length">No items yet.</li>
            <li class="list-group-item fs-4" v-for="todo in todoList" :key="todo.id">{{ todo.title }} <span
                v-on:click="deleteTodo(todo.id)" v-html="deleteIcon" style="cursor: pointer"></span></li>
          </ul>
          <div v-if="lastUpdated" class="card-footer text-end">Last updated {{ lastUpdated || '' }}</div>
        </div>

        <div class="bg-light mb-3" style="height: 3px"></div>

        <div class="mb-3">
          <form v-on:submit.prevent="addTodo">
            <div class="input-group input-group-lg my-3">
              <input type="text" class="form-control form-control-lg" placeholder="Add todo..." v-model.trim="newTodo"
                v-bind:maxlength="charLimit" autofocus>
              <span class="input-group-text">{{ newTodo.length }}/{{ charLimit }}</span>
            </div>

            <button type="submit" class="btn btn-primary w-100 fs-5" v-bind:disabled="!newTodo">Add Todo</button>
          </form>
        </div>

        <div class="d-flex justify-content-evenly">
          <button class="btn btn-danger w-100 me-2 fs-5" v-on:click="clearTodos" v-if="todoList.length">Clear
            Todos</button>
          <button class="btn btn-info w-100 me-2 fs-5" v-on:click="saveTodos">Save Todos</button>
          <button class="btn btn-success w-100 fs-5" v-bind:disabled="!savedTodos" v-on:click="loadTodos">Load
            Todos</button>
        </div>

        <div class="text-center mt-3">
          <p>Copyright &copy; <a href="https://paramdeo.com" target="_blank" rel="noopener">Paramdeo Singh</a>
            <strong>&middot;</strong> <a href="https://github.com/paramdeo/todo" target="_blank" rel="noopener">Source</a>
          </p>
        </div>

      </div>

      <div class="col-3 d-none d-lg-block"></div>

    </div>

  </div>
</template>
