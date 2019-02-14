<template>
  <div class='control-pane'>
      <p>渲染类型</p>
      <div class="render-img" v-for="(item,index) in renderTypeImageList" :key="index"
       @click="item.onItemClick(index)">
        <img :src="item.imgUrl" :class="{'active': index==dynamic}"/>
        <span>{{item.type}}</span>
      </div>
      <p>符号化选项</p>
      <div class ='simple-symbol' ref='simple'>
        <div class='symbol-color'>
          <span class="option-title">符号颜色</span>
          <div class='symbol-color-box'>
            <div class='symbol-color-opacity' v-on:click="simple_symbol_toggle">{{simple_symbol_color_opacity}}
              <div class="symbol-opacity-range" v-show="simple_symbol_show" >
                <!-- <div class="opacity-range"> -->
                  <span>透明度</span>
                  <input v-on:click="simple_rangeChanged1" ref="simple_range1" id="symbol-size" type="range" max=1 min=0 value=1 step=0.1 />
                <!-- </div> -->
              </div>
            </div>
            <color-picker type="sketch" v-model="simple_symbol_color" @change="onFillColorChange"></color-picker>
          </div>
        </div>
        <div class='line-color'>
          <span class='option-title'>轮廓颜色</span>
          <div class='line-color-box'>
            <div class='line-color-opacity' v-on:click="simple_line_toggle">{{simple_line_color_opacity}}
              <div class="line-opacity-range" v-show="simple_line_show" >
                  <span>透明度</span>
                  <input v-on:click="simple_rangeChanged2" ref="simple_range2" id="symbol-size" type="range" max=1 min=0 value=1 step=0.1 />
              </div>
            </div>
            <color-picker type="sketch" v-model="simple_line_color" @change="onLineColorChange"></color-picker>
          </div>
        </div>
        <div class='outline-type'>
          <span class='option-title'>轮廓线型</span>
          <div class="outline-type-box">
            <div class='outline-width' v-on:click="simple_width_toggle">{{simple_line_width}}
              <div class="outline-width-range" v-show="simple_width_show" >
                  <span>宽度</span>
                  <input v-on:click="simple_widthChanged" ref="simple_range3" id="line-width" type="range" max=10 min=0 value=1 step=0.1 />
              </div>
            </div>
            <div class="outline-type-selectBox" v-on:click.stop="simpleArrowDown" title="请选择">
              <img :src="unitUrl" style="position:absolute;width:80px;height:2px;margin-left:10px;margin-top:10px"/>
              <div class="selectBox_show">
                <i class="icon icon_arrowDown"></i>
              </div>
              <div class="selectBox_list" v-show="isShowSelect">
                <div class="selectBox_listLi" v-for="(item, index) in symbolImageList" :key="index"
                    @click.stop="selectLineType(item, index)" :title="item.name">
                    <img :src="item.url" style="width:100px;height:2px;"/>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class ='classbreak-symbol' ref='classbreak' style="display:none">
        <div class='option-field'>
          <span class='option-title'>分类字段</span>
          <div class="render-field-selectBox" v-on:click.stop="fieldListArrowDown" title="请选择">
            <span>{{curField}}</span>
            <div class="selectBox_show">
                  <i class="icon icon_arrowDown"></i>
            </div>
            <ul class="selectfield_list" v-show="isShowField" >
              <li class="field_listLi" v-for="(item, index) in fieldList" :key="index"
                  @click.stop="selectField(item, index)">
                  {{item}}
              </li>
            </ul>
          </div>
        </div>
        <div class='classbreak-type'>
          <span class="option-title">分段方式</span>
          <div class="class-type-selectBox" v-on:click.stop="typeListArrowDown" title="请选择">
            <span>{{curType}}</span>
            <div class="selectBox_show">
                  <i class="icon icon_arrowDown"></i>
            </div>
            <ul class="select_type_list" v-show="isShowTypes" >
              <li class="type_listLi" v-for="(item, index) in classTypeList" :key="index"
                  @click.stop="selectClassifyType(item, index)">
                  {{item}}
              </li>
            </ul>
          </div>
        </div>
        <div class='classbreak-num'>
          <span class="option-title">分段数量</span>
          <input v-on:click="classify_numChanged" ref="classify_num_range" id="class-num-range" type="range" max=9 min=3 value=3 step=1 />
          <div class='class-num'>{{classify_num}}</div>
        </div>
        <div class='symbol-color'>
          <span class='option-title'>填充颜色</span>
          <div class='symbol-color-box'>
            <div class='symbol-color-opacity' v-on:click="classify_symbol_toggle">{{classify_symbol_color_opacity}}
              <div class="symbol-opacity-range" v-show="classify_symbol_show" >
                <!-- <div class="opacity-range"> -->
                  <span>透明度</span>
                  <input v-on:click="classify_rangeChanged1" ref="classify_range1" id="symbol-size" type="range" max=1 min=0 value=1 step=0.1 />
                <!-- </div> -->
              </div>
            </div>
            <color-ramp></color-ramp>
          </div>
        </div>
        <div class='line-color'>
          <span class='option-title'>轮廓颜色</span>
          <div class='line-color-box'>
            <div class='line-color-opacity' v-on:click="classify_line_toggle">{{classify_line_color_opacity}}
              <div class="line-opacity-range" v-show="classify_line_show" >
                  <span>透明度</span>
                  <input v-on:click="classify_rangeChanged2" ref="classify_range2" id="symbol-size" type="range" max=1 min=0 value=1 step=0.1 />
              </div>
            </div>
            <color-picker type="sketch" v-model="classify_line_color" @change="onLineColorChange"></color-picker>
          </div>
        </div>
        <div class='outline-type'>
          <span class='option-title'>轮廓线型</span>
          <div class="outline-type-box">
            <div class='outline-width' v-on:click="classify_width_toggle">{{classify_line_width}}
              <div class="outline-width-range" v-show="classify_width_show" >
                  <span>宽度</span>
                  <input v-on:click="classify_widthChanged" ref="classify_range3" id="line-width" type="range" max=10 min=0 value=1 step=0.1 />
              </div>
            </div>
            <div class="outline-type-selectBox" v-on:click.stop="classifyArrowDown" title="请选择">
              <img :src="unitUrl" style="position:absolute;width:80px;height:2px;margin-left:10px;margin-top:10px"/>
              <div class="selectBox_show">
                <i class="icon icon_arrowDown"></i>
              </div>
              <div class="selectBox_list" v-show="isShowSelect">
                <div class="selectBox_listLi" v-for="(item, index) in symbolImageList" :key="index"
                    @click.stop="selectLineType(item, index)" :title="item.name">
                    <img :src="item.url" style="width:100px;height:2px;"/>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class ='unique-symbol' ref='unique' style="display:none">
        <div class='option-field'>
          <span class='option-title'>渲染字段</span>
          <div class="render-field-selectBox" v-on:click.stop="fieldListArrowDown" title="请选择">
            <span>{{curField}}</span>
            <div class="selectBox_show">
                  <i class="icon icon_arrowDown"></i>
            </div>
            <ul class="selectfield_list" v-show="isShowField" >
              <li class="field_listLi" v-for="(item, index) in fieldList" :key="index"
                  @click.stop="selectField(item, index)">
                  {{item}}
              </li>
            </ul>
          </div>
        </div>
        <div class='symbol-color'>
          <span class='option-title'>填充颜色</span>
          <div class='symbol-color-box'>
            <div class='symbol-color-opacity' v-on:click="unique_symbol_toggle">{{unique_symbol_color_opacity}}
              <div class="symbol-opacity-range" v-show="unique_symbol_show" >
                <!-- <div class="opacity-range"> -->
                  <span>透明度</span>
                  <input v-on:click="unique_rangeChanged1" ref="unique_range1" id="symbol-size" type="range" max=1 min=0 value=1 step=0.1 />
                <!-- </div> -->
              </div>
            </div>
            <color-ramp></color-ramp>
          </div>
        </div>
        <div class='line-color'>
          <span class='option-title'>轮廓颜色</span>
          <div class='line-color-box'>
            <div class='line-color-opacity' v-on:click="unique_line_toggle">{{unique_line_color_opacity}}
              <div class="line-opacity-range" v-show="unique_line_show" >
                  <span>透明度</span>
                  <input v-on:click="unique_rangeChanged2" ref="unique_range2" id="symbol-size" type="range" max=1 min=0 value=1 step=0.1 />
              </div>
            </div>
            <color-picker type="sketch" v-model="unique_line_color" @change="onLineColorChange"></color-picker>
          </div>
        </div>
        <div class='outline-type'>
          <span class='option-title'>轮廓线型</span>
          <div class="outline-type-box">
            <div class='outline-width' v-on:click="unique_width_toggle">{{unique_line_width}}
              <div class="outline-width-range" v-show="unique_width_show" >
                  <span>宽度</span>
                  <input v-on:click="unique_widthChanged" ref="unique_range3" id="line-width" type="range" max=10 min=0 value=1 step=0.1 />
              </div>
            </div>
            <div class="outline-type-selectBox" v-on:click.stop="uniqueArrowDown" title="请选择">
              <img :src="unitUrl" style="position:absolute;width:80px;height:2px;margin-left:10px;margin-top:10px"/>
              <div class="selectBox_show">
                <i class="icon icon_arrowDown"></i>
              </div>
              <div class="selectBox_list" v-show="isShowSelect">
                <div class="selectBox_listLi" v-for="(item, index) in symbolImageList" :key="index"
                    @click.stop="selectLineType(item, index)" :title="item.name">
                    <img :src="item.url" style="width:100px;height:2px;"/>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
  </div>
</template>

<script>
// import colorPicker from '@caohenghu/vue-colorpicker'
import { VueColorpicker } from 'vue-pop-colorpicker'
import colorMaps from 'color_ramps'
import ColorRamp from '../components/ColorRamp.vue'
console.log(colorMaps)
export default {
  name: 'pane',
  components: {
    'color-picker': VueColorpicker,
    'color-ramp': ColorRamp
  },
  data () {
    return {
      renderTypeImageList: [
        {type: '单色填充', imgUrl: require('../assets/img/simple2.png'), onItemClick: this.simpleRenderer},
        {type: '分段填充', imgUrl: require('../assets/img/classbreak.jpg'), onItemClick: this.classifyRenderer},
        {type: '单值填充', imgUrl: require('../assets/img/unique.jpg'), onItemClick: this.uniqueRenderer}
      ],
      dynamic: -1,
      simple_symbol_color: 'green',
      classify_symbol_color: 'green',
      unique_symbol_color: 'green',
      simple_line_color: 'blue',
      classify_line_color: 'blue',
      unique_line_color: 'blue',
      simple_symbol_color_opacity: 1,
      classify_symbol_color_opacity: 1,
      unique_symbol_color_opacity: 1,
      simple_line_color_opacity: 0.5,
      classify_line_color_opacity: 0.5,
      unique_line_color_opacity: 0.5,
      simple_line_width: 1,
      classify_line_width: 1,
      unique_line_width: 1,
      simple_symbol_show: false,
      classify_symbol_show: false,
      unique_symbol_show: false,
      simple_line_show: false,
      classify_line_show: false,
      unique_line_show: false,
      simple_width_show: false,
      classify_width_show: false,
      unique_width_show: false,
      isShowSelect: false,
      isShowField: false,
      isShowTypes: false,
      symbolImageList: [
        // {key: -1, value: '请选择'},
        {key: 0, name: '实线', value: '0', url: require('../assets/img/solid.png')},
        {key: 1, name: '短虚线', value: '5', url: require('../assets/img/dotdash.png')},
        {key: 2, name: '长虚线', value: '10', url: require('../assets/img/longdash.png')},
        {key: 3, name: '点虚线', value: '2', url: require('../assets/img/shortdash.png')}
      ],
      unitUrl: require('../assets/img/solid.png'),
      fieldList: [],
      curField: '',
      classTypeList: ['自然分段', '平均分段', '分位法', '手动分段', 'H-index'],
      curType: '自然分段',
      classify_num: 3
    }
  },
  mounted: function () {
    this.dynamic = 0
    this.renderParams = {
      color: this.simple_line_color,
      weight: this.simple_line_width,
      opacity: this.simple_line_color_opacity,
      fillOpacity: this.simple_symbol_color_opacity,
      fillColor: this.simple_symbol_color,
      dashArray: '2'
    }
  },
  methods: {
    onFillColorChange (color) {
      console.log(color)
      this.simple_symbol_color = color
      this.renderParams.fillColor = color
      console.log(this.renderParams)
      this.$emit('paramsListener', this.renderParams)
    },
    onLineColorChange (color) {
      console.log(color)
      this.simple_line_color = color
      this.renderParams.color = color
      console.log(this.renderParams)
      this.$emit('paramsListener', this.renderParams)
    },
    simple_symbol_toggle () {
      this.simple_symbol_show = !this.simple_symbol_show
    },
    classify_symbol_toggle () {
      this.classify_symbol_show = !this.classify_symbol_show
    },
    unique_symbol_toggle () {
      this.unique_symbol_show = !this.unique_symbol_show
    },
    simple_line_toggle () {
      this.simple_line_show = !this.simple_line_show
    },
    classify_line_toggle () {
      this.classify_line_show = !this.classify_line_show
    },
    unique_line_toggle () {
      this.unique_line_show = !this.unique_line_show
    },
    simple_width_toggle () {
      this.simple_width_show = !this.simple_width_show
    },
    classify_width_toggle () {
      this.classify_width_show = !this.classify_width_show
    },
    unique_width_toggle () {
      this.unique_width_show = !this.unique_width_show
    },
    simple_rangeChanged1 () {
      let value = this.$refs.simple_range1.value
      this.simple_symbol_color_opacity = value
      this.renderParams.fillOpacity = value
      // this.$refs.range2.style.background = '-webkit-linear-gradient(#61bd12, #61bd12) no-repeat, #ddd'
      this.$refs.simple_range1.style.backgroundSize = value * 100 + '% 100%'
      this.$emit('paramsListener', this.renderParams)
    },
    simple_rangeChanged2 () {
      let value = this.$refs.simple_range2.value
      this.simple_line_color_opacity = value
      this.renderParams.opacity = value
      // this.$refs.range2.style.background = '-webkit-linear-gradient(#61bd12, #61bd12) no-repeat, #ddd'
      this.$refs.simple_range2.style.backgroundSize = value * 100 + '% 100%'
      this.$emit('paramsListener', this.renderParams)
    },
    classify_rangeChanged1 () {
      let value = this.$refs.classify_range1.value
      this.classify_symbol_color_opacity = value
      // this.$refs.range2.style.background = '-webkit-linear-gradient(#61bd12, #61bd12) no-repeat, #ddd'
      this.$refs.classify_range1.style.backgroundSize = value * 100 + '% 100%'
    },
    classify_rangeChanged2 () {
      let value = this.$refs.classify_range2.value
      this.classify_line_color_opacity = value
      // this.$refs.range2.style.background = '-webkit-linear-gradient(#61bd12, #61bd12) no-repeat, #ddd'
      this.$refs.classify_range2.style.backgroundSize = value * 100 + '% 100%'
    },
    unique_rangeChanged1 () {
      let value = this.$refs.unique_range1.value
      this.unique_symbol_color_opacity = value
      // this.$refs.range2.style.background = '-webkit-linear-gradient(#61bd12, #61bd12) no-repeat, #ddd'
      this.$refs.unique_range1.style.backgroundSize = value * 100 + '% 100%'
    },
    unique_rangeChanged2 () {
      let value = this.$refs.unique_range2.value
      this.unique_line_color_opacity = value
      // this.$refs.range2.style.background = '-webkit-linear-gradient(#61bd12, #61bd12) no-repeat, #ddd'
      this.$refs.unique_range2.style.backgroundSize = value * 100 + '% 100%'
    },
    simple_widthChanged () {
      let value = this.$refs.simple_range3.value
      this.simple_line_width = value
      this.renderParams.weight = value
      // this.$refs.range2.style.background = '-webkit-linear-gradient(#61bd12, #61bd12) no-repeat, #ddd'
      this.$refs.simple_range3.style.backgroundSize = value * 10 + '% 100%'
      this.$emit('paramsListener', this.renderParams)
    },
    classify_widthChanged () {
      let value = this.$refs.classify_range3.value
      this.classify_line_width = value
      // this.$refs.range2.style.background = '-webkit-linear-gradient(#61bd12, #61bd12) no-repeat, #ddd'
      this.$refs.classify_range3.style.backgroundSize = value * 10 + '% 100%'
    },
    unique_widthChanged () {
      let value = this.$refs.unique_range3.value
      this.unique_line_width = value
      // this.$refs.range2.style.background = '-webkit-linear-gradient(#61bd12, #61bd12) no-repeat, #ddd'
      this.$refs.unique_range3.style.backgroundSize = value * 10 + '% 100%'
    },
    classify_numChanged () {
      let value = this.$refs.classify_num_range.value
      this.classify_num = value
      // this.$refs.range2.style.background = '-webkit-linear-gradient(#61bd12, #61bd12) no-repeat, #ddd'
      this.$refs.classify_num_range.style.backgroundSize = value * 10 + '% 100%'
    },
    simpleArrowDown () {
      this.isShowSelect = !this.isShowSelect
    },
    classifyArrowDown () {
      this.isShowSelect = !this.isShowSelect
    },
    uniqueArrowDown () {
      this.isShowSelect = !this.isShowSelect
    },
    fieldListArrowDown () {
      this.isShowField = !this.isShowField
    },
    typeListArrowDown () {
      this.isShowTypes = !this.isShowTypes
    },
    selectLineType (item, index) {
      this.isShowSelect = false
      this.unitUrl = item.url
      this.renderParams.dashArray = item.value
      this.$emit('paramsListener', this.renderParams)
    },
    selectField (item, index) {
      this.isShowField = false
      console.log(item)
      console.log(index)
      this.curField = item
    },
    selectClassifyType (item, index) {
      this.isShowTypes = false
      console.log(index)
      this.curType = item
    },
    simpleRenderer (index) {
      this.renderParams.renderType = 'simple'
      this.dynamic = index
      this.$refs.simple.style.display = 'block'
      this.$refs.unique.style.display = 'none'
      this.$refs.classbreak.style.display = 'none'
      this.$emit('paramsListener', this.renderParams)
    },
    uniqueRenderer (index) {
      this.renderParams.renderType = 'unique'
      this.dynamic = index
      this.$refs.simple.style.display = 'none'
      this.$refs.unique.style.display = 'block'
      this.$refs.classbreak.style.display = 'none'
      this.$emit('paramsListener', this.renderParams)
    },
    classifyRenderer (index) {
      this.renderParams.renderType = 'classbreak'
      this.dynamic = index
      this.$refs.simple.style.display = 'none'
      this.$refs.unique.style.display = 'none'
      this.$refs.classbreak.style.display = 'block'
      this.$emit('paramsListener', this.renderParams)
    }
  },
  watch: {
  }
}
</script>

<style scoped>
@import '../assets/css/pane.css'
</style>
