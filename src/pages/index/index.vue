<template>
  <div class="calculator-wrap">
    <div class="result-screen">
        <div class="history-pad">
          <span class="iconfont icon-lishi"></span>
        </div>
        <div class="record-pad">{{ recordValue }}</div>
        <div class="input-pad" :class="{hideRecord: showRecord, miniFont_0: changeFontSize_0, miniFont_1: changeFontSize_1, miniFont_2: changeFontSize_2}">{{ formatInput }}</div>
    </div>
    <div class="operation-wrap">
      <button class="key key-operation" @click="handlePercent()">%</button>
      <button class="key key-operation" @click="handleMath('x')">×</button>
      <button class="key key-operation" @click="handleMath('÷')">÷</button>
      <button class="key key-operation" @click="handleDelOne()">
        <span class="iconfont icon-shanchu"></span>
      </button>
      <button class="key" @click="handleNumber(7)">7</button>
      <button class="key" @click="handleNumber(8)">8</button>
      <button class="key" @click="handleNumber(9)">9</button>
      <button class="key key-operation" @click="delAll()">C</button>
      <button class="key" @click="handleNumber(4)">4</button>
      <button class="key" @click="handleNumber(5)">5</button>
      <button class="key" @click="handleNumber(6)">6</button>
      <button class="key key-operation" @click="handleMath('-')">-</button>
      <button class="key" @click="handleNumber(1)">1</button>
      <button class="key" @click="handleNumber(2)">2</button>
      <button class="key" @click="handleNumber(3)">3</button>
      <button class="key key-operation" @click="handleMath('+')">+</button>
      <button class="key" @click="handleNegation()">
        <span class="iconfont icon-jiajian"></span>
      </button>
      <button class="key" @click="handleNumber(0)">0</button>
      <button class="key" @click="handleDot()">.</button>
      <button class="key equal" @click="handleEqual()">=</button>
    </div>
  </div>
</template>
<script>
import '@/assets/icon/iconfont.css'
export default {
  data () {
    return {
      // 输入
      inputValue: '0',
      // 记录
      recordValue: '',
      // 算术符号
      opSymbol: '',
      // 计算结束标记
      computeFlag: false,
      // 第一次数据
      firstValue: '',
      // 第二次数
      secondValue: ''
    }
  },
  methods: {
    // 点击数字
    handleNumber (num) {
      if (this.computeFlag) {
        this.inputValue = String(num)
        this.computeFlag = false
      } else {
        if (this.inputValue === '0') {
          this.inputValue = String(num)
        } else {
          let len = (/\./).test(this.inputValue) ? 17 : 16
          if (this.inputValue.replace(/^-/, '').length < len) {
            this.inputValue += String(num)
          }
        }
        if (this.opSymbol) {
          this.secondValue = this.inputValue
        }
      }
    },
    // 取反
    handleNegation () {
      if (!(/^-/).test(this.inputValue)) {
        this.inputValue = this.inputValue === '0' ? '0' : '-' + this.inputValue
      } else {
        this.inputValue = this.inputValue.replace(/^-/, '')
      }
    },
    // 小数点
    handleDot () {
      if (!(/\./).test(this.inputValue) && this.inputValue.replace(/^-/, '').length < 16) {
        this.inputValue = this.inputValue + '.'
      }
    },
    // 删除一位
    handleDelOne () {
      let originVal = this.inputValue.replace(/^-/, '')
      if (originVal.length > 1) {
        this.inputValue = this.inputValue.slice(0, -1)
      } else if (originVal.length === 1) {
        this.inputValue = '0'
      }
    },
    // 删除所有
    delAll () {
      this.inputValue = '0'
      this.recordValue = ''
      this.opSymbol = ''
    },
    // 百分比
    handlePercent () {
      if (this.inputValue === '0') {
        this.inputValue = '0'
      } else {
        if (parseFloat(this.inputValue) > 0.000001) {
          this.inputValue = String(parseFloat(this.inputValue) / 100)
        }
      }
    },
    // 算术符号
    handleMath (op) {
      this.opSymbol = op
      this.recordValue = this.inputValue + op
      this.firstValue = this.inputValue
      this.inputValue = '0'
    },
    // 计算
    handleEqual () {
      let firstValue = parseFloat(this.firstValue)
      let op = this.opSymbol
      let secondValue = parseFloat(this.secondValue)
      let result = 0
      this.recordValue = this.inputValue + op + secondValue
      switch (op) {
        case '+':
          result = firstValue + secondValue
          break;
        case '-':
          result = firstValue - secondValue
          break;
        case 'x':
          result = firstValue * secondValue
          break;
        case '÷':
          result = firstValue / secondValue
          break;
      }
      this.inputValue = String(result)
      this.firstValue = result
      this.computeFlag = true
    }
  },
  computed: {
    // 记录
    showRecord () {
      if (this.recordValue === '') {
        return true
      }
      return false
    },
    // 格式化显示
    formatInput () {
      let numArr = this.inputValue
      let length = numArr.length
      let result = this.inputValue
      if (length > 3 && !/^0/.test(result)) {
        let only = length % 3
        result = this.inputValue.slice(0, only)
        let dotSite = this.inputValue.indexOf('.') === -1 ? length : this.inputValue.indexOf('.')
        for (let i = only; i < length; i += 3) {
          if (i < dotSite) {
            result += ',' + this.inputValue.slice(i, i + 3)
          } else {
            result += this.inputValue.slice(i, i + 3)
          }
        }
      }
      result = result.replace(/^,/, '')
      return result
    },
    // 字号改变
    changeFontSize_0 () {
      return this.inputValue.length === 14
    },
    changeFontSize_1 () {
      return this.inputValue.length === 15
    },
    changeFontSize_2 () {
      return this.inputValue.length >= 16
    }
  }
}
</script>
<style scoped>
html, body {
  width: 100%;
  height: 100vh;
  overflow: hidden;
}
/* 计算器外框 */
.calculator-wrap {
  width: 100%;
  height: 100%;
}
/* 输出区域 */
.result-screen {
  border: 1px solid grey;
  height: 3.8rem;
  font-weight: 500;
  font-family: "Microsoft YaHei",Georgia,Serif;
  display: flex;
  flex-direction: column;
  padding: 0 10px;
}
/* 历史按钮 */
.history-pad {
  display: flex;
  justify-content: flex-end;
  margin: 10px 0 0 0;
}
/* 输入记录 */
.record-pad {
  display: flex;
  justify-content: flex-end;
  margin: 8px 0 0;
}
/* 输入 */
.input-pad {
  display: flex;
  justify-content: flex-end;
  font-size: 42px;
}

/* 操作外框 */
.operation-wrap {
  height: 400px;
  display: grid;
  grid-template-columns: repeat(4, 25%);
  grid-template-rows: repeat(5, 80px);
  background: #fafafa!important;
}
/* 键盘通用样式 */
.key {
  width: 100%;
  display: flex;
  justify-content: center;
  align-content: center;
  line-height: 80px;
  background-color: #fafafa;

}
/* 键盘0 */
.key-zero {
  /* 左边框距离右边框跨越2个网格 */
  grid-column-start: span 2;
}
/* 图标 */
.iconfont {
  font-size: 20px;
}
/* 删除 */
.icon-shanchu {
  font-size: 30px;
}
/* 操作区域 */
.key-operation {
  background-color: #f0f0f0;
}
/* 等于 */
.equal {
  background-color: #74a3c9;
  border-radius: 5px 0 0!important;
}
/* 隐藏记录 */
.hideRecord {
  margin: 25px 0 0;
}
.miniFont_0 {
  font-size: 38px;
}
.miniFont_1 {
  font-size: 36px;
}
.miniFont_2 {
  font-size: 34px;
}
</style>