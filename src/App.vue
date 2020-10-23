<template>
  <div id="app">
    <h1>CS3219 OTOT Task Manager</h1>
    <b-overlay :show="loading">
    <b-card class="text-center main-body">
      <b-alert :show="errored" variant="danger" dismissible>Something went wrong. Please try again later.</b-alert>
        <b-container style="padding: 10px;">
          <Todo v-for="todo in todos" :key="todo._id" :todo="todo" v-on:api-error="errored = true" v-on:delete-task="removeTask($event)"></Todo>
        </b-container>
        <b-container>
          <b-row>
            <b-col style="margin-bottom: 10px;">
            <b-form-input type="text" v-model="newTask"></b-form-input>
            </b-col>
            <b-col md="auto">
              <b-button variant="primary" v-on:click="addTask">
              <b-icon icon="pencil-square"></b-icon> Add Task
              </b-button>
            </b-col>
        </b-row>
        </b-container>
    </b-card>
    </b-overlay>
  </div>
</template>

<script>
import axios from 'axios'
import Todo from './Todo.vue'

export default {
  name: 'app',
  data () {
    return {
      baseUrl: 'https://cs3219-a0121742r-taskb.herokuapp.com/api/todo',
      loading: true,
      errored: false,
      newTask: "",
      todos: []
    }
  },
  created() {
    this.getAllTasks()
  },
  methods: {
    getAllTasks: function() {
        axios
         .get(this.baseUrl)
         .then(res => {
           this.todos = res.data.data
          })
         .catch(err => {
           console.log(err)
           this.errored = true
         })
         .finally(() => this.loading = false)
    },
    addTask: function() {
      if (this.newTask !== "") {
        axios
         .post(this.baseUrl, { description: this.newTask })
         .then(res => {
            this.todos.push({ "_id": res.data.data._id, "description": this.newTask, "status": false })
            this.newTask = ""
         })
         .catch(err => {
           console.log(err)
           this.errored = true
         })
      }
    },
    removeTask: function(id) {
        axios
         .delete(this.baseUrl + "/" + id)
         .then(res => {
            this.todos = this.todos.filter(x => x._id !== id)
         })
         .catch(err => {
            console.log(err)
            this.errored = true
         })
    }
  },
  components: {
    Todo
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.main-body {
  margin: 0 auto; 
  margin-bottom: 40px; 
  max-width: 80%;
}

h1 {
  font-weight: normal;
  margin-bottom: 40px;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
