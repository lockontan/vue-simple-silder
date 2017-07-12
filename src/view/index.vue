<template>
  <div>
    <slider v-model="value" style="width: 400px" :maxNum="maxNum" @dragEnd='dragEnd' @dragStart='dragStart'>
      <template slot="dot"></template>
    </slider>
    <audio id="audio" ref='audio' controls="" name="media" style="display: none">
      <source :src='src' type="audio/mpeg">
    </audio>
  </div>
</template>

<script>
import slider from '@/components/slider'
export default {
  components: {
    slider
  },

  data: () => {
    return {
      value: 100,
      maxNum: 50,
      src: require('@/assets/1.mp3'),
      start: false
    }
  },

  mounted () {
    var _this = this
    _this.$refs.audio.addEventListener('timeupdate', function (e) {
      if (_this.start) return
      _this.value = Math.floor(_this.$refs.audio.currentTime)
    })
    _this.$refs.audio.addEventListener('loadeddata', function (e) {
      _this.maxNum = Math.round(_this.$refs.audio.duration)
      _this.$refs.audio.currentTime = _this.value
      _this.$refs.audio.play()
    })
  },

  methods: {
    dragEnd (value) {
      this.$refs.audio.currentTime = value
      this.start = false
    },
    dragStart () {
      this.start = true
    }
  }
}
</script>
