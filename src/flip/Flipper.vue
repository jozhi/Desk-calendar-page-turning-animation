/*
 * 翻牌数字动画
 * @author： 大大大，大志
 * @createDate: 2020-12-24
 */

<template>
  <div class="M-Flipper">

    <!-- 默认数字的上下两部分 -->
    <div class="digital front " :class="[UpperHalfNumber]"></div>
    <div class="digital back " :class="[BottomHalfNumber]"></div>

    <!-- 翻页动画的画册 -->
    <div class="invert" v-for="(option,index) in loop" :class="[option.loopNumber,flipType, {'go': option.going}]" :key="index">
      <span class="after" :style="{'zIndex':option.zIndex}">{{option.index}}</span>
      <span class="before" :style="{'zIndex':option.zIndex}">{{option.index === 0 ? 9 : option.index-1}}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'flipper',
  data() {
    return {
      flipType: 'down', // 动画方向
      UpperHalfNumber: 'number0', // 当前组件底板数值的上半部分
      BottomHalfNumber: 'number0', // 当前组件底板数值的下半部分

      loop: [], // 画册动画部分页码数据

      StartingValue: 0, // 初始值
      FinalValue: 0 // 终值
    }
  },
  props: {
    // 初始数值
    initialValue: {
      type: [Number, String],
      default: 0
    },
    // 翻牌动画结束时间，经测试400毫秒效果最佳
    duration: {
      type: Number,
      default: 400
    }
  },
  created() {
    this.UpperHalfNumber = 'number' + this.initialValue
    this.BottomHalfNumber = 'number' + this.initialValue
      // this.StartingValue = +this.initialValue
      // this.FinalValue = +this.FinalValue
  },
  mounted() {

    // this.loop[0].going = true
  },
  methods: {

    // 初始化动画过度板
    loopInit(start, end, cb) {
      this.UpperHalfNumber = 'number' + start
      this.BottomHalfNumber = 'number' + start

      // console.log('loopInit:', start, end);

      // 根据数字数组初始化数据结构
      const loopBuild = (arr) => {
        let len = arr.length
        arr.forEach((option, index) => {
          // console.log(option, index);
          this.loop.push({
            'index': option,
            'zIndex': len - index,
            'loopNumber': 'invert' + option,
            'going': false,
            'callback': () => {
              setTimeout(() => {
                // 单页动画结束收尾~
                const _index = this.loop[index].index
                this.loop[index].zIndex = 0
                this.BottomHalfNumber = 'number' + option

                // // 自毁机制
                // 如果是最后一个，那么代表动画任务结束，执行清扫工作
                if (this.loop[len - 1].index === _index) {
                  this.loop = []
                  this.StartingValue = this.FinalValue
                  // console.log('重置');
                }
              }, this.duration);
            }
          })
        })

        // 初始化之后回调~
        cb && cb()
      }

      // 初始化动画册数字数组
      let arr = []
      if (end > start) {
        // let diff = end - start
        for (let i = start + 1; i <= end; i++) {
          arr.push(i)
        }
        // console.log('########  end > start  ##########:', arr);
        loopBuild(arr)
      } else if (end < start) {
        // if (start === 9) { arr.push(9) }
        for (let i = start + 1; i <= 9; i++) {
          arr.push(i)
        }
        for (let ii = 0; ii <= end; ii++) {
          arr.push(ii)
        }
        // console.log('######## end < start  ##########:', arr);
        loopBuild(arr)
      }
    },

    // ~~ 启动
    loopRunning(index = 0) {
      let option = this.loop[index]
      if (option) {
        option.going = true
        option.callback()
        setTimeout(() => {
          this.UpperHalfNumber = 'number' + option.index
          this.loopRunning(index + 1)
        }, 100);
      }
    },

    // 初始化以及启动动画
    Next(TargetNumber = 0) {
      // 如果目标值与当前值一样，那么无需做任何事情
      if (TargetNumber === this.FinalValue) { return false; }
      this.FinalValue = TargetNumber

      this.loopInit(this.StartingValue, this.FinalValue, () => {
        this.loopRunning()
      })
    }
  }
}
</script>

<style scoped>
.M-Flipper{
  display: inline-block;
  position: relative;
  width: 60px;
  height: 100px;
  line-height: 100px;
  border: solid 1px #000;
  border-radius: 10px;
  background: #fff;
  font-size: 66px;
  color: #fff;
  box-shadow: 0 0 6px rgba(0, 0, 0, 0.5);
  text-align: center;
  font-family: 'Helvetica Neue';

}
/* .invert{
  position: absolute;
  top: 0;
  left: 0;
} */
.invert .after,
.invert .before{
  content: '';
  position: absolute;
  left: 0;
  right: 0;
  background: #000;
  overflow: hidden;
  box-sizing: border-box;
}

.invert .before {
  top: 0;
  bottom: 50%;
  border-radius: 10px 10px 0 0;
  border-bottom: solid 1px #666;
}

.invert .after {
  top: 50%;
  bottom: 0;
  border-radius: 0 0 10px 10px;
  line-height: 0;
}

/* .invert1:before{ } */
.invert .after{
  transform-origin: 50% 0%;
  transform: perspective(160px) rotateX(180deg);
}

.M-Flipper .invert.down.go .before {
  transform-origin: 50% 100%;
  animation: frontFlipDown 0.6s ease-in-out both;
  box-shadow: 0 -2px 6px rgba(255, 255, 255, 0.3);
  backface-visibility: hidden;
}

.M-Flipper .invert.down.go .after {
  animation: backFlipDown 0.6s ease-in-out both;
}

@keyframes frontFlipDown {
  0% {
    transform: perspective(160px) rotateX(0deg);
  }

  100% {
    transform: perspective(160px) rotateX(-180deg);
  }
}

@keyframes backFlipDown {
  0% {
    transform: perspective(160px) rotateX(180deg);
  }

  100% {
    transform: perspective(160px) rotateX(0deg);
  }
}
</style>

<style scoped>

.M-Flipper .digital:before,
.M-Flipper .digital:after {
  content: '';
  position: absolute;
  left: 0;
  right: 0;
  background: #000;
  overflow: hidden;
  box-sizing: border-box;

  z-index: 1;
}

.M-Flipper .digital.front:before {
  top: 0;
  bottom: 50%;
  border-radius: 8px 8px 0 0;
  border-bottom: solid 1px #666;
}

.M-Flipper .digital.back:after {
  top: 50%;
  bottom: 0;
  border-radius: 0 0 8px 8px;
  line-height: 0;
}

.M-Flipper .digital.back:before,
.M-Flipper .digital.front:after{
  display: none;
}

.M-Flipper .number0:before,
.M-Flipper .number0:after {
  content: '0';
}

.M-Flipper .number1:before,
.M-Flipper .number1:after {
  content: '1';
}

.M-Flipper .number2:before,
.M-Flipper .number2:after {
  content: '2';
}

.M-Flipper .number3:before,
.M-Flipper .number3:after {
  content: '3';
}

.M-Flipper .number4:before,
.M-Flipper .number4:after {
  content: '4';
}

.M-Flipper .number5:before,
.M-Flipper .number5:after {
  content: '5';
}

.M-Flipper .number6:before,
.M-Flipper .number6:after {
  content: '6';
}

.M-Flipper .number7:before,
.M-Flipper .number7:after {
  content: '7';
}

.M-Flipper .number8:before,
.M-Flipper .number8:after {
  content: '8';
}

.M-Flipper .number9:before,
.M-Flipper .number9:after {
  content: '9';
}
</style>
