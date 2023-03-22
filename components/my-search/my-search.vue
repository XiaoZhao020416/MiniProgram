<template>
  <view class="my-search-container" :style="{'background-color': bgcolor}">
    <!-- 使用 view 组件模拟 input 输入框的样式 -->
    <view class="my-search-box" :style="{'border-radius': radius + 'px'}" @click="searchBoxHandler">
      <uni-icons type="search" size="17"></uni-icons>
      <text class="placeholder">搜索</text>
    </view>
  </view>
</template> 

<script>
  export default {
    name:"my-search",
    // 指定值类型和属性默认值
    props: {
      // 背景颜色
      bgcolor: {
        type: String,
        default: '#f8e343'
      },
      // 圆角尺寸
      radius: {
        type: Number,
        // 单位是 px
        default: 18
      }
    },
    data() {
      return {
        
      };
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
    padding: 0 10px;
    display: flex;
    align-items: center;
  } 
  .my-search-box {
    height: 36px;
    background-color: #ffffff;
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    .placeholder {
      font-size: 15px;
      margin-left: 5px;
    }
  }
  .search-box {
    position: sticky;
    top: 0;
    z-index: 999;
  }
</style>