<template>
  <div id="grid">
    <app-map></app-map>
    <app-menu></app-menu>
  </div>
</template>

<script>
import AppMenu from "@/components/app-menu";
import AppMap from "@/components/app-map";

export default {
  name: 'App',
  components: {
    AppMap,
    AppMenu
  }
}
</script>

<style scoped>
  #grid {
    display: grid;
    grid-template-columns: 2fr 1fr;
    height: 100%;
  }
</style>
