<template>
  <div id="color-ramp" class="color-ramp-bar-selectbox" v-on:click.stop="onColorRampsArrowDown">
    <ul class='default-color-list'>
        <li class='default-color' style="float:left;overflow:hidden;" v-for="(color,index) in curColorRamp[classifyNum]" :key='index' :style="{background:color, width: (100/classifyNum)+'%'}"></li>
    </ul>
    <div class="select-box-show">
        <i class="icon icon_arrowDown"></i>
    </div>
    <!-- <el-scrollbar> -->
        <ul class="color-ramp-bar-list" v-show="isShowColorRamps">
            <li class='color-ramp-li' v-for="(colorRamps,key) of RampList" :key="key"
            @click.stop="onSelectRampItem(key)">
                <div class='color-ramp-bar' >
                    <ul class='color-list' style="display:list-item" >
                        <li class='color' v-for="(color,index) in colorRamps[classifyNum]" :key='index' :style="{background:color, width: (100/classifyNum)+'%'}"></li>
                    </ul>
                </div>
                <i class="icon color-bar"></i>
            </li>
        </ul>
    <!-- </el-scrollbar> -->
  </div>
</template>

<script>
import ColorRamps from 'color_ramps'
let colors = ColorRamps
console.log(colors)
export default {
  name: 'ColorRamp',
  props: {
    classifyNum: {
      type: Number
    }
  },
  data () {
    return {
      isShowColorRamps: false,
      RampList: ColorRamps,
      curColorRamp: ColorRamps['Blues']
    }
  },
  methods: {
    onColorRampsArrowDown () {
      this.isShowColorRamps = !this.isShowColorRamps
    },
    onSelectRampItem (key) {
      this.isShowColorRamps = false
      console.log(key)
      this.curColorRamp = ColorRamps[key]
      this.$emit('colorRamps', this.curColorRamp[this.classifyNum])
      // 点击添加字体颜色，其他的删除class名
    }
  }
}
</script>

<style scoped>
.color-ramp-bar-selectbox{
    width: 100px;
    height: 24px;
    border: 1px solid #bfcbd9;
    border-radius: 0px 2px 2px 0px;
    margin-left: 34px;
    margin-top: 0px;
    cursor: pointer;
}
.color-ramp-bar-list{
    width: 114px;
    height: 300px;
    overflow-y: scroll;
    overflow-x: hidden;
    margin-left: -53px;
    margin-top: -2px;
    position: absolute;
    z-index: 10;
    background: rgb(255, 255, 255);
    box-shadow: 2px 3px 3px 2px #bfcbd9;
}
.default-color-list{
    width: 69px !important;
    display: inline-flex;
    margin-left: -12px;
    margin-top: 2px;
}
.default-color-list li{
    /* width: 11.11%; */
    height: 10px;
    float: left;
    overflow: hidden;
    cursor: pointer;
    margin-top: 5px;
}
.default-color-list li:first-child{
    border-radius: 5px 0px 0px 5px;
}
.default-color-list li:last-child{
    border-radius: 0px 5px 5px 0px;
}
/* .el-scrollbar{
    height: 100%;
}
.el-scrollbar__wrap{
    overflow: scroll;
    overflow-y: scroll;
    overflow-x: hidden;
} */
/* .selectBox_list{
  float: left;
  display: grid;
  margin-top: 6px;
  margin-left: -34px;
  width: 132px;
  height: 100px;
  border: 1px solid #bfcbd9;
  border-radius: 2px;
  justify-content: center;
  cursor: pointer;
} */
.color-ramp-li {
    width: 120px !important;
    height: 27px !important;
    display: inline-block;
    overflow: hidden;
    /* display: table-row-group; */
    margin-left: -33px;
    /* border: 1px solid blue; */
    cursor: pointer;
    border-bottom: 1px solid #eee;
}
/* .color-ramp-li:before {
    content: "";
    display: block;
    position: absolute;
    top: 9px;
    bottom: 9px;
    left: 10px;
    right: 32px;
    border: 1px solid rgba(17,129,251,.64);
    border-radius: 8px;
    background: rgba(17,129,251,.08);
} */
.color-ramp-bar{
    width: 120px !important;
    margin-left: 0px;
    margin-top: 3px;
    /* border: 1px solid green; */
}
.color-list{
    width: 102px !important;
    display: inline-flex;
    margin-left: -22px;
}
.color-list li{
    /* width: 11.11%; */
    height: 10px;
    float: left;
    overflow: hidden;
    cursor: pointer;
    margin-top: 5px;
}
.color-list li:first-child{
    border-radius: 5px 0px 0px 5px;
}
.color-list li:last-child{
    border-radius: 0px 5px 5px 0px;
}

</style>
