<template>

  <div v-on:click="showOrganisms" class="kingdom">
    {{ kingdom.nameRussian }}
  </div>
  <div
      v-for="organism in organisms"
      v-bind:key="organism.id"
      class="organism">
    {{ organism.nameRussian }}
    <hr>
  </div>

</template>

<script>
export default {
  name: "KingdomV",

  data() {
    return {
      isOpen: false,
      organisms: []
    }
  },

  props: [
    "kingdom"
  ],

  methods: {
    async showOrganisms() {
      if (this.isOpen) {
        this.organisms = []
      } else {
        const response = await fetch(`http://localhost:8080/api/v1/kingdoms/${this.kingdom.id}/organisms`);
        this.organisms = await response.json()
      }
      this.isOpen = !this.isOpen
    }
  }
}
</script>

<style scoped>
div {
  border-radius: 10px;
}

.kingdom {
  background: #D6AB78;
  padding: 10px 20px;
  margin-bottom: 20px;
  margin-top: 20px;
}

.organism {
  background: #F5DDAA;
  padding: 15px 20px;
  margin-bottom: 10px;
}

hr {
  border: none;
  color: black;
  background-color: black;
  height: 1px;
}
</style>