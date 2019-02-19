<template>
  <div id="map">
  </div>
</template>

<script>
import 'leaflet/dist/leaflet.css'
import L from 'leaflet/dist/leaflet-src.js'
import {basemapLayer, featureLayer} from 'esri-leaflet/dist/esri-leaflet-debug.js'
import bus from '../js/bus'
import Renderers from 'esri-leaflet-renderers/dist/esri-leaflet-renderers.js'

export default {
  name: 'map-container',
  created: function () {
    bus.$on('paramsInfo', function (data) {
      this.renderField = data
    })
  },
  data () {
    return {
      curRenderType: 'simple',
      fields: [],
      fieldValues: [],
      renderField: 'objectid',
      grades: [],
      classBreakColors: ['#BD0026', '#E31A1C', '#FC4E2A'],
      classify_num: 3,
      classify_type: '自然分段'
    }
  },
  mounted: function () {
    var vm = this
    this.map = L.map('map').setView([39.56, 116.34], 7)
    basemapLayer('Streets').addTo(this.map)
    this.layer = featureLayer({
      url: 'https://ictgis.thupdi.com:6443/arcgis/rest/services/CityPlat/DataMap/MapServer/0',
      style: this.customStyle
    }).addTo(this.map)

    // var southWest = L.latLng(32.51, 116.70)
    // var northEast = L.latLng(36.52, 120.64)
    var southWest = this.map.getBounds()['_southWest']
    var northEast = this.map.getBounds()['_northEast']
    var bounds = L.latLngBounds(southWest, northEast)

    let query = this.layer.query()
    query.within(bounds)

    query.run(function (error, featureCollection, response) {
      featureCollection.features.forEach(function (feature) {
        vm.fieldValues.push(feature.properties[vm.renderField])
      })
      let allFields = Object.keys(featureCollection.features[0].properties)
      let allValues = Object.values(featureCollection.features[0].properties)
      for (let i = 0; i < allFields.length; i++) {
        if (typeof allValues[i] === 'string' && allValues[i].constructor === String) {
          continue
        } else {
          vm.fields.push(allFields[i])
        }
      }
      vm.sendParamsToPane()
      bus.$on('paramsInfo', function (data) {
        this.renderField = data['renderField']
        this.classify_num = data['classifyNum']
        this.classify_type = data['classifyType']
      })
    })
    console.log(this.layer)
  },
  methods: {
    customStyle (feature) {
      return {
        color: '#ffff',
        width: 2,
        fillColor: 'red'
      }
    },
    sendParamsToPane () {
      // let fieldsInfo = {'avaliableFields': this.fields, renderField: this.renderField}
      bus.$emit('fieldsInfo', this.fields)
    },
    setRender (params) {
      var vm = this
      let renderType = params.renderType
      this.classBreakColors = params.colorRamps || this.classBreakColors
      this.classify_num = params.classifyNum || this.classify_num
      switch (renderType) {
        case 'simple':
          this.layer.setStyle(function (feature) {
            return params
          })
          break
        case 'classbreak':
          this.layer.setStyle(function (feature) {
            // console.log(feature.properties['名称'], feature.properties['所属县区'])
            if (vm.fieldValues.length > 0) {
              params.fillColor = vm.getColor(feature.properties[vm.renderField])
            }
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
      this.grades = this.getJenksBreaks(this.fieldValues, this.classify_num)
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

              //  if (l === 200 && j === 5) alert('l=' + 200 + ',j=' + 5 + ';mat2[200][5]=' + mat1[l][j] + 'i3=' + i3)
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
