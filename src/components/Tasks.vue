<template>
  <div v-for="task in tasks">
    <!-- <Task @deleteTask="deleteTask" :task="task" /> -->
    <Task @toggle-reminder="$emit('toggle-reminder', task.id)" @delete-task="$emit('delete-task', task.id)" :task="task" />
  </div>
</template>

<script>
import Task from "./Task.vue";
export default {
  name: "Tasks",
  props: {
    tasks: Array,
  },
  components: {
    Task,
  },
  emits: ['delete-task', 'toggle-reminder']
  // methods: {
  //   deleteTask(id) {
  //     this.$emit("deleteTask", id);
  //   },
  // },
};
</script>
