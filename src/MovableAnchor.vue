<template>
  <div style="position: relative;">
    <slot></slot>
  </div>
</template>

<script>
export default {
  name: 'MovableAnchor',
  props: {
    height: {
      type: Number,
      default: 0
    },
    width: {
      type: Number,
      default: 0
    }
  },
  methods: {
    resize (e) {
      this.$emit('resize')
    },
    getBounding () {
      if (this.height && this.width) {
        return {
          height: this.height,
          width: this.width
        }
      }
      return {
        height: this.$el.offsetHeight,
        width: this.$el.offsetWidth
      }
    }
  },
  created () {
    window.addEventListener('resize', this.resize, { passive: true })
  },
  destroyed () {
    window.removeEventListener('resize', this.resize)
  }
}
</script>
