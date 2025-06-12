<script setup lang="ts">
import Cubo3D from './components/Cubo3D.vue'
import Piramide3D from './components/Piramide3D.vue'
</script>

<template>
  <div class="container">
    <div class="figure">
      <Cubo3D />
    </div>
    <div class="figure">
      <Piramide3D />
    </div>
  </div>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  overflow: hidden;
}
</style>

<style scoped>
.container {
  display: flex;
  width: 100%;
  height: 100vh;
}

.figure {
  flex: 1;
  height: 100%;
}
</style>
