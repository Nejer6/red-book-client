<template>
  <div style="padding: 40px 30px">
    <div v-if="!isEmptySelected">
      <div>
        <p>Выбранные: </p>
        <button/>
      </div>
      <app-organism v-for="organism in selectedOrganisms"
                    :key="organism.id"
                    :organism="organism"
                    @click="removeSelect(organism)"/>
    </div>

    <!--Поисковик-->
    <div>
      <input v-model="search" v-on:keyup.enter="getOrganisms">
      <button @click="getOrganisms">Искать</button>
    </div>

    <div>
      <p style="font-size: 17pt; margin: 20px 0"><b>Все: </b></p>
    </div>
    <div v-for="organism in allOrganisms"
         :key="organism.id">
      <app-organism :organism="organism"
                    @click="selectOrganism(organism)"/>
    </div>

    <div>
      Страница: {{ page }}
      <button @click="page--" v-if="page > 1">Назад</button>
      <button @click="page++" v-if="hasNextPage">Вперед</button>
    </div>

  </div>
</template>

<script>
import AppOrganism from "@/components/app-organism";

export default {
  name: "app-menu",

  emits: [
      "pushOrganismId",
      "removeOrganism"
  ],

  data() {
    return {
      selectedOrganisms: [],
      allOrganisms: [{
        "id": 14,
        "name": "Somateria mollissima Linnaeus, 1758",
        "nameRu": "Обыкновенная гага",
        "rare": "5"
      }],
      search: "",
      region: "",
      page: 1,
      hasNextPage: false
    }
  },

  components: {AppOrganism},

  mounted() {
    this.getOrganisms()
  },

  computed: {
    isEmptySelected() {
      return this.selectedOrganisms.length === 0
    }
  },

  methods: {
    selectOrganism(organism) {
      let select = false

      this.selectedOrganisms.forEach(item => {
        if (item.id === organism.id) select = true
      })

      if (!select) {
        this.selectedOrganisms.push(organism)
        this.$emit("pushOrganismId", organism.id)
      }
    },

    removeSelect(organism) {
      this.selectedOrganisms = this.selectedOrganisms.filter(item => item.id !== organism.id)
      this.$emit("removeOrganism", organism.id)
    },

    async getOrganisms() {
      const count = 10;
      const offset = (this.page - 1) * count
      const response = await fetch(`http://localhost:8080/api/v1/animals?offset=${offset}&count=${count + 1}&search=${this.search}`);
      const result = await response.json()

      if (result.length > count) {
        result.pop()
        this.hasNextPage = true
      } else {
        this.hasNextPage = false
      }

      this.allOrganisms = result
    }
  },

  watch: {
    page() {
      this.getOrganisms()
    }
  }
}
</script>

<style scoped>

</style>