<template>
  <view class="my-search-container" :style="{'background-color': bgcolor}">
    <view class="my-search-box" :style="{'border-radius': radius + 'px'}" @click="searchBoxHandler">
      <!-- 使用 uni-ui 提供的图标组件 -->
      <uni-icons type="search" size="17"></uni-icons>
      <text class="placeholder">搜索</text>
    </view>
  </view>
</template>

<script>
  export default {
    name: "my-search",
    data() {
      return {

      };
    },
    props: {
      // 背景颜色
      bgcolor: {
        type: String,
        default: '#c00000'
      },
      // 圆角尺寸
      radius: {
        type: Number,
        default: 18 //px
      }
    },
    methods: {
      // 点击了模拟的 input 输入框
      searchBoxHandler() {
        // 触发外界通过 @click 绑定的 click 事件处理函数
        this.$emit('click')
      }
    }
  }
</script>

<style lang="scss">
  .my-search-container {
    height: 50px;

    display: flex;
    align-items: center;
    padding: 0 10px;

    .my-search-box {
      height: 36px;
      background-color: #fff;

      width: 100%;
      display: flex;
      justify-content: center;
      align-items: center;

      .placeholder {
        font-size: 15px;
        margin-left: 5px;
      }
    }
  }
</style>
