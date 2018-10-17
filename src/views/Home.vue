<template>
  <div class="home">
    <h1>Todo App</h1>
    <div>
      <h3>New Task</h3>
      <div>
        <input v-model="newTask.text">
        <button v-on:click="addTask()">Add Task</button>
      </div>
    </div>
    <div>
      <h5>{{ numberOfIncompleteTasks() }} tasks left</h5>
    </div>
    <ul>
      <div v-for="task in tasks" v-bind:class="{strike: task.completed}">
        <li @click="completeTask(task)">{{ task.text }}</li>
      </div>  
    </ul>
  </div>
</template>

<style>
.strike {
  text-decoration: line-through;
}
</style>

<script>
var axios = require('axios');

export default {
  data: function() {
    return {
      tasks: [],
      newTask: {text: '', completed: false}
    };
  },
  created: function() {
    axios
    .get("http://localhost:3000/api/tasks")
    .then(function(response) {
      this.tasks = response.data;
    }.bind(this));
  },
  methods: {
    addTask: function() {
      if (this.newTask.text !== "") {
        this.tasks.push(this.newTask);
        this.newTask = {text: "", completed: false};
      }
    },
    completeTask: function(inputTask) {
      inputTask.completed = !inputTask.completed;
    },
    numberOfIncompleteTasks: function() {
      var count = 0;
      this.tasks.forEach(function(task) {
        if (!task.completed) {
          count++;
        }
      });
      return count;
    },
    removeCompelete: function() {

    }
  },
  computed: {}
};
</script>










