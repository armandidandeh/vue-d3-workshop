<template>
  <BasePage>
    <div slot="readme" ref="readme" v-html="readme"></div>
    <div slot="example" ref="example">
      <!-- <word-cloud v-bind="cloudOptions" /> -->
      <GeoMap
        ref="map"
        @mousedown.left.native="stopAnimation()"
        @mouseup.native="onMouseUp"
      />
    </div>
  </BasePage>
</template>

<script>
import readme from './readme.md'
import BasePage from '@/views/BasePage.vue'
export default {
  data() {
    return {
      readme,
      cloudOptions: {
        value:     null,
        size:      [1000, 1000],
        rotate:    () => (Math.random() > 0.5 ? 0 : (90 * Math.PI) / 180),
        immediate: true
      },
      curRotation:  [0, 0, 0],
      curAnimation: null,
      animating:    true
    }
  },
  components: {
    BasePage,
    GeoMap: () => import('@/components/d3/finished/VersorDrag.vue')
  },
  methods: {
    animate(id) {
      this.curRotation[0]++
      if (this.$refs.map) this.$refs.map.rotation = this.curRotation
      // console.log('wtf')
      if (!this.curAnimation) this.curAnimation = id
      if (this.animating) return requestAnimationFrame(this.animate)
    },
    stopAnimation() {
      this.animating = false
    },
    startAnimation() {
      this.animating = true
      this.animate()
    },
    onMouseUp() {
      this.curRotation = this.$refs.map.rotation
      this.startAnimation()
    }
  },
  mounted() {
    this.$nextTick(() => {
      const rect = this.$refs.example.getBoundingClientRect()
      this.cloudOptions.size = [rect.width, rect.height]
      this.cloudOptions.value = this.$refs.readme.innerText
      this.startAnimation()
    })
  },
  beforeDestroy() {
    this.stopAnimation()
  }
}
</script>
