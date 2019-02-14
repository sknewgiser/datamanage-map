<template>
  <div id="map">
  </div>
</template>

<script>
import 'leaflet/dist/leaflet.css'
import L from 'leaflet/dist/leaflet-src.js'
import {basemapLayer, featureLayer} from 'esri-leaflet/dist/esri-leaflet-debug.js'
// import Renderers from 'esri-leaflet-renderers/dist/esri-leaflet-renderers.js'

export default {
  name: 'map-container',
  data () {
    return {
      map: null,
      layer: null,
      curRenderType: 'simple',
      fields: [],
      fieldValues: [],
      renderField: 'Shape_Area',
      grades: [],
      classBreakColors: ['#BD0026', '#E31A1C', '#FC4E2A']
    }
  },
  mounted: function () {
    var vm = this
    this.map = L.map('map').setView([39.56, 116.34], 10)
    // let map = L.map('map').setView([45.526, -122.667], 13)
    basemapLayer('Streets').addTo(this.map)
    this.layer = featureLayer({
      url: 'https://ictgis.thupdi.com:6443/arcgis/rest/services/CityPlat/DataMap/MapServer/0',
      style: this.customStyle
    }).addTo(this.map)
    this.layer.on('load', function () {
      vm.layer.eachFeature(function (layer) {
        let value = layer.feature.properties[vm.renderField]
        vm.fieldValues.push(value)
        vm.fields = vm.fields.length === 0 ? Object.keys(layer.feature.properties) : vm.fields
      })
    })
  },
  methods: {
    customStyle (feature) {
      return {
        color: '#ffff',
        width: 2,
        fillColor: 'red'
      }
    },
    setRender (params) {
      let renderType = params.renderType
      switch (renderType) {
        case 'simple':
          this.layer.setStyle(function (feature) {
            return params
          })
          break
        case 'classbreak':
          this.layer.setStyle(function (feature) {
            console.log('class')
            // params.fillColor = this.getColor(feature.properties.Shape_Area * 1000)
            return params
          })
          break
        case 'unique':
          this.layer.setStyle(function (feature) {
            return params
          })
          break
      }
    },
    getColor (v) {
      this.fieldValues.sort((a, b) => (a - b))
      this.grades = this.getJenksBreaks(this.fieldValues, 3)
      this.grades.unshift(parseInt(this.fieldValues[0]))
      for (let i = 0; i < this.grades.length - 1; i++) {
        if (v > this.grades[i] && v <= this.grades[i + 1]) {
          return this.classBreakColors[i]
        }
      }
    },
    getJenksBreaks (data, numclass) {
      let numdata = data.length
      let mat1 = []
      let mat2 = []
      let st = []

      for (let j = 0; j <= numdata; j++) {
        mat1[j] = []
        mat2[j] = []
        st[j] = 0
        for (let i = 0; i <= numclass; i++) {
          mat1[j][i] = 0
          mat2[j][i] = 0
        }
      }

      for (let i = 1; i <= numclass; i++) {
        mat1[1][i] = 1
        mat2[1][i] = 0
        for (let j = 2; j <= numdata; j++) {
          mat2[j][i] = Number.MAX_VALUE
        }
      }
      let v = 0

      for (let l = 2; l <= numdata; l++) {
        let s1 = 0
        let s2 = 0
        let w = 0
        let i3 = 0
        for (let m = 1; m <= l; m++) {
          i3 = l - m + 1

          let val = parseInt(data[i3 - 1])

          s2 += val * val
          s1 += val

          w++
          v = s2 - (s1 * s1) / w
          let i4 = i3 - 1
          if (i4 !== 0) {
            for (let j = 2; j <= numclass; j++) {
              if (mat2[l][j] >= (v + mat2[i4][j - 1])) {
                mat1[l][j] = i3
                mat2[l][j] = v + mat2[i4][j - 1]

                if (l === 200 && j === 5) alert('l=' + 200 + ',j=' + 5 + ';mat2[200][5]=' + mat1[l][j] + 'i3=' + i3)
              }
            }
          }
        }

        mat1[l][1] = 1
        mat2[l][1] = v
      }

      let k = numdata
      let kclass = []

      /* int[] kclass = new int[numclass]; */
      kclass[numclass - 1] = parseInt(data[data.length - 1])
      /* kclass[numclass - 1] = (Integer) data.get(data.size() - 1); */

      for (let j = numclass; j >= 2; j--) {
        let id = parseInt(mat1[k][j]) - 2
        kclass[j - 2] = parseInt(data[id])
        k = parseInt(mat1[k][j] - 1)
      }
      return kclass
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
