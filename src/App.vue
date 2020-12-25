<template>
  <div id="app">
    <FlipClock :maxLength = maxLength :changeNumber = changeNumber :thousandsSeparator = thousandsSeparator></FlipClock>
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
      thousandsSeparator: true,
      maxLength: 5, // 数字最大位数
      changeNumber: '00000'
    }
  },
  created() {
  },
  mounted() {
      let randomNumber = Math.floor(Math.random() * 100000)
      this.changeNumber = randomNumber

      setInterval(() => {
        let randomNumber = Math.floor(Math.random() * 100000)
        this.changeNumber = randomNumber
      }, 3000)
  }
}
</script>
