<template>
  <div id="map"></div>
</template>

<script>

export default {
  name: "app-map",
  mounted() {
    const L = require('leaflet')

    const mapOptions = {
      center: [70.76, 104.68],
      zoom: 4
    };
    // Creating a map object
    const map = new L.map('map', mapOptions);

    // Creating a Layer object
    const layer = new L.TileLayer('http://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png');

    // Adding layer to the map
    map.addLayer(layer);

    // Creating latlng object
    const latlngs = [
      [17.385044, 78.486671],
      [16.506174, 80.648015],
      [17.686816, 83.218482]
    ];
    // Creating a polygon
    const polygon = L.polygon(latlngs, {color: 'red'});

    // Adding to polygon to map
    polygon.addTo(map);
  }
}
</script>

<style scoped>
@import "/node_modules/leaflet/dist/leaflet.css";

#map {
  height: 100%;
}

.leaflet-bottom {
  display: none
}
</style>