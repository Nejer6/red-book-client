<template>
  <div class="grid">
    <app-map :features="features"/>
    <app-menu v-on:pushOrganismId="getFeature"/>
  </div>
</template>

<script>
import AppMap from "@/components/app-map";
import AppMenu from "@/components/app-menu";

export default {
  name: 'main-page',

  data() {
    return {
      features: []
    }
  },

  methods: {
    async getFeature(id) {
      const response = await fetch(`http://localhost:8080/api/v1/animals/${id}`)
      this.features = await response.json()
    }
  },

  components: {
    AppMenu,
    AppMap
  }
}
</script>

<style scoped>
div.grid {
  display: grid;
  grid-template-columns: 2fr 1fr;
}
</style>
