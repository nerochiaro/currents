<script setup lang="ts">
import "cesium/Build/Cesium/Widgets/widgets.css";
import { setupCesium } from './setup-cesium';
import { onMounted } from 'vue';

declare global {
  interface Window { CESIUM_BASE_URL: string; }
}

window.CESIUM_BASE_URL = 'static/cesium';


onMounted(() => {
  setupCesium('cesium');
});
</script>

<template>
  <div id="cesium" />
</template>

<style scoped>
body {
  margin: 0px;
}
#cesium {
  margin: 0px;
  width: 100vw;
  height: 100vh;
}
</style>
