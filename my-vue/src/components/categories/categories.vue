<template>
  <div id="app">
    <van-tree-select
      :items="items"
      :main-active-index="mainActiveIndex"
      :active-id="activeId"
      @navclick="onNavClick"
      @itemclick="onItemClick"
    />
  </div>
</template>
<script>
export default {
  data () {
    return {
      items: [
        {
          // 导航名称
          text: '美味水果',
          // 该导航下所有的可选项
          children: [
            {
              // 名称
              text: '砀山油桃',
              // id，作为匹配选中状态的标识
              id: 1
              // 禁用选项
              // disabled: true
            },
            {
              text: '现摘香蕉',
              id: 2
            },
            {
              text: '金都一号红心火龙果',
              id: 3
            }
          ]
        },
        {
          // 导航名称
          text: '休闲零食',
          // 该导航下所有的可选项
          children: [
            {
              // 名称
              text: '温州',
              // id，作为匹配选中状态的标识
              id: 1
              // 禁用选项
              // disabled: true
            },
            {
              text: '杭州',
              id: 2
            }
          ]
        }
      ],
      // 左侧高亮元素的index
      mainActiveIndex: 0,
      // 被选中元素的id
      activeId: 1
    }
  },
  methods: {
    onNavClick (index) {
      this.mainActiveIndex = index
    },
    onItemClick (data) {
      this.activeId = data.id
    }
  }
}
</script>

<style lang="less">

</style>
