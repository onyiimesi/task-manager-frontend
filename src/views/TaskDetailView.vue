<template>
  <div class="home">
    <TaskDetail />
  </div>
</template>

<script>
// @ is an alias to /src
import TaskDetail from "@/components/TaskDetail.vue";

export default {
  name: "TaskDetailView",
  components: {
    TaskDetail,
  },
};
</script>
