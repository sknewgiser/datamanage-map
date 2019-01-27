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
            <div class='symbol-color-opacity' v-on:click="symbol_toggle">{{symbol_color_opacity}}
              <div class="symbol-opacity-range" v-show="symbol_show" >
                <!-- <div class="opacity-range"> -->
                  <span>透明度</span>
                  <input v-on:click="rangeChanged1" ref="range1" id="symbol-size" type="range" max=1 min=0 value=1 step=0.1 />
                <!-- </div> -->
              </div>
            </div>
            <color-picker type="sketch" v-model="symbol_color" @change="onColorChange"></color-picker>
          </div>
        </div>
        <div class='line-color'>
          <span class='option-title'>轮廓颜色</span>
          <div class='line-color-box'>
            <div class='line-color-opacity' v-on:click="line_toggle">{{line_color_opacity}}
              <div class="line-opacity-range" v-show="line_show" >
                  <span>透明度</span>
                  <input v-on:click="rangeChanged2" ref="range2" id="symbol-size" type="range" max=1 min=0 value=1 step=0.1 />
              </div>
            </div>
            <color-picker type="sketch" v-model="line_color" @change="onColorChange"></color-picker>
          </div>
        </div>
        <div class='outline-type'>
          <span class='option-title'>轮廓线型</span>
          <div class="outline-type-box">
            <div class='outline-width' v-on:click="width_toggle">{{line_width}}
              <div class="outline-width-range" v-show="width_show" >
                  <span>宽度</span>
                  <input v-on:click="widthChanged" ref="range3" id="line-width" type="range" max=10 min=0 value=1 step=0.1 />
              </div>
            </div>
            <div class="outline-type-selectBox" v-on:click.stop="arrowDown" title="请选择">
              <img :src="unitUrl" style="position:absolute;width:80px;height:2px;margin-left:10px;margin-top:10px"/>
              <div class="selectBox_show">
                <i class="icon icon_arrowDown"></i>
              </div>
              <div class="selectBox_list" v-show="isShowSelect">
                <div class="selectBox_listLi" v-for="(item, index) in symbolImageList" :key="index"
                    @click.stop="select(item, index)" :title="item.value">
                    <img :src="item.url" style="width:100px;height:2px;"/>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class ='classbreak-symbol' ref='classbreak' style="display:none">
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
        <div class='classbreak-type'>
          <span class="option-title">分段方式</span>
          <div class="class-type-selectBox" v-on:click.stop="typeListArrowDown" title="请选择">
            <span>{{curType}}</span>
            <div class="selectBox_show">
                  <i class="icon icon_arrowDown"></i>
            </div>
            <ul class="select_type_list" v-show="isShowTypes" >
              <li class="type_listLi" v-for="(item, index) in classTypeList" :key="index"
                  @click.stop="selectType(item, index)">
                  {{item}}
              </li>
            </ul>
          </div>
        </div>
        <div class='symbol-color'>
          <span class='option-title'>填充颜色</span>
          <div class='symbol-color-box'>
            <div class='symbol-color-opacity' v-on:click="symbol_toggle">{{symbol_color_opacity}}
              <div class="symbol-opacity-range" v-show="symbol_show" >
                <!-- <div class="opacity-range"> -->
                  <span>透明度</span>
                  <input v-on:click="rangeChanged1" ref="range1" id="symbol-size" type="range" max=1 min=0 value=1 step=0.1 />
                <!-- </div> -->
              </div>
            </div>
            <color-ramp></color-ramp>
          </div>
        </div>
        <div class='line-color'>
          <span class='option-title'>轮廓颜色</span>
          <div class='line-color-box'>
            <div class='line-color-opacity' v-on:click="line_toggle">{{line_color_opacity}}
              <div class="line-opacity-range" v-show="line_show" >
                  <span>透明度</span>
                  <input v-on:click="rangeChanged2" ref="range2" id="symbol-size" type="range" max=1 min=0 value=1 step=0.1 />
              </div>
            </div>
            <color-picker type="sketch" v-model="line_color" @change="onColorChange"></color-picker>
          </div>
        </div>
        <div class='outline-type'>
          <span class='option-title'>轮廓线型</span>
          <div class="outline-type-box">
            <div class='outline-width' v-on:click="width_toggle">{{line_width}}
              <div class="outline-width-range" v-show="width_show" >
                  <span>宽度</span>
                  <input v-on:click="widthChanged" ref="range3" id="line-width" type="range" max=10 min=0 value=1 step=0.1 />
              </div>
            </div>
            <div class="outline-type-selectBox" v-on:click.stop="arrowDown" title="请选择">
              <img :src="unitUrl" style="position:absolute;width:80px;height:2px;margin-left:10px;margin-top:10px"/>
              <div class="selectBox_show">
                <i class="icon icon_arrowDown"></i>
              </div>
              <div class="selectBox_list" v-show="isShowSelect">
                <div class="selectBox_listLi" v-for="(item, index) in symbolImageList" :key="index"
                    @click.stop="select(item, index)" :title="item.value">
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
            <div class='symbol-color-opacity' v-on:click="symbol_toggle">{{symbol_color_opacity}}
              <div class="symbol-opacity-range" v-show="symbol_show" >
                <!-- <div class="opacity-range"> -->
                  <span>透明度</span>
                  <input v-on:click="rangeChanged1" ref="range1" id="symbol-size" type="range" max=1 min=0 value=1 step=0.1 />
                <!-- </div> -->
              </div>
            </div>
            <color-picker type="sketch" v-model="symbol_color" @change="onColorChange"></color-picker>
          </div>
        </div>
        <div class='line-color'>
          <span class='option-title'>轮廓颜色</span>
          <div class='line-color-box'>
            <div class='line-color-opacity' v-on:click="line_toggle">{{line_color_opacity}}
              <div class="line-opacity-range" v-show="line_show" >
                  <span>透明度</span>
                  <input v-on:click="rangeChanged2" ref="range2" id="symbol-size" type="range" max=1 min=0 value=1 step=0.1 />
              </div>
            </div>
            <color-picker type="sketch" v-model="line_color" @change="onColorChange"></color-picker>
          </div>
        </div>
        <div class='outline-type'>
          <span class='option-title'>轮廓线型</span>
          <div class="outline-type-box">
            <div class='outline-width' v-on:click="width_toggle">{{line_width}}
              <div class="outline-width-range" v-show="width_show" >
                  <span>宽度</span>
                  <input v-on:click="widthChanged" ref="range3" id="line-width" type="range" max=10 min=0 value=1 step=0.1 />
              </div>
            </div>
            <div class="outline-type-selectBox" v-on:click.stop="arrowDown" title="请选择">
              <img :src="unitUrl" style="position:absolute;width:80px;height:2px;margin-left:10px;margin-top:10px"/>
              <div class="selectBox_show">
                <i class="icon icon_arrowDown"></i>
              </div>
              <div class="selectBox_list" v-show="isShowSelect">
                <div class="selectBox_listLi" v-for="(item, index) in symbolImageList" :key="index"
                    @click.stop="select(item, index)" :title="item.value">
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
      symbol_color: 'green',
      line_color: 'blue',
      symbol_color_opacity: 1,
      line_color_opacity: 0.5,
      line_width: 1,
      symbol_show: false,
      line_show: false,
      width_show: false,
      isShowSelect: false,
      isShowField: false,
      isShowTypes: false,
      symbolImageList: [
        // {key: -1, value: '请选择'},
        {key: 0, value: '实线', url: require('../assets/img/solid.png')},
        {key: 1, value: '点虚线', url: require('../assets/img/dotdash.png')},
        {key: 2, value: '长虚线', url: require('../assets/img/longdash.png')},
        {key: 3, value: '短虚线', url: require('../assets/img/shortdash.png')}
      ],
      unitUrl: require('../assets/img/solid.png'),
      fieldList: ['id', 'code', 'name', 'popu'],
      curField: 'code',
      classTypeList: ['自然分段', '平均分段', '分位法', '手动分段', 'H-index'],
      curType: '自然分段'
    }
  },
  mounted: function () {
    this.dynamic = 0
  },
  methods: {
    // onItemClick (index) {
    //   if (index === 0) {
    //     this.simpleRenderer(index)
    //   } else if (index === 1) {
    //     this.uniqueRenderer(index)
    //   } else {
    //     this.classifyRenderer(index)
    //   }
    // },
    onColorChange (color) {
      this.$emit('colorPicker', color)
    },
    symbol_toggle () {
      this.symbol_show = !this.symbol_show
    },
    line_toggle () {
      this.line_show = !this.line_show
    },
    width_toggle () {
      this.width_show = !this.width_show
    },
    rangeChanged1 () {
      let value = this.$refs.range1.value
      this.symbol_color_opacity = value
      // this.$refs.range2.style.background = '-webkit-linear-gradient(#61bd12, #61bd12) no-repeat, #ddd'
      this.$refs.range1.style.backgroundSize = value * 100 + '% 100%'
    },
    rangeChanged2 () {
      let value = this.$refs.range2.value
      this.line_color_opacity = value
      // this.$refs.range2.style.background = '-webkit-linear-gradient(#61bd12, #61bd12) no-repeat, #ddd'
      this.$refs.range2.style.backgroundSize = value * 100 + '% 100%'
    },
    widthChanged () {
      let value = this.$refs.range3.value
      this.line_width = value
      // this.$refs.range2.style.background = '-webkit-linear-gradient(#61bd12, #61bd12) no-repeat, #ddd'
      this.$refs.range3.style.backgroundSize = value * 10 + '% 100%'
    },
    arrowDown () {
      this.isShowSelect = !this.isShowSelect
    },
    fieldListArrowDown () {
      this.isShowField = !this.isShowField
    },
    typeListArrowDown () {
      this.isShowTypes = !this.isShowTypes
    },
    select (item, index) {
      this.isShowSelect = false
      // console.log(item)
      // console.log(index)
      // this.unitModel = index
      this.unitUrl = item.url
    },
    selectField (item, index) {
      this.isShowField = false
      console.log(item)
      console.log(index)
      this.curField = item
      // this.unitModel = index
    },
    selectType (item, index) {
      this.isShowTypes = false
      console.log(item)
      console.log(index)
      this.curType = item
      // this.unitModel = index
    },
    simpleRenderer (index) {
      // console.log('simple')
      this.dynamic = index
      this.$refs.simple.style.display = 'block'
      this.$refs.unique.style.display = 'none'
      this.$refs.classbreak.style.display = 'none'
    },
    uniqueRenderer (index) {
      console.log('unique')
      this.dynamic = index
      this.$refs.simple.style.display = 'none'
      this.$refs.unique.style.display = 'block'
      this.$refs.classbreak.style.display = 'none'
    },
    classifyRenderer (index) {
      console.log('classify')
      this.dynamic = index
      this.$refs.simple.style.display = 'none'
      this.$refs.unique.style.display = 'none'
      this.$refs.classbreak.style.display = 'block'
    }
  },
  watch: {
  }
}
</script>

<style scoped>
@import '../assets/css/pane.css'
</style>
