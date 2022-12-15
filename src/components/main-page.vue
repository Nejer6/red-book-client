<template>
  <div class="grid" style="height: 100%;">
    <div id="map"></div>
    <app-menu v-on:pushOrganismId="getFeature"
              v-on:removeOrganism="removeOrganism"
              v-on:showArcticTerritory="showArcticTerritory"/>
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
      map: {},
      arcticTerritoryLayer: {},
      lastLayer: {}
    }
  },

  mounted() {
    this.map = new L.Map('map', {
      center: [70.76, 104.68],
      zoom: 4,
      //crs: L.CRS.EPSG3857
    })
    // Creating a Layer object
    const layer = new L.TileLayer('http://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png');

    // Adding layer to the map
    this.map.addLayer(layer);

    const lgnd = L.control({
      position: "bottomright"
    });

    lgnd.onAdd = function () {
      let lgndDiv = L.DomUtil.create('div', 'infolgnd'),
          labels = [];
      labels.push('<b>Редкость</b>')
      labels.push('<div class="circle" style="background: #000000; text-align: center; color: darkgray"> 0')
      labels.push('<div class="circle" style="background: #ff0101; color: black"> 1')
      labels.push('<div class="circle" style="background: #f37101"> 2')
      labels.push('<div class="circle" style="background: #eaac00"> 3')
      labels.push('<div class="circle" style="background: #e6d600"> 4')
      labels.push('<div class="circle" style="background: #deff00"> 5')
      labels.push('<div class="circle" style="background: #acff01"> 6')
      labels.push('<div class="circle" style="background: #2cff01"> 7')
      lgndDiv.innerHTML = labels.join('<br>')

      return lgndDiv
    }

    lgnd.addTo(this.map)

    const tooltip = L.control({
      position: "bottomleft"
    })

    tooltip.onAdd = function () {
      const tooltipDiv = L.DomUtil.create('div', 'tooltip')
      tooltipDiv.style.background = "white"
      tooltipDiv.style.fontSize = "12pt"
      tooltipDiv.style.border = "1px solid black"
      return tooltipDiv
    }

    tooltip.addTo(this.map)

    this.getArcticTerritory()
  },

  methods: {
    async getArcticTerritory() {
      const response = await fetch("http://localhost:8080/api/v1/arctic-territory")
      const json = await response.json()
      this.antimeridian(json.features[0].geometry.coordinates)
      const layer = L.geoJSON(json, {
        style: {
          color: "#1a31db",
          fillOpacity: 0
        }
      })

      this.arcticTerritoryLayer = layer
      layer.addTo(this.map)
    },

    async getFeature(id) {
      const response = await fetch(`http://localhost:8080/api/v1/animals/${id}`)
      const json = await response.json()
      this.antimeridian(json.geometry.coordinates)
      const vue = this
      const layer = L.geoJSON(json, {
        style: function (feature) {
          const rare = feature.properties.rare[0]
          switch (rare) {
            case  "1":
              return {color: "#ff0101"}
            case "2":
              return {color: "#f37101"}
            case "3":
              return {color: "#eaac00"}
            case "4":
              return {color: "#e6d600"}
            case "5":
              return {color: "#deff00"}
            case "6":
              return {color: "#acff01"}
            case "7":
              return {color: "#2cff01"}
            case "0":
              return {color: "#000000"}
          }
        },
        onEachFeature: function (feature, layer) {
           //layer.bindPopup(feature.properties.nameRu)
          //layer.bindTooltip(feature.properties.nameRu)
          layer.on('click', function () {
            layer.bringToBack()
            vue.arcticTerritoryLayer.bringToBack()
          })

          const defaultColor = layer.options.color
          const tooltip = document.querySelector(".tooltip")
          layer.on('mouseover', function () {
            layer.bringToFront()
            //layer.openPopup()
            layer.setStyle({weight: 3, color: "black", fillColor: defaultColor})
            tooltip.innerHTML = feature.properties.nameRu + "<br> Редкость: " + feature.properties.rare
          })

          layer.on('mouseout', function () {
            //layer.closePopup()
           tooltip.innerHTML = ""
            layer.setStyle({weight: 3, color: defaultColor})
          })
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
    },

    showArcticTerritory(display) {
      if (display) {
        this.arcticTerritoryLayer.addTo(this.map)
      } else {
        this.map.removeLayer(this.arcticTerritoryLayer)
      }
    },

    antimeridian(elem) {
      if (Array.isArray(elem)) {
        for (var i = 0; i < elem.length; i++) {
          if (Array.isArray(elem[i][0])) {
            this.antimeridian(elem[i]);
          } else {
            if (elem[i][0] < 0) {
              elem[i][0] = 180 + (180 + elem[i][0]);
            }
          }
        }
      }
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
  height: 100vh;
}

.circle {
  clip-path: circle(50%);
  height: 5em;
  width: 5em;
  text-align: center;
}

div.grid {
  display: grid;
  grid-template-columns: 2fr 1fr;
}
</style>
