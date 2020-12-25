/*
 * 翻牌数字动画
 * @author： 大大大，大志
 * @createDate: 2020-12-24
 */
<template>
  <div class="FlipClock">
    <span v-for="(option,index) in FlipperSort" :key="index">
      <span class="split" v-if="thousandsSeparator && option ===','">,</span>
      <Flipper v-else :ref="`flipper${index}`" />
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
    maxLength: {
      type: Number,
      default: 0
    },
    thousandsSeparator: {
      type: Boolean,
      default: false
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
    this.FlipperSort = this.add_comma_toThousands('00000000000000'.substring(0, this.maxLength)).split('')
  },
  mounted() {
    // mounted 中对构建好的
    for (let i = this.FlipperSort.length - 1; i >= 0; i--) {
      this.FlipperSort[i] !== ',' && (this.arr[i] = this.$refs['flipper' + i])
    }
  },
  methods: {
    add_comma_toThousands(num) {
      let result = '';
      while (num.length > 3) {
          result = ',' + num.slice(-3) + result;
          num = num.slice(0, num.length - 3);
      }
      if (num) { result = num + result; }
      return result;
    },

    // 开始计时
    updateView(newNumber) {
      // 转为字符串
      newNumber = (newNumber || 0).toString();

      // 位数不足时补零,以便于遍历赋值
      if (this.maxLength > newNumber.length) { newNumber = '00000000000000'.substring(0, (this.maxLength - newNumber.length)) + newNumber }

      // 千分位加逗号
      newNumber = this.add_comma_toThousands(newNumber).split('')

      // 单个数字赋值
      for (let i = 0; i <= this.arr.length - 1; i++) {
        if (this.arr[i]) {
          let _number = newNumber[i] ? Number(newNumber[i]) : 0
          this.arr[i].length && this.arr[i][0].Next(_number)
        }
      }
    }
  }
}
</script>

<style>
.FlipClock {
    text-align: center;
}
.FlipClock .split {
  display: inline-block;
  position: relative;
  width: 15px;
  font-size: 55px;
  line-height: 1;
  top: -10px;
}
.FlipClock .M-Flipper {
    margin: 0 10px;
}
.FlipClock em {
    display: inline-block;
    line-height: 102px;
    font-size: 66px;
    font-style: normal;
    vertical-align: top;
}
</style>
