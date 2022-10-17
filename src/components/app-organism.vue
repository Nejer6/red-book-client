<template>
  <div>
    <p>{{ organism.nameRu }}</p>
    <p>{{ organism.name }}</p>
    <p>Редкость: {{ organism.rare }}</p>
  </div>
</template>

<script>
export default {
  name: "app-organism",

  props: ["organism"]
}
</script>

<style scoped>

</style>