<template>
  <div id="selected">
    <div class="organism"
         v-for="organism in selected"
         v-bind:key="organism.id"
    >
      {{ organism.nameRussian }}
      <hr>
      <div
          v-for="(number, region) in organism.region"
          v-bind:key="region.id"
      >
        <p>{{ region }} {{ number }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "app-selected",
  data() {
    return {
      selected: [
        {
          "id": 1,
          "nameRussian": "Гельвелла ямчатая (г. бороздчатая, лопастник ямчатый)",
          "nameLatin": "Helvella lacunosa Afzel.",
          "region": {
            "Ненецкий автономный округ": 3
          }
        },
        {
          "id": 2,
          "nameRussian": "Паутинник фиолетовый",
          "nameLatin": "Cortinarius violaceus (L.) Gray",
          "region": {
            "Мурманская область": 3,
            "Республика Карелия": 3,
            "Ненецкий автономный округ": 3
          }
        }
      ]
    }
  }
}
</script>

<style scoped>
.organism {
  background: #B0DF93;
  border-radius: 10px;
  margin-bottom: 10px;
  padding: 20px 10px;
}

hr {
  border: none;
  color: black;
  background-color: black;
  height: 1px;
}

p {
  font-size: 12px;
}
</style>