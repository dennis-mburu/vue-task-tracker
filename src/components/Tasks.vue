<template>
  <div v-for="task in tasks">
    <!-- <Task @deleteTask="deleteTask" :task="task" /> -->
    <Task @delete-task="$emit('delete-task', task.id)" :task="task" />
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
  emits: ['delete-Task'],
  methods: {
    // deleteTask(id) {
    //   this.$emit("deleteTask", id);
    // },
  },
};
</script>
