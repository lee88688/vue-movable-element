<template>
  <div
    class="movable" :style="positionStyle">
    <slot name="dragPoint" :dragStart="dragStart" :dragEnd="dragEnd"></slot>
    <slot></slot>
  </div>
</template>

<script>
export default {
  name: 'Movable',
  data () {
    return {
      isMove: false,
      x: 0,
      y: 0,
      initX: 0,
      initY: 0
    }
  },
  props: {
    active: {
      type: Boolean,
      default: true
    },
    value: {
      type: Array,
      default: () => [0, 0]
    },
    bounding: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    positionStyle () {
      return {
        left: `${this.x}px`,
        top: `${this.y}px`
        // cursor: this.active ? 'move' : 'auto'
      }
    }
  },
  watch: {
    value (val) {
      const [x, y] = val
      this.x = Number(x) || 0
      this.y = Number(y) || 0
    }
  },
  methods: {
    dragStart (e) {
      if (!this.active || e.button !== 0) {
        // ignore mouse middle and right button
        return
      }
      document.addEventListener('mousemove', this.drag, {passive: false})
      this.isMove = true
      this.initX = e.pageX - this.$el.offsetLeft
      this.initY = e.pageY - this.$el.offsetTop
      e.stopPropagation()
      // console.log('start')
    },
    dragEnd (e) {
      if (!this.active || e.button !== 0) {
        // this condition will be exactly the same as dragStart if.
        // if not, this function will call e.stopPropagation() and
        // this parent element will not revieve event which leads parents failing
        // remove event listener.
        return
      }
      document.removeEventListener('mousemove', this.drag)
      this.isMove = false
      e.stopPropagation()
      // console.log('end')
    },
    drag (e) {
      if (this.active && this.isMove) {
        let x = e.pageX - this.initX
        let y = e.pageY - this.initY
        if (this.bounding) {
          let getBounding = this.$parent.getBounding || this.getBounding
          let {width, height} = getBounding()
          width -= this.$el.offsetWidth
          height -= this.$el.offsetHeight
          x = x > width ? width : x
          x = x < 0 ? 0 : x
          y = y > height ? width : y
          y = y < 0 ? 0 : y
        }
        this.x = x
        this.y = y
        this.$emit('input', [this.x, this.y])
      }
    },
    getBounding () {
      return {
        height: this.$el.parentElement.offsetHeight,
        width: this.$el.parentElement.offsetWidth
      }
    }
  },
  created () {
    const [x, y] = this.value
    this.x = Number(x) || 0
    this.y = Number(y) || 0
  },
  mounted () {
    if (!this.$scopedSlots.dragPoint) {
      const el = this.$el
      el.addEventListener('mousedown', this.dragStart, {passive: false})
      el.addEventListener('mouseup', this.dragEnd, {passive: false})
    }
    this.$el.onselectstart = () => false
    this.$el.ondragstart = () => false
  },
  destroyed () {
    const el = this.$el
    el.removeEventListener('mousedown', this.dragStart)
    el.removeEventListener('mouseup', this.dragEnd)
  }
}
</script>

<style scoped>
.movable {
  position: absolute;
  transition: 0.05s;
}
</style>
