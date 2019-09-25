<template>
  <div class="cover-image">
      <div class="cover-item" v-for="(item,index) in images" :key='index'>
          <img :src="item?item:defaultImg" alt="">
      </div>
  </div>
</template>

<script>
export default {
  props: ['images'],
  data () {
    return {
      defaultImg: require('../../assets/img/pic_bg.png')
    }
  }
}
</script>

<style lang='less' scoped>
    .cover-image {
        display: flex;
        margin: 10px 0;
        margin-left: 80px;
        .cover-item {
            border: 1px solid #ccc;
            padding: 10px;
            margin: 0 10px;
        }
    }
</style>
