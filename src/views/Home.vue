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
      <button @click="removeCompleted()">Clear Completed Tasks</button>
    </div>
    <div>
      Filter By: <input v-model="taskFilter" list="tasks">
      <datalist id="tasks">
       <option v-for="task in tasks">{{ task.text }}</option>
     </datalist>
    </div>
    
    <div>
      <button @click="setSortOrder()">Sort {{ sortIndicator }} </button>
    </div>
    
    <ul>
      <transition-group name="fade">
        <div v-for="task in orderBy(filterBy(tasks, taskFilter, 'text', 'completed'), 'text' ,sortOrder)" v-bind:class="{strike: task.completed}" v-bind:key="task.id">
          <li @click="completeTask(task)">{{ task.text }}</li>
        </div>  
      </transition-group>
    </ul>

  </div>
</template>

<style>
.strike {
  text-decoration: line-through;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity .9s
}
.fade-enter, .fade-leave-to {
  opacity: 0
}

/* Vue.js slide-right */
.slide-right-enter-active {
  transition: all .3s ease;
}
.slide-right-leave-active {
  transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-right-enter, .slide-right-leave-to {
  transform: translateX(10px);
  opacity: 0;
}

/* Vue.js slide-left */
.slide-left-enter-active {
  transition: all .3s ease;
}
.slide-left-leave-active {
  transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-left-enter, .slide-left-leave-to {
  transform: translateX(-10px);
  opacity: 0;
}
</style>

<script>
var axios = require('axios');

export default {
  data: function() {
    return {
      tasks: [],
      newTask: {text: '', completed: false},
      taskFilter: "",
      sortOrder: 1,
      sortIndicator: '▲',
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
      var params = {
                   text: this.newTask.text,
                   completed: this.newTask.completed
                   };
      axios
      .post("http://localhost:3000/api/tasks", params)
      .then(function(response) {
        this.tasks.push(response.data);        
      }.bind(this));

      this.newTask = {text: "", completed: false};
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
    removeCompleted: function() {
      var incompleteTasks = [];
      for(var i = 0; i < this.tasks.length; i++) {
        var task = this.tasks[i];
        if (!task.completed) {
          incompleteTasks.push(task);
        }
      }
      this.tasks = incompleteTasks;
    },
    setSortOrder: function () {
      this.sortOrder = this.sortOrder * -1;
      if (this.sortOrder === 1) {
        this.sortIndicator = '▲';
      } else {
        this.sortIndicator = '▼';
      }
    }
  },
  computed: {}
};
</script>










