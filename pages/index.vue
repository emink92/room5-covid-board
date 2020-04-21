<template>
  <div>
    <div class="toolbar">
      <div class="logo">
        <img class="logo" src="~/assets/images/logo.png">
      </div>
      <div class="time">
        {{currentTime}}
      </div>
    </div>
      <div class="container">
        <div class="block">
          <h2>WARM UP</h2>
          <textarea rows="10" v-model="warmUpTemp" v-if="updatingWarmUp"></textarea>
          <p class="content" v-if="!updatingWarmUp" v-on:click="updatingWarmUp = true">{{warmUpText}}</p>
          <button v-if="updatingWarmUp" v-on:click="updateText(warmUpTemp, 'warmUp')">GUARDAR</button>
        </div>
        <div class="block">
          <h2>STRENGTH</h2>
          <textarea rows="10" v-model="strengthTemp" v-if="updatingStrength"></textarea>
          <p class="content" v-if="!updatingStrength" v-on:click="updatingStrength = true">{{strengthText}}</p>
          <button v-if="updatingStrength" v-on:click="updateText(strengthTemp, 'strength')">GUARDAR</button>
        </div>
        <div class="block">
          <h2>METCON</h2>
          <textarea rows="10" v-model="metconTemp" v-if="updatingMetcon"></textarea>
          <p class="content" v-if="!updatingMetcon" v-on:click="updatingMetcon = true">{{metconText}}</p>
          <button v-if="updatingMetcon" v-on:click="updateText(metconTemp, 'metcon')">GUARDAR</button>
        </div>
      </div>
      <div class="timer">
        <div class="actions">
          <!-- <button v-on:click="startCountdown" :disabled="timerStarted">START</button> -->
          <!-- <button v-on:click="stopCountdown" :disabled="!timerStarted">STOP</button> -->
          <button v-on:click="startStopwatch" :disabled="timerStarted">START</button>
          <button v-on:click="stopStopwatch" :disabled="!timerStarted">STOP</button>
          <button disabled>MODE</button>
        </div>
        <span v-on:click="updateTimer" class="time">{{countDownTime}}</span>
      </div>
  </div>
</template>

<script>
import Logo from '~/components/Logo.vue'
import moment from 'moment'

const setIntervalAsync = (fn, ms) => {
  fn().then(() => {
    setTimeout(() => setIntervalAsync(fn, ms), ms);
  });
};

export default {
  data () {
    return {
      currentTime: '',
      updatingWarmUp: false,
      warmUpText: 'this is a test text',
      warmUpTemp: '',
      updatingStrength: false,
      strengthText: 'this is a test text',
      strengthTemp: '',
      updatingMetcon: false,
      metconText: 'this is a test text',
      metconTemp: '',
      // countDown: moment(60 * 10 * 1000),
      countDown: moment(0),
      timerStarted: false,
      timerInterval: ''
    }
  },
  components: {
    Logo
  },
  watch: {
    warmUpText(value) {
      localStorage.warmUpText = value
    },
    strengthText(value) {
      localStorage.strengthText = value
    },
    metconText(value) {
      localStorage.metconText = value
    },
  },
  methods: {
    time () {
      let self = this
      this.currentTime = moment().format('hh:mm')

      setInterval(self.time, 1000 * 60)
    },
    updateText (value, type) {
      this[type + 'Text'] = value
      this['updating' + type.charAt(0).toUpperCase() + type.slice(1)] = false
    },
    startCountdown () {
      this.timerStarted = true
      let that = this
      this.timerInterval = setInterval(function(){
        that.countDown = moment(that.countDown.subtract(1, 'seconds'))
      }, 1000)
    },
    stopCountdown () {
      clearInterval(this.timerInterval)
      this.timerStarted = false
      this.countDown = moment(60 * 10 * 1000)
    },
    startStopwatch () {
      this.timerStarted = true
      let that = this
      this.timerInterval = setInterval(function(){
        that.countDown = moment(that.countDown.add(1, 'second'))
      }, 1000)
    },
    updateTimer () {
      let value = prompt('Introdueix un valor numèric')
      if (value) {
        this.countDown = moment(60 * parseInt(value) * 1000)
      }
    },
    stopStopwatch () {
      clearInterval(this.timerInterval)
      this.timerStarted = false
      this.countDown = moment(0)
    }
  },
  computed: {
    countDownTime() {
      return this.countDown.format('mm:ss')
    }
  },
  mounted () {
    this.time()
    this.warmUpTemp = this.warmUpTemp ? this.warmUpTemp : this.warmUpText
    this.strengthTemp = this.strengthTemp ? this.strengthTemp : this.strengthText
    this.metconTemp = this.metconTemp ? this.metconTemp : this.metconText

    if (localStorage.warmUpText) this.warmUpText = this.warmUpTemp = localStorage.warmUpText
    if (localStorage.strengthText) this.strengthText = this.strengthTemp = localStorage.strengthText
    if (localStorage.metconText) this.metconText = this.metconTemp = localStorage.metconText
  }
}
</script>

<style lang="sass">
@import '~assets/sass/style'
</style>
