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
      Поиск:
      <input v-model="search">

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
      <app-organism v-if="selectedOrganisms.indexOf(organism) === -1"
                    :organism="organism"
                    @click="selectOrganism(organism)"/>
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
      region: ""
    }
  },

  components: {AppOrganism},

  computed: {
    isEmptySelected() {
      return this.selectedOrganisms.length === 0
    }
  },

  methods: {
    selectOrganism(organism) {
      this.selectedOrganisms.push(organism)
    },

    removeSelect(organism) {
      this.selectedOrganisms = this.selectedOrganisms.filter(item => item !== organism)
    }
  }
}
</script>

<style scoped>

</style>