<template>
   <div class="pay">
      <div class="pay-container" v-for="(list,index) in payconfigEntities" :key="index" v-bind:class="{colorChange:index==dynamic}" direction="column" @click="getMoney(index)">
        <div class="pay-money">{{list.effect}}</div>
        <div class="pay-time">{{list.duration}}分钟{{list.money}}元</div>
      </div>
    </div>
</template>
<script>
export default {
  name: 'test',
  data () {
    return {
      payconfigEntities: [{'duration': 6, 'effect': '缓解疲劳', 'money': 4}, {'duration': 6, 'effect': '缓解疲劳', 'money': 4}, {'duration': 6, 'effect': '缓解疲劳', 'money': 4}],
      dynamic: -1
    }
  },
  methods: {
    getMoney: function (index) {
      console.log(index)
      // 点击添加字体颜色，其他的删除class名称
      this.dynamic = index
    }
  }
}
</script>
<style scoped>
  .colorChange {
    background-color: orange;
  }
</style>
