// options API : 处理鼠标位置的逻辑分散在多处,不利于维护和逻辑复用

<template>
  <h3>当前鼠标的位置</h3>
  <div>{{x}} -- {{y}}</div>
</template>

<script>
export default {
  data() {
    return {
      x: 0,
      y: 0
    }
  },

  methods: {
    mousemove(e) {
      this.x = e.pageX
      this.y = e.pageY
    }
  },

  created() {
    document.addEventListener("mousemove", this.mousemove)
  },

  beforeDestroy() {
    document.removeEventListener("mousemove", this.mousemove)
  }

}
</script>

<style>
</style>