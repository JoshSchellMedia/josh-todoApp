<template>
  <div id="app">
    <div v-if="isLoggedIn">
      <img src="./assets/logo.png" class="logo">
      <todo-list></todo-list>
      <button @click="logout">LOGOUT</button>
    </div>
    <login-page v-else @saveApiTokens="saveApiTokens" />
  </div>
</template>

<script>
/* eslint-disable */
import axios from 'axios'
import moment from 'moment'
import LoginPage from "@/components/LoginPage.vue"
import TodoList from '@/components/TodoList.vue'

export default {
  name: 'App',
  components: { LoginPage, TodoList },
  data () {
    return {
      baseURL: 'https://vue-todos.robertmckenney.ca/api',
      api: {
        accessToken: null,
        expiresAt: null
      },
      taskList: [
        
      ]
    }
  },

  created () {
  axios
  .get(`${this.baseURL}/priorities`)
  .then(response => {
    this.priorityOptions = response.data.data.length
     ? response.data.data
     : []
  })

  .catch(error => { console.error(error)})
},
methods: {
  refreshTasks () {
    axios
      .get('/todos', this.axiosOptions)
      .then(response => { this.taskList = response.data.data })
      .catch(error => { this.handleError(error) })
  },
  addTask (task) {
    axios
      .post('/todos', task, this.axiosOptions)
      .then(response => { this.taskList.push(response.data.data) })
      .catch(error => { this.handleError(error) })
  },
  toggleDone (task) {
    task.isComplete = !task.isComplete
    axios
      .put(`/todos/${task.id}`, task, this.axiosOptions)
      .catch(error => { this.handleError(error) })
  },
  removeTask (task) {
    axios
      .delete(`/todos/${task.id}`, this.axiosOptions)
      .then(response => {
        const taskIndex = this.taskList.findIndex(t => t.id === task.id)
        this.taskList.splice(taskIndex, 1)
      })
      .catch(error => { this.handleError(error) })
  },
  handleError(error) {
    console.log(error)
    // obviously we want to do something more robust
    // including notifying the user somehow.
  },
  saveApiTokens (apiTokens) {
  this.api.accessToken = apiTokens.access_token
  this.api.expiresAt = apiTokens.expires_at
  localStorage.setItem('todoApiTokens', JSON.stringify(apiTokens))
  this.refreshTasks()
},
saveTaskList () {
        localStorage.setItem('taskList', JSON.stringify(this.taskList))
},
loadInitialData () {
  const apiTokens = JSON.parse(localStorage.getItem('todoApiTokens'))
  if (apiTokens) {
    this.api.accessToken = apiTokens.access_token
    this.api.expiresAt = apiTokens.expires_at
    if (this.isLoggedIn) this.refreshTasks()
  }
},
created () {
  this.loadInitialData()
}, 
logout () {
  this.api.accessToken = null
  this.api.expiresAt = null
  localStorage.removeItem('todoApiTokens')
}
},

computed: {
    isLoggedIn () {
      return this.api.accessToken && moment(this.api.expiresAt).isAfter()
    },
    axiosOptions () {
  return {
    baseURL: this.baseURL,
    headers: { 'Authorization' : `Bearer ${this.api.accessToken}`}
  }
}
},
}
</script>

<style>

* {
  box-sizing: border-box;
}

.container {
  max-width: 600px;
  margin: 0 auto;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
  margin-top: 60px;
  font-size: 24px;
}

.logo {
  display: block;
  margin: 20px auto;
  height: 75px;
}
</style>
