<template>
  <ul class="list">
    <li class="item"
        v-for="(item, index) in letters"
        @click="handleLerClick"
        @touchstart.prevent="handleTouchStart"
        @touchmove="handleTouchMove"
        @touchend="handleTouchEnd"
        :ref = item
        :class="{'current': item===alphabetcolor || index===alphabetcolor}"
        :key="item">
      {{item}}
    </li>
  </ul>
</template>

<script>
  export default {
    name: 'CityAlphabet',
    props: {
      cities: Object
    },
    data() {
      return {
        alphabetcolor: '',
        touchStatus: false,
        startY: 0,
        timer: null // 事件节流 （属于优化）
      }
    },
    updated () {
      this.startY = this.$refs['A'][0].offsetTop
    },
    computed: {
      letters () {
        const letters = []
        for (let i in this.cities) {
          letters.push(i)
        }
        return letters
      }
    },
    methods: {
      handleLerClick(e) {
        this.alphabetcolor = e.target.innerText
        this.$emit('change', e.target.innerText)
      },
      handleTouchStart() {
        this.touchStatus = true
      },
      handleTouchMove(e) {
        if (this.touchStatus) {
          // const startY = this.$refs['A'][0].offsetTop
          if (this.timer) {
            clearTimeout(this.timer)
          }
          this.alphabetcolor = ''
          this.timer = setTimeout(() => {
            const touchY = e.touches[0].clientY - 79
            const index = Math.floor((touchY - this.startY) / 20)
            if (index >= 0 && index < this.letters.length) {
              this.alphabetcolor = index
              this.$emit('change', this.letters[index])
            }
          }, 16)
        }
      },
      handleTouchEnd() {
        this.touchStatus = false
      }
    }
  }
</script>

<style lang="stylus" scoped>
  @import '~styles/varibles.styl'
  .list
    display: flex;
    flex-direction: column;
    justify-content: center;
    position: absolute
    top: 1.58rem;
    right: 0;
    bottom: 0;
    width: .4rem;
    .item
      line-height: .4rem;
      text-align: center;
      color: $bgColor
    .current
      color: red;
</style>
