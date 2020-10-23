<template>
    <b-row align-v="center" style="margin-bottom: 10px;">
        <b-col md="auto" style="margin-bottom: 10px;">
        <b-button :variant="checkboxColor" v-on:click="toggleTask">
            <b-icon :icon="checkboxIcon"></b-icon>
        </b-button>
        </b-col>
        <b-col style="text-align: left; margin-bottom: 10px;">
            <b-form-input style="padding:5px;" :plaintext="!editMode" size="md" v-model="todo.description" :style="isStriked"></b-form-input>
        </b-col>
        <b-col md="auto" style="margin-bottom: 10px;">
        <b-button-group size="md">
            <b-button v-if="!editMode" variant="warning" v-on:click="editMode = true">
                  <b-icon icon="pencil-fill"></b-icon> Edit
             </b-button>
             <b-button v-else variant="info" v-on:click="editTask">
                  <b-icon icon="check"></b-icon> Save
             </b-button>
            <b-button variant="danger" v-on:click="$emit('delete-task', todo._id)">
                 <b-icon icon="trash-fill"></b-icon> Delete
            </b-button>
        </b-button-group>
        </b-col>
    </b-row>
</template>

<script>
import axios from 'axios'
export default {
    name: 'to-do',
    props: ['todo'],
    data() {
        return {
            editMode: false,
            baseUrl: 'https://cs3219-a0121742r-taskb.herokuapp.com/api/todo'
        }
    },
    computed: {
        checkboxColor: function() {
            if (this.todo.status) return "success"
            else return "secondary"
        },
        checkboxIcon: function() {
            if (this.todo.status) return "check"
            else return "three-dots"
        },
        isStriked: function() {
            if (this.todo.status && !this.editMode) {
                return { 'text-decoration': 'line-through' }
            } else {
              return { }
            }
        }
    },
    methods: {
        editTask: function() {
            axios
            .put(this.baseUrl + "/" + this.todo._id, 
                { description: this.todo.description })
            .then(res => {
                this.editMode = false
            })
            .catch(err => {
                this.$emit('api-error')
                console.log(err)
            })
        },
        toggleTask: function() {
            axios
            .put(this.baseUrl + "/" + this.todo._id, 
                { status: !this.todo.status })
            .then(res => {
                this.todo.status = !this.todo.status
            })
            .catch(err => {
                this.$emit('api-error')
                console.log(err)
            })
        }
    }
}
</script>

<style lang="scss">
input {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

h1, h2 {
  font-weight: normal;
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
