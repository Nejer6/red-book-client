<template>
  <div>
    <app-map/>
    <app-menu/>
  </div>
</template>

<script>
import AppMap from "@/components/app-map";
import AppMenu from "@/components/app-menu";

export default {
  name: 'main-page',

  components: {
    AppMenu,
    AppMap
  }
}
</script>

<style>

</style>
