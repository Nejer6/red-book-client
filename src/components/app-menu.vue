<template>
  <div style="height: 100%; overflow-y: scroll">
    <div style="padding: 40px 30px; height: 100%">
      <div>
        <!--Поисковик-->
        <div style="display: flex; justify-content: space-around">
          <input v-model="search" v-on:keyup.enter="searchOrganisms" style="width: 85%">
          <button @click="searchOrganisms" class="button" style="margin-left: 10px">Искать</button>
        </div>

        <div>
          <div style="display: flex; justify-content: space-evenly; margin-top: 10px">
            <div>
              <p>Царство</p>
              <select class="button" v-model="kingdom" style="font-size: 12pt">
                <option>Любое</option>
                <option>Грибы</option>
                <option>Животные</option>
                <option>Растения</option>
                <option>Лишайники</option>
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

          <div style="display: flex; justify-content: space-around">
            <div>
              <div v-if="kingdom === 'Животные'">
                <p>Таксон</p>
                <select class="button" style="font-size: 12pt" v-model="animalClass">
                  <option>Любой</option>
                  <option>Птицы</option>
                  <option>Рыбы</option>
                  <option>Насекомые</option>
                  <option>Млекопитающие</option>
                  <option>Пресмыкающиеся</option>
                  <option>Беспозвоночные (без членистоногих)</option>
                  <option>Земноводные</option>
                  <option>Членистоногие (без насекомых)</option>
                </select>
              </div>
              <div v-if="kingdom === 'Растения'">
                <p>Таксон</p>
                <select class="button" style="font-size: 12pt" v-model="animalClass">
                  <option>Любой</option>
                  <option>Покрытосеменные</option>
                  <option>Мхи</option>
                  <option>Водоросли</option>
                  <option>Папоротники, хвощи, плауновидные</option>
                  <option>Цианобактерии</option>
                  <option>Голосеменные</option>
                </select>
              </div>

              <p>Регион</p>
              <select class="button" style="font-size: 12pt" v-model="region">
                <option>Любой</option>
                <option>Архангельская область</option>
                <option>Мурманская область</option>
                <option>Республика Карелия</option>
                <option>Республика Коми</option>
                <option>Ненецкий автономный округ</option>
                <option>Ямало-Ненецкий автономный округ</option>
                <option>Красноярский край</option>
                <option>Республика Саха</option>
                <option>Чукотский автономный округ</option>
              </select>
            </div>
          </div>

          <!--Отображение арктики-->
          <div style="display: flex; justify-content: space-around">
            <div style="margin-top: 5px">
              <input type="checkbox" style="height: 1.5em; width: 1.5em; margin-right: 5px"  v-model="showArcticTerritory">
              <label style="font-size: 12pt">отображение границ Арктической зоны</label>
            </div>
          </div>

          <!--Скрыть|Показать-->
          <div style="display: flex; justify-content: space-around">
            <button class="button" style="font-size: 12pt" @click="getAllOrganisms">Отобразить все найденные</button>
            <button class="button" style="font-size: 12pt" v-if="!isEmptySelected" @click="clearSelected">Скрыть все</button>
          </div>

        </div>

        <div>
          <p class="label"><b>Все: </b></p>
        </div>
        <div v-for="organism in allOrganisms"
             :key="organism.id">
          <app-organism :organism="organism"
                       :class="{ selected: idsSelected.includes(organism.id) }"
                        @click="selectOrganism(organism)"/>
        </div>
      </div>

      <div style="display: flex; justify-content: space-around">
        <button class="button" @click="page--" v-if="page > 1">Назад</button>
        <p style="margin: 0 5px; font-size: 15pt">Страница: {{ page }}</p>
        <button class="button" @click="page++" v-if="hasNextPage">Вперед</button>
      </div>

      <!--Выбранные-->
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
        "rare": "5",
        "animalClass": null
      }],
      search: "",
      region: "Любой",
      page: 1,
      hasNextPage: false,
      kingdom: "Любое",
      animalClass: "Любой",
      sort: "По убыванию",
      idsSelected: [],
      showArcticTerritory: true
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
    clearSelected() {
      this.selectedOrganisms.forEach(selected => {
        this.removeSelect(selected)
      })
    },

    selectOrganism(organism) {
      let select = false

      this.selectedOrganisms.forEach(item => {
        if (item.id === organism.id) select = true
      })

      if (!select) {
        this.idsSelected.push(organism.id)
        this.selectedOrganisms.push(organism)
        this.$emit("pushOrganismId", organism.id)
      } else {
        this.removeSelect(organism)
      }
    },

    removeSelect(organism) {
      this.idsSelected = this.idsSelected.filter(item => item !== organism.id)
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
      if (this.region !== "Любой") {
        url += `&region=${this.region}`
      }
      if (this.animalClass !== "Любой") {
        url += `&animalClass=${this.animalClass}`
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

    async getAllOrganisms() {
      let url = `http://localhost:8080/api/v1/animals?search=${this.search}`
      if (this.kingdom !== "Любое") {
        url += `&kingdom=${this.kingdom}`
      }
      if (this.region !== "Любой") {
        url += `&region=${this.region}`
      }
      if (this.animalClass !== "Любой") {
        url += `&animalClass=${this.animalClass}`
      }
      if (this.sortType === "ASC") {
        url += `&sort=DESK}`
      } else {
        url += `&sort=ASC`
      }
      const response = await fetch(url);
      const result = await response.json()

      result.forEach(organism => this.selectOrganism(organism))
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
      this.animalClass = "Любой"
      this.searchOrganisms()
    },

    animalClass() {
      this.searchOrganisms()
    },

    sort() {
      this.searchOrganisms()
    },

    region() {
      this.searchOrganisms()
    },

    showArcticTerritory() {
      this.$emit("showArcticTerritory", this.showArcticTerritory)
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

.selected {
  background-color: #B0DF93
}
</style>