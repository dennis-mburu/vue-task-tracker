<script>
import Task from "./Task.vue"
    export default {
        name: "Tasks",
        props: {
            tasks: Array
        },
        components: {
            Task
        }
    }
</script>

<template>
    <div v-for="task in tasks">
        <Task :task="task" />
    </div>
</template>