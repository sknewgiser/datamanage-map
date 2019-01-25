<template>
  <div id="map">
  </div>
</template>

<script>
import L from 'leaflet/dist/leaflet-src.js'
import {basemapLayer, featureLayer} from 'esri-leaflet/dist/esri-leaflet-debug.js'
import 'esri-leaflet-renderers/dist/esri-leaflet-renderers-debug.js'
import 'leaflet/dist/leaflet.css'
export default {
  name: 'map-container',
  data () {
    return {
    }
  },
  mounted: function () {
    // let map = L.map('map').setView([39.56, 116.34], 10)
    let map = L.map('map').setView([45.526, -122.667], 13)
    basemapLayer('Streets').addTo(map)
    featureLayer({url: 'https://services.arcgis.com/rOo16HdIMeOBI4Mb/arcgis/rest/services/Heritage_Trees_Portland/FeatureServer/0'}).addTo(map)
  },
  methods: {
    setColor () {

    }
  }
}
</script>

<style scoped>
#map {
    position: absolute;
    width: 82%;
    height: 102%;
    margin-left: 320px;
    margin-top: -8px;
    border:1px solid #000
}
</style>
