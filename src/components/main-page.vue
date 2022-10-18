<template>
  <div class="grid">
    <div id="map"></div>
    <app-menu v-on:pushOrganismId="getFeature" v-on:removeOrganism="removeOrganism"/>
  </div>
</template>

<script>
import L from "leaflet";
import AppMenu from "@/components/app-menu";

export default {
  name: 'main-page',

  data() {
    return {
      features: [],
      layers: [],
      map: {}
    }
  },

  mounted() {
    this.map = new L.Map('map', {
      center: [70.76, 104.68],
      zoom: 4
    })
    // Creating a Layer object
    const layer = new L.TileLayer('http://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png');

    // Adding layer to the map
    this.map.addLayer(layer);
  },

  methods: {
    async getFeature(id) {
      const response = await fetch(`http://localhost:8080/api/v1/animals/${id}`)
      const json = await response.json()

      const layer = L.geoJSON(json, {
        style: function (feature) {
          const rare = feature.properties.rare
          if (rare.includes("б") || rare.includes("а") || rare === "1") {
            return {color: "#ff0101"}
          } else switch (rare) {
            case "2": return {color: "#f37101"}
            case "3": return {color: "#eaac00"}
            case "4": return {color: "#e6d600"}
            case "5": return {color: "#deff00"}
            case "6": return {color: "#acff01"}
            case "7": return {color: "#2cff01"}
          }
        },
        onEachFeature: function (feature, layer) {
          layer.bindPopup(feature.properties.nameRu)
        }
      })
      this.layers.push({id: id, layer: layer})
      layer.addTo(this.map)
    },

    removeOrganism(id) {
      this.layers.forEach(layer => {
            if (layer.id === id) {
              this.map.removeLayer(layer.layer)
            }
          }
      )
    }
  },

  components: {
    AppMenu
  }
}
</script>

<style scoped>
@import "/node_modules/leaflet/dist/leaflet.css";

#map {
  height: 900px;
}

div.grid {
  display: grid;
  grid-template-columns: 2fr 1fr;
}
</style>
