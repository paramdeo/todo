<script setup>
import { ref } from 'vue'

function id() {
  // all we really need is the first 8 chars for a unique ID, not the entire UUID
  return crypto.randomUUID().slice(0, 8)
}

const title = 'To-Do',

  todoList = ref([]),

  newTodo = ref(''),

  charLimit = 50,

  urgent = ref(false),

  urgentIcon = `<svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 32 32" fill="none" stroke="#ffcc00" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-siren fs-4 float-end mt-1"><path d="M7 12a5 5 0 0 1 5-5v0a5 5 0 0 1 5 5v6H7v-6Z"/><path d="M5 20a2 2 0 0 1 2-2h10a2 2 0 0 1 2 2v2H5v-2Z"/><path d="M21 12h1"/><path d="M18.5 4.5 18 5"/><path d="M2 12h1"/><path d="M12 2v1"/><path d="m4.929 4.929.707.707"/><path d="M12 12v6"/></svg>`,

  addTodo = (urgency = false) => {
    // if the item is empty, don't add to list
    if (newTodo.value) {
      todoList.value.unshift({
        id: id(),
        title: newTodo.value,
        urgent: urgency
      })
      // reset input text and urgency
      newTodo.value = ''
      urgent.value = false
      updateTimestamp(Date.now())
    }
  },

  clearTodos = () => {
    // nuke the array items
    todoList.value = []
    lastUpdated.value = ''
  },

  saveTodos = () => {
    // localStorage can only store strings
    localStorage.setItem('todos', JSON.stringify(todoList.value))
  },

  loadTodos = () => {
    todoList.value = JSON.parse(localStorage.getItem('todos'))
  },

  lastUpdated = ref(''),

  updateTimestamp = () => {
    let timestamp = new Date(Date.now())
    lastUpdated.value = timestamp.toLocaleDateString() + ' @ ' + timestamp.getHours() + ':' + (timestamp.getMinutes() < 10 ? '0' + timestamp.getMinutes().toString() : timestamp.getMinutes())
  }
</script>

<template>
  <div class="row mt-5">
    <div class="col">
      <h1 class="mb-3">{{ title }}</h1>
      <div class="card mb-5">
        <ul class="list-group list-group-flush">
          <li class="list-group-item fs-4 text-muted disabled" v-if="!todoList.length">No items yet.</li>
          <li class="list-group-item fs-4" v-for="todo in todoList" :key="todo.id">{{ todo.title }} <span v-if="todo.urgent" v-html="urgentIcon"></span></li>
        </ul>
        <div v-if="lastUpdated" class="card-footer text-end">Last updated {{ lastUpdated || '' }}</div>
      </div>
      <hr/>
    </div>
  </div>

  <div class="row">

    <div class="mb-3">

      <form v-on:submit.prevent="addTodo(urgent)">

        <div class="input-group input-group-lg my-3">
          <input type="text" class="form-control form-control-lg" placeholder="Add new todo item" v-model.trim="newTodo"
            v-bind:maxlength="charLimit" autofocus>
          <div class="input-group-text">
            <input class="form-check-input mt-0" type="checkbox" id="checkboxUrgent" v-model="urgent">
            <label class="form-check-label fs-6 text-uppercase fw-bold" for="checkboxUrgent">&ensp;Urgent</label>
          </div>

          <span class="input-group-text">{{ newTodo.length }}/{{ charLimit }}</span>
        </div>

        <button type="submit" class="btn btn-primary w-100" v-bind:disabled="!newTodo">Add Item</button>
      </form>

    </div>

    <div class="d-flex justify-content-evenly">

      <button class="btn btn-danger w-100 me-2" v-on:click="clearTodos" v-if="todoList.length">Clear List</button>
      <button class="btn btn-success w-100 me-2" v-on:click="saveTodos">Save Todos</button>
      <button class="btn btn-dark w-100" v-on:click="loadTodos">Load Todos</button>

    </div>

  </div>
</template>
