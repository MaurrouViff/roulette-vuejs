<template>
  <div id="app">
    <RandomStudent />
    <RandomStudentAbsent />
  </div>
</template>

<script>
import RandomStudent from "./components/RandomStudent.vue";
import RandomStudentAbsent from "@/components/RandomStudentAbsent.vue";

export default {
  components: {
    RandomStudentAbsent,
    RandomStudent
  }
};
</script>
