<script setup>
  import Layout from "./components/layouts/Layout.vue";
  import Dashboard from "./components/pages/Dashboard.vue";
  import Welcome from "./components/pages/Welcome.vue";
  import Workout from "./components/pages/Workout.vue";
</script>

<template>
  <Layout>
    <Welcome />
    <Dashboard />
    <Workout />
  </Layout>
</template>

<style scoped></style>
