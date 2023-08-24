<template>
    <div class="home">
      <EditTask />
    </div>
  </template>
  
  <script>
  // @ is an alias to /src
  import EditTask from "@/components/EditTask.vue";
  
  export default {
    name: "EditTaskView",
    components: {
      EditTask,
    },
  };
  </script>