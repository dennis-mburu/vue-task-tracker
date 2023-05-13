<template>
  <div class="container">
    <Header
      :showAddTask="showAddTask"
      @on-click="toggleAddTask"
      title="Task Tracker"
    />
    <div v-if="showAddTask">
      <AddTask @add-task="addTask" />
    </div>

    <!-- <Tasks @deleteTask="deleteTask" :tasks="tasks"/> -->
    <Tasks
      @delete-task="deleteTask"
      @toggle-reminder="toggleReminder"
      :tasks="tasks"
    />
  </div>
</template>

<script>
import Header from "./components/Header.vue";
import Tasks from "./components/Tasks.vue";
import AddTask from "./components/AddTask.vue";
export default {
  name: "App",
  components: {
    Header,
    Tasks,
    AddTask,
  },
  data() {
    return {
      tasks: [],
      showAddTask: false,
    };
  },
  methods: {
    toggleAddTask() {
      this.showAddTask = !this.showAddTask;
    },
    async addTask(newTask) {
      const res = await fetch("http://localhost:3000/tasks", {
        method: "POST",
        headers: {
          "content-type": "application/json",
        },
        body: JSON.stringify(newTask),
      });

      // this method also works without using async, and will
      // persist to the database and also the UI
      // .then(res => res.json())
      // .then(data => this.tasks = [...this.tasks, data])

      const data = await res.json();

      this.tasks = [...this.tasks, data];

      // before using json-server
      // this.tasks = [...this.tasks, newTask];
    },
    async deleteTask(id) {
      if (confirm("Are you sure? ")) {
        const res = await fetch(`http://localhost:3000/tasks/${id}`, {
          method: "DELETE",
        });
        // with this, the data returned is an empty object
        // .then(res => res.json())
        // .then(data => console.log(data))

        // the alert won't be shown if the server is closed, as the first fetch wont go through
        res.status === 200
          ? (this.tasks = this.tasks.filter((task) => task.id !== id))
          : alert("Error in deleting");

        // before using json server
        // this.tasks = this.tasks.filter((task) => task.id !== id);
      }
    },
    async toggleReminder(id) {
      const taskToToggle = await this.fetchTask(id);
      const updatedTask = { ...taskToToggle, reminder: !taskToToggle.reminder };

      const res = await fetch(`http://localhost:3000/tasks/${id}`, {
        method: "PATCH",
        headers: {
          "content-type": "application/json",
        },
        body: JSON.stringify(updatedTask),
      });

      const data = await res.json();

      this.tasks = this.tasks.map((task) =>
        task.id === id ? { ...task, reminder: data.reminder } : task
      );

      // before json-server
      // this.tasks = this.tasks.map((task) =>
      //   task.id === id ? { ...task, reminder: !task.reminder } : task
      // );
    },

    // This method can also work to toggle reminders
    // toggleReminder(id) {
    //   let taskToToggle = this.tasks.find((task) => task.id === id);
    //   fetch(`http://localhost:3000/tasks/${id}`, {
    //     method: "PATCH",
    //     headers: {
    //       "content-type": "application/json",
    //     },
    //     body: JSON.stringify({
    //       ...taskToToggle,
    //       reminder: !taskToToggle.reminder,
    //     }),
    //   })
    //     .then((res) => res.json())
    //     .then(
    //       (data) =>
    //         (this.tasks = this.tasks.map((task) =>
    //           task.id === data.id ? (task = data) : task
    //         ))
    //     );
    // },

    // Using json-server
    async fetchData() {
      const res = await fetch("http://localhost:3000/tasks");
      const data = await res.json();
      return data;
    },

    async fetchTask(id) {
      const res = await fetch(`http://localhost:3000/tasks/${id}`);
      const data = await res.json();
      return data;
    },
  },

  async created() {
    this.tasks = await this.fetchData();
  },

  // Before Starting to use json server
  // created() {
  //   this.tasks = [
  //     {
  //       id: "1",
  //       text: "Doctors Appointment",
  //       day: "March 5th at 2:30pm",
  //       reminder: true,
  //     },
  //     {
  //       id: "2",
  //       text: "Meeting with boss",
  //       day: "March 6th at 1:30pm",
  //       reminder: true,
  //     },
  //     {
  //       id: "3",
  //       text: "Food shopping",
  //       day: "March 7th at 2:00pm",
  //       reminder: false,
  //     },
  //   ];
  // },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap");

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: "Poppins", sans-serif;
}

.container {
  max-width: 500px;
  margin: 30px auto;
  overflow: auto;
  min-height: 300px;
  border: 1px solid steelblue;
  padding: 30px;
  border-radius: 5px;
}

.btn {
  display: inline-block;
  background: #000;
  color: #fff;
  border: none;
  padding: 10px 20px;
  margin: 5px;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 15px;
  font-family: inherit;
}

.btn:focus {
  outline: none;
}

.btn:active {
  transform: scale(0.98);
}

.btn-block {
  display: block;
  width: 100%;
}
</style>
