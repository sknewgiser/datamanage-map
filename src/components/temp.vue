<template>
  <div class='control-pane'>
       <p>渲染类型</p>
       <div class='render-img'>
        <img :src="imgUrl_simple" v-on:click="simpleRenderer"/>
        <!-- <div>简单符号</div> -->
        <img :src="imgUrl_unique" v-on:click="uniqueRenderer"/>
        <!-- <div>唯一值渲染</div> -->
        <img :src="imgUrl_class" v-on:click="classifyRenderer"/>
        <!-- <div>分类渲染</div> -->
      </div>
      <p>符号化选项</p>
      <div class ='symbol'>
        <div class='symbol-color'>
          <p><span>符号颜色:</span></p>
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
          <p><span>轮廓颜色:</span></p>
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
          <p><span>轮廓线型:</span></p>
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
export default {
  name: 'pane',
  components: {
    'color-picker': VueColorpicker
  },
  data () {
    return {
      renderTypeImageList: [
        {imgUrl_simple: require('../assets/img/simple.jpg'), onClick: simpleRenderer},
        {imgUrl_unique: require('../assets/img/unique.jpg'), onClick: uniqueRenderer},
        {imgUrl_class: require('../assets/img/class.jpg'), onClick: classifyRenderer}
      ],
      symbol_color: 'green',
      line_color: 'blue',
      symbol_color_opacity: 1,
      line_color_opacity: 0.5,
      line_width: 1,
      symbol_show: false,
      line_show: false,
      width_show: false,
      isShowSelect: false,
      symbolImageList: [
        // {key: -1, value: '请选择'},
        {key: 0, value: '实线', url: require('../assets/img/solid.png')},
        {key: 1, value: '点虚线', url: require('../assets/img/dotdash.png')},
        {key: 2, value: '长虚线', url: require('../assets/img/longdash.png')},
        {key: 3, value: '短虚线', url: require('../assets/img/shortdash.png')}
      ],
      unitUrl: require('../assets/img/solid.png')
    }
  },
  methods: {
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
    select (item, index) {
      this.isShowSelect = false
      console.log(item)
      console.log(index)
      this.unitModel = index
      this.unitUrl = item.url
    },
    simpleRenderer (layer) {
      console.log('simple')
    },
    uniqueRenderer (layer) {
      console.log('unique')
    },
    classifyRenderer (layer) {
      console.log('classify')
    }
  },
  watch: {
  }
}
</script>

<style scoped>
@import '../assets/css/pane.css'
</style>
