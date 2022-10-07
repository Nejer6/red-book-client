<template>
  <div id="container">
    <div>
      <p>Выбранные:</p>
      <img src="/Vector.svg" alt="open selected" @click="selectedShow = !selectedShow" :class="{rotated: !selectedShow}"/>
    </div>

    <app-selected v-if="selectedShow"></app-selected>
    <div>
      <p>Все:</p>
      <img src="/Vector.svg" alt="open all" @click="allShow = !allShow" :class="{rotated: !allShow}"/>
    </div>

    <div v-if="allShow">
      <app-kingdom
          v-for="k in kingdoms"
          v-bind:kingdom="k"
          v-bind:key="k.id"
      ></app-kingdom>
    </div>

  </div>
</template>

<script>


import appKingdom from "@/components/app-kingdom";
import AppSelected from "@/components/app-selected";

export default {
  name: 'app-menu',

  data() {
    return {
      kingdoms: [],
      selectedShow: true,
      allShow: true
    }
  },

  async mounted() {
    const response = await fetch("http://localhost:8080/api/v1/kingdoms");
    this.kingdoms = await response.json()
  },
  components: {
    AppSelected,
    appKingdom
  }
}
</script>

<style scoped>
app-kingdom {
  margin-bottom: 10px;
}

#container {
  padding: 45px;
  overflow-x: auto;
}

p {
  font-size: 20px;
}

.rotated {
  transform: rotate(90deg);
}
</style>