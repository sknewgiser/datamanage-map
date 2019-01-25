<template>
  <div id="menu">
    <div id="logo">
        <img :src='img_logo' style="width:45px;height:45px;margin-left:-2px"/>
    </div>
      <!-- <div class="data-item" :style="{background: background_color}" @click="onDataClick($event)">
          <img :src='img_data'/>
          <span>数据</span>
      </div>
      <div class="map-item" :style="{background: background_color}" :class="{isActive:active}" @click="onDataClick($event)">
          <img :src='img_map'/>
          <span>地图</span>
      </div> -->
    <div class="item-container" v-for="(item,index) in itemList" :key="index"
        v-bind:class="{colorChange:index==dynamic}"
        @click="onClick(index)">
        <img class="item-logo" :src="item.logoUrl"/>
        <span class="item-name">{{item.name}}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Menu',
  data () {
    return {
      img_logo: require('../assets/img/newlogo1.png'),
      background_color: '#fffff',
      itemList: [
        {'logoUrl': require('../assets/img/data.png'), 'name': '数据'},
        {'logoUrl': require('../assets/img/map.png'), 'name': '地图'}
      ],
      dynamic: -1
    }
  },
  methods: {
    onClick (index) {
    //   e.target.style.background = '#0e9fff'
    //   this.background_color = '#0e9fff'
      console.log(index)
      // 点击添加字体颜色，其他的删除class名称
      this.dynamic = index
    }
  }
}
</script>

<style scoped>
#menu {
    position: absolute;
    top: 0px;
    left: 0px;
    font-size: 1.25em;
    font-family: monospace;
    color: #4C4C4C;
    width: 56px;
    height: 100%;
    background-color: #273142;
    padding: 10px;
    border: 1px solid #57585A;
    border-radius: 0px;
}

/* #menu .data-item{
    position: absolute;
    width: 45px;
    height: 45px;
    margin-top: 30px;
    border-radius: 2px;
} */
/* #menu .map-item{
    position: absolute;
    width: 45px;
    height: 45px;
    margin-top: 100px;
    border-radius: 2px;

} */
/* #menu div:hover{
    background-color: gray;
} */
/* #menu div:active{
    background-color: #0e9fff;
} */
/* #menu img{
    width: 20px;
    height: 20px;
    text-align: center;
    margin-left: 12px;
} */
.colorChange {
    background-color: #0e9fff;
}
.item-container{
    float: left;
    width: 45px;
    height: 45px;
    margin-top: 30px;
    margin-left: -3px;
    border-radius: 2px;
}
.item-container img{
    width: 20px;
    height: 20px;
    text-align: center;
    margin-left: 12px;
}
.item-container span{
    color: #fff;
    font-size: 12px;
    margin-left: 8px;
}
.item-container:hover {
    background-color: gray;
}
</style>
