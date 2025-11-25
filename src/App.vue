<script setup>
  import { computed, onMounted, ref } from "vue";
  import Layout from "./components/layouts/Layout.vue";
  import Dashboard from "./components/pages/Dashboard.vue";
  import Welcome from "./components/pages/Welcome.vue";
  import Workout from "./components/pages/Workout.vue";
  import { workoutProgram } from "./utils";

  const defaultData = ref({});
  for (let workoutIdx in workoutProgram) {
    const workoutData = workoutProgram[workoutIdx];

    defaultData.value[workoutIdx] = {};

    for (let e of workoutData.workout) {
      defaultData.value[workoutIdx][e.name] = "";
    }
  }

  console.log(defaultData.value);

  const selectedDisplay = ref(1);
  const data = ref(defaultData.value);
  const selectedWorkout = ref(-1);

  const isWorkoutComplete = computed(() => {
    const currWorkout = data.value?.[selectedWorkout.value];

    if (!currWorkout) {
      return false;
    }

    const isCompleteCheck = Object.values(currWorkout).every((ex) => !!ex);
    console.log("Is Complete:", isCompleteCheck);

    return isCompleteCheck;
  });

  const firstInCompleteWorkIndex = computed(() => {
    const allWorkouts = data.value;
    if (!allWorkouts) {
      return -1;
    }

    for (const [index, workout] of Object.entries(allWorkouts)) {
      const isComplete = Object.values(workout).every((ev) => !!ev);

      if (!isComplete) {
        return parseInt(index);
      }
    }

    return -1;
  });
  const handleChangeDisplay = (idx) => {
    selectedDisplay.value = idx;
  };

  const handleSelectedWorkout = (idx) => {
    selectedDisplay.value = 3;
    selectedWorkout.value = idx;
  };

  const handleSaveWorkout = () => {
    localStorage.setItem("workouts", JSON.stringify(data.value));
    selectedDisplay.value = 2;
    selectedWorkout.value = -1;
  };

  const handleResetPlan = () => {
    selectedDisplay.value = 2;
    selectedWorkout.value = -1;
    data.value = defaultData;
    localStorage.removeItem("workouts");
  };

  onMounted(() => {
    console.log("Page has mounted");

    if (!localStorage) return;
    if (localStorage.getItem("workouts")) {
      const savedData = JSON.parse(localStorage.getItem("workouts"));

      data.value = savedData;

      selectedDisplay.value = 2;
    }
  });
</script>

<template>
  <Layout>
    <Welcome
      v-if="selectedDisplay == 1"
      :handleChangeDisplay="handleChangeDisplay"
    />
    <Dashboard
      v-if="selectedDisplay == 2"
      :handleSelectedWorkout="handleSelectedWorkout"
      :firstInCompleteWorkIndex="firstInCompleteWorkIndex"
      :handleResetPlan="handleResetPlan"
    />
    <Workout
      :isWorkoutComplete="isWorkoutComplete"
      :handleSaveWorkout="handleSaveWorkout"
      :data="data"
      :selectedWorkout="selectedWorkout"
      v-if="workoutProgram?.[selectedWorkout]"
    />
  </Layout>
</template>

<style scoped></style>
