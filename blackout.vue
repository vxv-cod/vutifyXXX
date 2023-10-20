<template>
  <div class="blackout" tag="body" :style="menuShow ? `opacity: 0.4; z-index: 100; ` : ``" />
</template>

<script>
  import { toRefs } from 'vue'

  export default {
    props: {
      menuShow: {
        type: Boolean,
        default: () => false,
      },
    },

    setup(props) {
      const { menuShow } = toRefs(props)
      return {
        menuShow,
      }
    }

  }
</script>

<style lang="sass" scoped>
.blackout
  display: block
  background-color: black
  width: 100%
  height: 100%
  position: fixed
  top: 0
  left: 0
  z-index: -1
  opacity: 0
  transition-property: opacity top z-index
  transition-duration: 0.5s
</style>
