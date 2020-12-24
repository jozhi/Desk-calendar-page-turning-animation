/*
 * 翻牌数字动画
 * @author： 大大大，大志
 * @createDate: 2020-12-24
 */
<template>
  <div class="FlipClock">
    <span v-for="(option,index) in FlipperSort" :key="index">
      <Flipper :ref="`flipper${index}`" />
    </span>
  </div>
</template>

<script>
import Flipper from './Flipper.vue'

export default {
  name: 'FlipClock',
  data() {
    return {
      FlipperSort: [],
      arr: []
    }
  },
  props: {
    // 初始数值
    changeNumber: {
      type: [Number, String],
      default: 0
    },
    minLength: {
      type: Number,
      default: 0
    }
  },
  components: {
    Flipper
  },
  watch: {
    changeNumber: function (newVal, oldVal) {
      console.log('changeNumber~~:', newVal, oldVal);
      this.updateView(newVal)
    }
  },
  created() {
    // created 刚好先行初始化子组件
    this.FlipperSort = '0000000000'.substring(0, this.minLength).split('')
  },
  mounted() {
    // mounted 中对构建好的
    for (let i = this.FlipperSort.length - 1; i >= 0; i--) {
      this.arr[i] = this.$refs['flipper' + i]
    }
  },
  methods: {
    // 开始计时
    updateView(newNumber) {
      newNumber = String(newNumber).split('')

      for (let i = 0; i <= this.minLength - 1; i++) {
        let _number = newNumber[i] ? Number(newNumber[i]) : 0
        this.arr[i].length && this.arr[i][0].Next(_number)
      }
    }
  }
}
</script>

<style>
.FlipClock {
    text-align: center;
}
.FlipClock .M-Flipper {
    margin: 0 3px;
}
.FlipClock em {
    display: inline-block;
    line-height: 102px;
    font-size: 66px;
    font-style: normal;
    vertical-align: top;
}
</style>
