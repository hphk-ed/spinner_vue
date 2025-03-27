<template>
  <div class="app">
    <WheelSpinner />
  </div>
</template>

<script>
import WheelSpinner from './components/WheelSpinner.vue'

export default {
  name: 'App',
  components: {
    WheelSpinner
  }
}
</script>

<style>
body {
  font-family: 'Noto Sans KR', sans-serif;
  margin: 0;
  padding: 0;
  background-color: #f5f5f5;
}

.app {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}
</style>
