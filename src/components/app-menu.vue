<template>
  <div style="height: 100%; overflow-y: scroll">
    <div style="padding: 40px 30px; height: 100%">
      <div>
        <div v-if="!isEmptySelected">
          <div>
            <p class="label"><b>Выбранные: </b></p>
            <button/>
          </div>
          <app-organism v-for="organism in selectedOrganisms"
                        :key="organism.id"
                        :organism="organism"
                        @click="removeSelect(organism)"
                        style="background-color: #B0DF93"/>
        </div>

        <!--Поисковик-->
        <div style="display: flex; justify-content: space-around">
          <input v-model="search" v-on:keyup.enter="searchOrganisms" style="width: 85%">
          <button @click="searchOrganisms" class="button" style="margin-left: 10px">Искать</button>
        </div>

        <div style="display: flex; justify-content: space-evenly; margin-top: 10px">
          <div>
            <p>Царство</p>
            <select class="button" v-model="kingdom" style="font-size: 12pt">
              <option>Любое</option>
              <option>Грибы</option>
              <option>Животные</option>
              <option>Растения</option>
            </select>
          </div>

          <div>
            <p>Редкость</p>
            <select class="button" v-model="sort" style="font-size: 12pt">
              <option>По убыванию</option>
              <option>По возрастанию</option>
            </select>
          </div>
        </div>

        <div>
          <p class="label"><b>Все: </b></p>
        </div>
        <div v-for="organism in allOrganisms"
             :key="organism.id">
          <app-organism :organism="organism"
                        style="background-color: #F5DDAA"
                        @click="selectOrganism(organism)"/>
        </div>
      </div>

      <div style="display: flex; justify-content: space-around">
        <button class="button" @click="page--" v-if="page > 1">Назад</button>
        <p style="margin: 0 5px; font-size: 15pt">Страница: {{ page }}</p>
        <button class="button" @click="page++" v-if="hasNextPage">Вперед</button>
      </div>

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
        "kingdom": "Животные",
        "rare": "5"
      }],
      search: "",
      region: "",
      page: 1,
      hasNextPage: false,
      kingdom: "Любое",
      sort: "По убыванию",
    }
  },

  components: {AppOrganism},

  mounted() {
    this.getOrganisms()
  },

  computed: {
    isEmptySelected() {
      return this.selectedOrganisms.length === 0
    },

    sortType() {
      return this.sort === "По убыванию" ? "ASC" : "DESK"
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
      let url = `http://localhost:8080/api/v1/animals?offset=${offset}&count=${count + 1}&search=${this.search}&sort=${this.sortType}`
      if (this.kingdom !== "Любое") {
        url += `&kingdom=${this.kingdom}`
      }
      const response = await fetch(url);
      const result = await response.json()

      if (result.length > count) {
        result.pop()
        this.hasNextPage = true
      } else {
        this.hasNextPage = false
      }

      this.allOrganisms = result
    },

    searchOrganisms() {
      this.page = 1
      this.getOrganisms()
    }
  },

  watch: {
    page() {
      this.getOrganisms()
    },

    kingdom() {
      this.searchOrganisms()
    },

    sort() {
      this.searchOrganisms()
    }
  }
}
</script>

<style scoped>
.button {
  padding: 3px;
  background: #D6AB78;
  border: 0;
  border-radius: 3px;
  font-size: 15pt;
}

p.label {
  font-size: 17pt;
  margin: 20px 0
}
</style>