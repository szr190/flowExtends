<template>
  <div class="d2-theme-header-menu" ref="page" :class="{'is-scrollable': isScroll}" flex="cross:center">
    <div class="d2-theme-header-menu__content" ref="content" flex-box="1" flex>
      <div class="d2-theme-header-menu__scroll" ref="scroll" flex-box="0" :style="'transform: translateX(' + currentTranslateX + 'px);'">
        <el-menu mode="horizontal" :default-active="active" @select="handleMenuSelect">
          <template v-for="(menu, menuIndex) in header">
            <d2-layout-header-aside-menu-item v-if="menu.children === undefined" :menu="menu" :key="menuIndex"/>
            <d2-layout-header-aside-menu-sub v-else :menu="menu" :key="menuIndex"/>
          </template>
        </el-menu>
      </div>
    </div>
    <div v-if="isScroll" class="d2-theme-header-menu__prev" flex-box="0" @click="scroll('left')" flex="main:center cross:center">
      <i class="el-icon-arrow-left"></i>
    </div>
    <div v-if="isScroll" class="d2-theme-header-menu__next" flex-box="0" @click="scroll('right')" flex="cross:center">
      <i class="el-icon-arrow-right"></i>
    </div>
  </div>
</template>

<script>
import { mapState } from 'vuex'
import menuMixin from '../mixin/menu'
import d2LayoutMainMenuItem from '../components/menu-item/index.vue'
import d2LayoutMainMenuSub from '../components/menu-sub/index.vue'
export default {
  name: 'd2-layout-header-aside-menu-header',
  mixins: [
    menuMixin
  ],
  components: {
    'd2-layout-header-aside-menu-item': d2LayoutMainMenuItem,
    'd2-layout-header-aside-menu-sub': d2LayoutMainMenuSub
  },
  computed: {
    ...mapState('d2admin/menu', [
      'header'
    ])
  },
  data () {
    return {
      active: '',
      isScroll: false,
      scrollWidth: 0,
      contentWidth: 0,
      currentTranslateX: 0
    }
  },
  watch: {
    '$route.matched': {
      handler (val) {
        this.active = val[val.length - 1].path
      },
      immediate: true
    }
  },
  methods: {
    scroll (direction) {
      if (direction === 'left') {
        // 向右滚动
        this.currentTranslateX = 0
      } else {
        // 向左滚动
        if (this.contentWidth * 2 - this.currentTranslateX <= this.scrollWidth) {
          this.currentTranslateX -= this.contentWidth
        } else {
          this.currentTranslateX = this.contentWidth - this.scrollWidth
        }
      }
    }
  },
  mounted () {
    const checkScroll = () => {
      let contentWidth = this.$refs.content.clientWidth
      let scrollWidth = this.$refs.scroll.clientWidth
      if (this.isScroll) {
        // 页面依旧允许滚动的情况，需要更新width
        if (this.contentWidth - this.scrollWidth === this.currentTranslateX) {
          // currentTranslateX 也需要相应变化【在右端到头的情况时】
          this.currentTranslateX = contentWidth - scrollWidth
          // 快速的滑动依旧存在判断和计算时对应的contentWidth变成正数，所以需要限制一下
          if (this.currentTranslateX > 0) {
            this.currentTranslateX = 0
          }
        }
        // 更新元素数据
        this.contentWidth = contentWidth
        this.scrollWidth = scrollWidth
        // 判断何时滚动消失: 当page > content + btns(40)
        if (contentWidth > scrollWidth + 40) {
          this.isScroll = false
        }
      }
      // 判断何时滚动出现: 当page < content
      if (!this.isScroll && contentWidth < scrollWidth) {
        this.isScroll = true
        // 注意，当isScroll变为true，对应的元素盒子大小会发生变化
        this.$nextTick(() => {
          contentWidth = this.$refs.content.clientWidth
          scrollWidth = this.$refs.scroll.clientWidth
          this.contentWidth = contentWidth
          this.scrollWidth = scrollWidth
          this.currentTranslateX = 0
        })
      }
    }
    // 初始化判断
    checkScroll()

    // 默认判断父元素和子元素的大小，以确定初始情况是否显示滚动
    // 全局窗口变化监听，判断父元素和子元素的大小，从而控制isScroll的开关
    window.onresize = () => {
      checkScroll()
    }
  }
}
</script>
