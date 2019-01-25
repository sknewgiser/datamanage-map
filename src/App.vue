<template>
  <div id="app">
    <app-menu></app-menu>
    <app-pane></app-pane>
    <app-map></app-map>
  </div>
</template>

<script>
import Map from './views/Map'
import pane from './components/pane'
import Menu from './components/Menu'
export default {
  name: 'App',
  components: {
    'app-map': Map,
    'app-pane': pane,
    'app-menu': Menu
  },
  data () {
    return {
    }
  }
}
</script>

<style scoped>
#app{
  width:100%;
  height: 100%;
}
#app >>> .vue-colorpicker-btn{
    display: inline-block ;
    width: 100% ;
    height: 12px ;
    border: 1px solid #666;
    background: #FFFFFF;
}
</style>
