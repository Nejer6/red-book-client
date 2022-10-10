<template>
  <div>
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

    <!--Поисковик и фильтры-->
    <div>
      <input v-model="search" v-on:keyup.enter="getOrganisms">
      <button @click="getOrganisms">Искать</button>

      Регион:
      <select v-model="region">
        <option>Любая</option>
        <option>Мурманская область</option>
        <option>Архангельская область</option>
        <option>Республика Карелия</option>
        <option>Республика Коми</option>
        <option>Ненецкий автономный округ</option>
        <option>Ямало-ненецкий автономный округ</option>
        <option>Красноярский край</option>
        <option>Республика Саха (Якутия)</option>
        <option>Чукотский автономный округ</option>
      </select>

      <!--todo      Царство:-->
      <!--      <select>-->
      <!--        <option></option>-->
      <!--      </select>-->

    </div>

    <div>
      <p>Все: </p>
      <button/>
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

  data() {
    return {
      allOrganisms: [{id: 1, nameRussian: "Fish"}, {id: 2, nameRussian: "Crab"}, {id: 3, nameRussian: "Cat"}],
      selectedOrganisms: [],
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
      }
    },

    removeSelect(organism) {
      this.selectedOrganisms = this.selectedOrganisms.filter(item => item.nameRussian !== organism.nameRussian)
    },

    async getOrganisms() {
      const count = 2;
      const offset = (this.page - 1) * count
      const response = await fetch(`http://localhost:8080/api/v1/organisms?offset=${offset}&count=${count + 1}&search=${this.search}`);
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