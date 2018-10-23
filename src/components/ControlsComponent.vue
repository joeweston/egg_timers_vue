<template>
  <div>
    <button :disabled="state==='on'" @click="start">Start</button>
    <button :disabled="state!=='on'" @click="pause">Pause</button>
    <button :disabled="state==='off'" @click="stop">Stop/Reset</button>
  </div>
</template>

<script>
export default {
  props: ["state"],
  methods: {
    start() {
      this.$emit('start')
    },
    pause() {
      this.$emit('pause')
    },
    stop() {
      this.$emit('stop')
    }
  }

}

</script>

<style lang="scss">

</style>