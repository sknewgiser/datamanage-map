<template>
  <div class='control-pane'>
       <p>渲染类型</p>
       <div class='render-img'>
        <img :src="imgUrl_simple"/>
        <!-- <div>简单符号</div> -->
        <img :src="imgUrl_unique"/>
        <!-- <div>唯一值渲染</div> -->
        <img :src="imgUrl_class"/>
        <!-- <div>分类渲染</div> -->
      </div>
      <p>符号化选项</p>
      <div class ='symbol'>
        <div class='symbol-color'>
          <p><span>符号颜色:</span></p>
          <div class='symbol-color-box'>
            <div class='symbol-color-opacity' v-on:click="symbol_toggle">{{symbol_color_opacity}}
              <div class="symbol-color-selector" v-show="symbol_show" >
                <div class="opacity-range">
                  <span>透明度</span>
                  <input id="symbol-size" type="range" max=1 min=0 value=1 step=0.1 />
                </div>
              </div>
            </div>
            <color-picker type="sketch" v-model="symbol_color" @change="onChange"></color-picker>
          </div>
        </div>
        <div class='line-color'>
          <p><span>轮廓颜色:</span></p>
          <div class='line-color-box'>
            <div class='line-color-opacity' v-on:click="line_toggle">{{line_color_opacity}}
              <div class="line-color-selector" v-show="line_show" >
                <div class="opacity-range">
                  <span>透明度</span>
                  <input id="symbol-size" type="range" max=1 min=0 value=1 step=0.1 />
                </div>
              </div>
            </div>
            <color-picker type="sketch" v-model="line_color" @change="onChange"></color-picker>
          </div>
        </div>
        <div>
          <p><span>轮廓线型:</span></p>
          <div class='outline-type'></div>
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
      imgUrl_simple: require('../assets/simple.jpg'),
      imgUrl_unique: require('../assets/unique.jpg'),
      imgUrl_class: require('../assets/class.jpg'),
      symbol_color: 'green',
      line_color: 'blue',
      symbol_color_opacity: 1,
      line_color_opacity: 0.5,
      symbol_show: false,
      line_show: false
    }
  },
  methods: {
    onChange (color) {
      console.log(color)
    },
    symbol_toggle () {
      this.symbol_show = !this.symbol_show
    },
    line_toggle () {
      this.line_show = !this.line_show
    }
  }
}
</script>

<style scoped>
.control-pane {
    position: absolute;
    top: 0px;
    left: 0px;
    font-size: 1.25em;
    font-family: monospace;
    color: #4C4C4C;
    width: 240px;
    background-color: #f5f1f1;
    padding: 10px;
    border: 2px solid #57585A;
    border-radius: 5px;
}
.control-pane p{
  font-size: 20px;
  color: black;
  font-weight: bold;
}
.control-pane .symbol p span{
  display: block;
  font-size: 12px;
  float: left;
  padding: 10px;
}
.render-img img {
  width: 60px;
  height: 50px;
  border: 2px solid rgb(248, 247, 247);
}
.symbol-color-opacity{
  width: 34px;
  height: 34px;
  float: left;
  /* margin: 0; */
  /* display:inline; */
  border: 1px solid #bfcbd9;
  border-radius: 4px;
  text-align: center;
  line-height: 33px;
}
.line-color-opacity{
  width: 34px;
  height: 34px;
  float: left;
  /* margin: 0;
  display:inline-block; */
  border: 1px solid #bfcbd9;
  border-radius: 4px;
  text-align: center;
  line-height: 33px;
}
.symbol-color-box{
  position: relative;
}
.symbol-color-selector{
  position: absolute;
  width: 230px;
  height: 33px;
  background:white;
  border:1px solid #bfcbd9;
  border-radius:4px;
}
.line-color-box{
  position: relative;
}
.line-color-selector{
  position: absolute;
  width: 230px;
  height: 33px;
  background:white;
  border:1px solid #bfcbd9;
  border-radius:4px;
}
.opacity-range{
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
}
/*横条样式*/
input[type=range] {
  -webkit-appearance: none;/*清除系统默认样式*/
  width: 65%;
  background: -webkit-linear-gradient(#61bd12, #61bd12) no-repeat, #ddd;/*设置左边颜色为#61bd12，右边颜色为#ddd*/
  background-size: 75% 100%;/*设置左右宽度比例*/
  height: 3px;/*横条的高度*/
}
/*拖动块的样式*/
input[type=range]::-webkit-slider-thumb {
  -webkit-appearance: none;/*清除系统默认样式*/
  height: 16px;/*拖动块高度*/
  width: 16px;/*拖动块宽度*/
  background: #fff;/*拖动块背景*/
  border-radius: 50%; /*外观设置为圆形*/
  border: solid 2px #61bd12; /*设置边框*/
}
</style>
