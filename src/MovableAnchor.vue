<template>
  <div style="position: relative;">
    <slot></slot>
  </div>
</template>

<script>
export default {
  name: 'MovableAnchor',
  methods: {
    resize (e) {
      this.$emit('resize')
    },
    getBounding () {
      return {
        height: this.$el.offsetHeight,
        width: this.$el.offsetWidth
      }
    }
  },
  created () {
    window.addEventListener('resize', this.resize, {passive: true})
  },
  destroyed () {
    window.removeEventListener('resize', this.resize)
  }
}
</script>
