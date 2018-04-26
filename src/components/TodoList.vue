<template>
    <div class="largeContainer">
        <h1>Josh's To-Do App</h1>
        <input type="text" class="todo-input" placeholder="Add An Item" v-model="newTodo" @keyup.enter="addTodo">
        
        <div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo-item">
            <div class="todo-item-left">
                <input type="checkbox" v-model="todo.completed">
                <div v-if="!todo.editing" @dblclick="editTodo(todo)" class="todo-item-label" :class="{ completed : todo.completed }">{{ todo.title }}</div>
                <input v-else class="todo-item-edit" type="text" v-model="todo.title" @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)" v-focus>
            </div>
            <div class="remove-item" @click="removeTodo(index)">
                <i class="fas fa-trash-alt"></i>
            </div>
        </div>

        <div class="extra-container">
            <div>
                <label><input class="checkbox" type="checkbox" :checked="!anyRemaining" @change="checkAllTodos"> Check All</label>
            </div>
            <div>
                {{ remaining }} items left
            </div>
        </div>

        <div class="extra-container">
            <div>
                <button :class="{ active: filter == 'all' }" @click="filter = 'all'">All</button>
                <button :class="{ active: filter == 'active' }" @click="filter = 'active'">Active</button>
                <button :class="{ active: filter == 'completed' }" @click="filter = 'completed'">Completed</button>
            </div>

            <div>
                <button v-if="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>
            </div>
        </div>

        <div class="extra-container">
            <h3>Double Click To Edit</h3>
        </div>
    </div>
</template>

<script>
export default {
  name: 'todo-list',
  data () {
    return {
      newTodo: '',
      idForTodo: 3,
      beforeEditCache: '',
      filter: 'all',
      todos: [
        {
            'id': 1,
            'title': 'Finish Vue Todo List',
            'completed': false,
            'editing': false,
        },
        {
            'id': 2,
            'title': 'Mow The Lawn',
            'completed': false,
            'editing': false,
        },
      ]
    }
  },
  computed: {
      remaining() {
          return this.todos.filter(todo => !todo.completed).length
      },
      anyRemaining() {
          return this.remaining != 0
      },
      todosFiltered() {
          if (this.filter == 'all') {
              return this.todos
          } else if (this.filter == 'active') {
              return this.todos.filter(todo => !todo.completed)
          } else if (this.filter == 'completed') {
              return this.todos.filter(todo => todo.completed)
          }

          return this.todos
      },
      showClearCompletedButton() {
          return this.todos.filter(todo => todo.completed).length > 0
      }
  },
  directives: {
      focus: {
          inserted: function (el) {
              el.focus()
          }
      }
  },
  methods: {
      addTodo() {
          if(this.newTodo.trim().length == 0) {
              return
          }

          this.todos.push({
              id: this.idForTodo,
              title: this.newTodo,
              completed: false,
          })

          this.newTodo = ''
          this.idForTodo++
      },
      editTodo(todo) {
          this.beforeEditCache = todo.title
          todo.editing = true
      },
      doneEdit(todo) {
          if(todo.title.trim().length == 0) {
              todo.title = this.beforeEditCache
          }
          todo.editing = false
      },
      cancelEdit(todo) {
          todo.title = this.beforeEditCache
          todo.editing = false
      },
      removeTodo(index) {
          this.todos.splice(index, 1)
      },
      checkAllTodos() {
          this.todos.forEach((todo) => todo.completed = event.target.checked)
      },
      clearCompleted() {
          this.todos = this.todos.filter(todo => !todo.completed)
      },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
    body {
        background-color: #FEFBFB;
    }
    .largeContainer {
        border-top: 3px solid #FE3562;
        border-right: 7px solid #FE3562;
        border-bottom: 7px solid #FE3562;
        border-left: 3px solid #FE3562;
        text-align: center;
        padding: 70px;
    }
    .todo-input {
        width: 100%;
        padding: 10px 18px;
        font-size: 18px;
        margin-bottom: 16px;

        &:focus {
            outline: 0;
        }
    }

    .todo-item {
        margin-bottom: 12px;
        display: flex;
        align-items: center;
        justify-content: space-between;
    }

    .remove-item {
        cursor: pointer;
        margin-left: 14px;
        &:hover {
            color: black;
        }
    }

    .todo-item-left {
        display: flex;
        align-items: center;
    }

    .todo-item-label {
        padding: 10px;
        border: 1px solid white;
        margin-left: 12px;
    }

    .todo-item-edit {
        font-size: 24px;
        color: #433D3E;
        margin-left: 12px;
        width: 100%;
        padding: 10px;
        border: 1px solid #ccc;
        font-family: 'Avenir', Arial, Helvetica, sans-serif;

        &:focus {
            outline: none;
        }
    }

    .completed {
        text-decoration: line-through;
        color: grey;
    }

    .extra-container {
        display: flex;
        align-items: center;
        justify-content: space-between;
        font-size: 16px;
        border-top: 1px solid lightgrey;
        padding-top: 14px;
        margin-bottom: 14px;
    }

    button {
        font-size: 14px;
        background-color: #433D3E;
        color: #FEFBFB;
        appearance: none;
        border: none;
        padding: 10px 22px;
        text-align: center;
        text-decoration: none;
        display: inline-block;
        font-size: 16px;

        &:hover {
            background: #FE3562;
            cursor: pointer;
        }

        &:focus {
            outline: none;
        }
    }

    .active {
        background: #FE3562;
    }

</style>
