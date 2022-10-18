<template>
  <div id="map"></div>
</template>

<script>
import L from "leaflet";

export default {
  name: "app-map",

  props: ["features"],

  data() {
    return {
      regions: {},
      layers: [],
      map: {}
    }
  },

  methods: {
    async getRegions() {
      const response = await fetch("http://localhost:8080/api/v1/animals?offset=0&count=10&search=Гусь")
      this.regions = await response.json()
    },

    log(organism) {
      console.log(organism.nameRu)
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

    // Creating latlng object
    // const latlngs = [
    //   [17.385044, 78.486671],
    //   [16.506174, 80.648015],
    //   [17.686816, 83.218482]
    // ];
    // // Creating a polygon
    // const polygon = L.polygon(latlngs, {color: 'red'});
    //
    // // Adding to polygon to map
    // polygon.addTo(this.map);

    this.getRegions()
  },

  watch: {
    features() {
      this.layers.push(L.geoJSON(this.features))
    },

    layers() {
      this.layers.addTo(this.map)
    },

    regions() {
      let newLayer = L.geoJSON(this.regions, {
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
     newLayer.addTo(this.map)
    }
  }
}
</script>

<style scoped>
@import "/node_modules/leaflet/dist/leaflet.css";

#map {
  height: 900px;
}
</style>