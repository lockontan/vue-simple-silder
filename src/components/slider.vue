<template>
  <div class="vue-slider-body" @click="bodyClick" :value="value">
    <div class="vue-slider" :style="[sliderStyles]">
      <span class="vue-slider-dot" @click.stop @mousedown="moveStart" :style="[dotStyles]">
        <slot name="dot">
          <span class="vue-slider-dot-center" :style="[centerStyles]"></span>
        </slot>
      </span>
    </div>
    <div class="vue-slider-process" :style="[processStyles]"></div>
  </div>
</template>

<script>
  export default {
    data: function () {
      return {
        start: false,
        duration: 0.5,
        clientWidth: 0
      }
    },
    props: {
      value: {
        type: Number,
        default () {
          return 0
        }
      },
      maxNum: {
        type: Number,
        default () {
          return 100
        }
      },
      dotSize: {
        type: [Number, String],
        default () {
          return 12
        }
      },
      height: {
        type: [Number, String],
        default () {
          return 6
        }
      },
      processColor: {
        type: String,
        default () {
          return '#50bfff'
        }
      }
    },
    computed: {
      sliderStyles () {
        return {
          height: `${this.height}px`,
          borderRadius: `${this.height / 2}px`
        }
      },
      dotStyles () {
        return {
          transform: `translateX(${this.translateX}px)`,
          transitionDuration: `${this.duration}s`,
          width: `${this.dotSize}px`,
          height: `${this.dotSize}px`,
          borderRadius: `${this.dotSize}px`,
          top: `${(this.height - this.dotSize) / 2}px`
        }
      },
      processStyles () {
        return {
          width: `${this.width}px`,
          transitionDuration: `${this.duration}s`,
          height: `${this.height}px`,
          borderRadius: `${this.height / 2}px`,
          backgroundColor: `${this.processColor}`
        }
      },
      centerStyles () {
        var w = this.dotSize / 2
        // w处理为偶数
        w += w % 2
        return {
          width: `${w}px`,
          height: `${w}px`,
          top: `${(this.dotSize - w) / 2}px`,
          left: `${(this.dotSize - w) / 2}px`,
          borderRadius: `${w}px`
        }
      },
      width () {
        return this.value / this.maxNum * this.clientWidth
      },
      translateX () {
        return this.value / this.maxNum * this.clientWidth - this.dotSize / 2
      }
    },
    mounted () {
      this.clientWidth = this.$el.firstChild.clientWidth
    },
    methods: {
      bindEvents () {
        document.addEventListener('mousemove', this.moving)
        document.addEventListener('mouseup', this.moveEnd)
      },
      clearEvents () {
        document.removeEventListener('mousemove', this.moving)
        document.removeEventListener('mouseup', this.moveEnd)
      },
      bodyClick (e) {
        var value = Math.ceil(this.maxNum * e.offsetX / this.clientWidth)
        this.$emit('dragEnd', value)
        this.width = value / this.maxNum * this.clientWidth
        this.translateX = this.width - this.dotSize / 2
      },
      moveStart (e) {
        this.bindEvents()
        this.start = true
        document.body.style.userSelect = 'none'
        this.duration = 0
        this.startW = e.pageX
        this.w = JSON.parse(JSON.stringify(this.width))
        this.$emit('dragStart')
      },
      moving (e) {
        if (this.start) {
          var distance = e.pageX - this.startW
          var clientX = (this.w + distance) < 0 ? 0 : ((this.w + distance) > this.clientWidth ? this.clientWidth : (this.w + distance))
          this.width = clientX
          this.translateX = this.width - this.dotSize / 2
          this.$emit('input', Math.ceil(clientX * this.maxNum / this.clientWidth))
        }
      },
      moveEnd () {
        document.body.style.userSelect = 'inherit'
        this.start = false
        this.duration = 0.5
        this.translateX = (this.value / this.maxNum) * this.clientWidth - this.dotSize / 2
        this.$emit('dragEnd', this.value)
      }
    },
    beforeDestroy () {
      this.clearEvents()
    },
    watch: {
      value (value) {
        if (value > this.maxNum) {
          this.$emit('input', this.maxNum)
        } else if (value < 0) {
          this.$emit('input', 0)
        }
      }
    }
  }
</script>

<style scoped>
  .vue-slider-body{
    padding: 8px;
    position: relative;
    width: auto;
    user-select: none
  }
  .vue-slider{
    background: #ccc;
    position: relative;
  }
  .vue-slider-process{
    position: absolute;;
    top: 8px;
    z-index: 98;
  }
  .vue-slider-dot{
    position: absolute;
    top: 5px;
    cursor: pointer;
    background-color: #fff;
    box-shadow: 0.5px 0.5px 2px 1px rgba(0, 0, 0, 0.32);
    z-index: 99
  }
  .vue-slider-dot-center{
    background-color: red;
    position: absolute;
  }
</style>
