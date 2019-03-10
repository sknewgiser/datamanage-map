<template>
  <div id="map">
  </div>
</template>

<script>
import 'leaflet/dist/leaflet.css'
import L from 'leaflet/dist/leaflet-src.js'
import {basemapLayer, featureLayer} from 'esri-leaflet/dist/esri-leaflet-debug.js'
import bus from '../js/bus'
import 'esri-leaflet-renderers/dist/esri-leaflet-renderers-debug.js'

export default {
  name: 'map-container',
  created: function () {
    console.log('come on')
  },
  data () {
    return {
      map: null,
      layer: null,
      curRenderType: 'simple',
      fields: [],
      fieldValues: [],
      testValues: [],
      renderField: 'OBJECTID',
      grades: [],
      classBreakColors: ['#BD0026', '#E31A1C', '#FC4E2A','#FD8D3C'],
      classify_num: 3,
      classify_type: '自然分段',
      serviceInfo: {
        drawingInfo: {
          'renderer': {
            'type': 'classBreaks',
            'field': 'OBJECTID',
            'defaultSymbol': null,
            'minValue': 0,
            'classBreakInfos': [],
            'uniqueValueInfos': []
          },
          'label': '',
          'description': ''
        },
        'transparency': 1,
        'labelingInfo': null
      }
    }
  },
  mounted: function () {
    var vm = this
    vm.map = L.map('map').setView([32.56, 117.34], 7)
    basemapLayer('Streets').addTo(vm.map)
    vm.layer = featureLayer({
      url: 'http://124.128.48.217:6080/arcgis/rest/services/sdxzj/MapServer/2'
      // style: this.customStyle
    }).addTo(vm.map)

    // var southWest = L.latLng(29.51, 115.70)
    // var northEast = L.latLng(38.52, 124.64)
    var southWest = this.map.getBounds()['_southWest']
    var northEast = this.map.getBounds()['_northEast']
    console.log(southWest, northEast)
    var bounds = L.latLngBounds(southWest, northEast)

    let query = this.layer.query()
    query.within(bounds)

    query.run(function (error, featureCollection, response) {
      featureCollection.features.forEach(function (feature) {
        vm.fieldValues.push(feature.properties[vm.renderField])
      })
      console.log(vm.fieldValues)
      vm.fields = Object.keys(featureCollection.features[0].properties)
      vm.testValues = Object.values(featureCollection.features[0].properties)
      vm.sendParamsToPane()
      bus.$on('paramsInfo', function (data) {
        vm.renderField = data['renderField']
        vm.classify_num = data['classifyNum']
        vm.classify_type = data['classifyType']
      })
    })
    console.log(this.layer)
  },
  methods: {
    customStyle (feature) {
      return {
        color: '#ffff',
        width: 2,
        fillColor: 'red',
        fillOpacity: 1
      }
    },
    sendParamsToPane () {
      let fieldsInfo = [this.fields, this.testValues]
      // let fieldsInfo = {'avaliableFields': this.fields, renderField: this.renderField}
      bus.$emit('fieldsInfo', fieldsInfo)
    },
    setRender (params) {
      var vm = this
      let renderType = params.renderType
      if (!renderType) {
        renderType = 'simple'
      }
      switch (renderType) {
        case 'simple':
          this.layer.setStyle(function (feature) {
            return params
          })
          break
        case 'classbreak':
          this.serviceInfo.drawingInfo.renderer.type = 'classBreaks'
          let tempClassBreakColors = params.colorRamps || this.classBreakColors
          tempClassBreakColors = tempClassBreakColors.map(vm.transformationRgba)
          tempClassBreakColors = tempClassBreakColors.map(function (v, i) {
            v.pop()
            v.push(parseInt((params.fillOpacity || 1) * 255))
            return v
          })
          this.classify_num = params.classifyNum || this.classify_num
          this.grades = vm.getJenksBreaks(this.fieldValues, this.classify_num)
          let lineColor = vm.transformationRgba(params.color || '#030303')
          lineColor.pop()
          lineColor.push(parseInt((params.opacity || 1) * 255))
          this.serviceInfo.transparency = params.fillOpacity || 0
          for (let i = 0; i < this.grades.length; i++) {
            this.serviceInfo.drawingInfo.renderer.classBreakInfos.push(
              {
                'classMaxValue': this.grades[i],
                'symbol': {
                  'type': 'esriSFS',
                  'style': 'esriSFSSolid',
                  'color': tempClassBreakColors[i],
                  'outline': {
                    'type': 'esriSLS',
                    'style': params.lineType || 'esriSLSolid',
                    'color': lineColor,
                    'width': params.weight || 1
                  }
                }
              }
            )
          }
          vm.map.removeLayer(vm.layer)
          vm.layer = featureLayer({
            url: vm.layer.options.url
          }).addTo(this.map)
          vm.layer._setRenderers(this.serviceInfo)
          this.serviceInfo.drawingInfo.renderer.classBreakInfos = []
          break
        case 'unique':
          this.serviceInfo.drawingInfo.renderer.type = 'uniqueValue'
          this.serviceInfo.drawingInfo.renderer.field = 'OBJECTID'
          let tempColors = params.colorRamps || this.classBreakColors
          tempColors = tempColors.map(vm.transformationRgba)
          tempColors = tempColors.map(function (v, i) {
            v.pop()
            v.push(parseInt((params.fillOpacity || 1) * 255))
            return v
          })
          lineColor = vm.transformationRgba(params.color || '#030303')
          lineColor.pop()
          lineColor.push(parseInt((params.opacity || 1) * 255))
          this.serviceInfo.transparency = params.fillOpacity || 0
          
          for (let j = 0; j < this.fieldValues.length; j++) {
            let k = Math.floor(Math.random() * tempColors.length)
            let fillColor = tempColors[k]
            this.serviceInfo.drawingInfo.renderer.uniqueValueInfos.push(
              {
                'value': this.fieldValues[j],
                'symbol': {
                  'type': 'esriSFS',
                  'style': 'esriSFSSolid',
                  'color': fillColor,
                  'outline': {
                    'type': 'esriSLS',
                    'style': params.lineType || 'esriSLSolid',
                    'color': lineColor,
                    'width': params.weight || 1
                  }
                }
              }
            )
          }
          vm.map.removeLayer(vm.layer)
          vm.layer = featureLayer({
            url: vm.layer.options.url
          }).addTo(this.map)
          vm.layer._setRenderers(this.serviceInfo)
          // delete this.serviceInfo.drawingInfo.renderer.field1
          this.serviceInfo.drawingInfo.renderer.uniqueValueInfos = []
          // this.layer.setStyle(function (feature) {
          //   return params
          // })
          break
      }
    },
    transformationRgba (color) {
      let colors = color.toLowerCase()
      let arr = []
      if (colors.indexOf('#') !== -1) {
        for (let i = 1; i < 7; i += 2) {
          let str = colors.slice(i, i + 2)
          arr.push(parseInt(str, 16))
        }
        arr.push(255)
        return arr
      }
      // rgba(300,300,300)
      if (colors.indexOf('rgb') !== -1) {
        let reg = /rgb\((.+)\)/
        colors.replace(reg, function (a, b) {
          arr = b.split(',')
        })
        arr = arr.map(function (v, i) {
          return parseInt(v)
        })
        arr.push(255)
        return arr
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
      data.sort((a, b) => (a - b))
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
