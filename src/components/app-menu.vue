<template>
<div>
  <app-selected />
  <app-all />
</div>
</template>

<script>
import AppSelected from "@/components/app-selected";
import AppAll from "@/components/app-all";
export default {
  name: "app-menu",
  components: {AppAll, AppSelected}
}
</script>

<style scoped>

</style>