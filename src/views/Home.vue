<template>
  <div v-if="showAddTask">
    <AddTask @add-task="addTask" />
  </div>

  <!-- <Tasks @deleteTask="deleteTask" :tasks="tasks"/> -->
  <Tasks
    @delete-task="deleteTask"
    @toggle-reminder="toggleReminder"
    :tasks="tasks"
  />
</template>

<script>
import AddTask from "../components/AddTask.vue";
import Tasks from "../components/Tasks.vue";

export default {
  name: "Home",
  components: {
    AddTask,
    Tasks,
  },
  data() {
    return {
      tasks: [],
    };
  },
  props: {
    showAddTask: Boolean,
  },
  methods: {
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
