<template>
  <div id="app">
    <h1>Device Manager</h1>
    <DeviceList />
  </div>
</template>

<script>
import DeviceList from "./components/DeviceList.vue";

export default {
  components: {
    DeviceList,
  },
};
</script>

<style scoped>
h1 {
  text-align: center;
}
</style>
