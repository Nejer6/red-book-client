<template>
  <div class="organism" style="margin-bottom: 15px; padding: 5px; border-radius: 5px">
    <p style="font-size: 15pt"><b>{{ organism.nameRu }}</b></p>
    <hr>
    <p>{{ organism.name }}</p>
    <p>Царство: {{ organism.kingdom }}</p>
    <p>Редкость: {{ organism.rare }}</p>
    <p>Регион: {{ organism.region }}</p>
  </div>
</template>

<script>
export default {
  name: "app-organism",

  props: ["organism"]
}
</script>

<style scoped>
hr {
  display: block;
  height: 1px;
  border: 0;
  border-top: 1px solid #000;
  margin: 10px 0;
  padding: 0;
}

.organism p {
  margin-bottom: 5px;
}

div {
  background-color: #F5DDAA
}
</style>