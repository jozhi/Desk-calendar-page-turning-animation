<template>
  <div id="app">
    <FlipClock :minLength = minLength :changeNumber = changeNumber></FlipClock>
  </div>
</template>

<script>
import FlipClock from './components/FlipClock.vue'

export default {
  name: 'app',
  components: {
    FlipClock
  },
  data() {
    return {
      minLength: 5,
      changeNumber: '00000'
    }
  },
  created() {
      setInterval(() => {
        let randomNumber = Math.floor(Math.random() * 100000)
        this.changeNumber = randomNumber
      }, 3000)
  }
}
</script>
