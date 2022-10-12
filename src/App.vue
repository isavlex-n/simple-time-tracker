<template>
  <header class="container mx-auto">
    <h1 class="text-3xl font-bold text-center">Simple time tracker</h1>
  </header>
  <div>
    <h2 class="text-2xl font-bold text-center mt-4 mb-4">{{ timer }}</h2>
    <div class="flex justify-center mb-6">
      <app-input
        v-model="description"
        placeholder="Описание задачи"
      ></app-input>
      <app-button
        @action="startTimer"
        class="ml-2 mr-2"
        :disabled="isDisabled"
        >{{ isStart ? 'Пауза' : 'Старт' }}</app-button
      >
      <app-button @action="stopTimer">Стоп</app-button>
    </div>

    <app-time-list :times="times" @delete-item="deleteTime" v-if="times.length"></app-time-list>
  </div>
</template>
<script>
import outputTime from './utils/outputTime'
import AppButton from './components/AppButton.vue'
import AppTimeList from './components/AppTimeList.vue'
import AppInput from './components/AppInput.vue'

export default {
  data() {
    return {
      description: '',
      isStart: false,
      interval: null,
      startOfTask: null,
      timestamp: 0,
      pausedTime: 0,
      totalSec: 0,
      timer: '00:00:00',
      times: [],
    }
  },
  mounted() {
    if (!localStorage.getItem('times')) return
    this.times = JSON.parse(localStorage.getItem('times'))
  },
  computed: {
    isDisabled() {
      return this.description.length < 3
    },
  },
  methods: {
    startTimer() {
      if (!this.isStart) {
        this.isStart = true
        const start = Date.now()
        this.startOfTask = this.startOfTask ? this.startOfTask : start
        this.interval = setInterval(() => {
          const delta = Date.now() - start
          this.timestamp = Math.floor(delta / 1000)
          this.timer = outputTime(this.timestamp + this.pausedTime)
        }, 1000)
      } else {
        this.isStart = false
        clearInterval(this.interval)
        this.pausedTime += this.timestamp
      }
    },
    stopTimer() {
      if (!this.isStart) return
      this.isStart = false
      clearInterval(this.interval)
      this.timer = '00:00:00'
      const stop = Date.now()
      this.times.push({
        id: stop,
        desc: this.description,
        date: new Date().toLocaleDateString(),
        start: new Date(this.startOfTask).toLocaleTimeString(),
        stop: new Date(stop).toLocaleTimeString(),
        total: outputTime(Math.floor((stop - this.startOfTask) / 1000)),
      })
      this.addToStorage()
      this.startOfTask = 0
      this.description = ''
    },
    addToStorage() {
      const timesJson = JSON.stringify(this.times)
      localStorage.setItem('times', timesJson)
    },
    deleteTime(id) {
      const newTimes = this.times.filter(time => time.id !== id)
      this.times = newTimes
      this.addToStorage()
    }
  },
  components: {
    AppButton,
    AppTimeList,
    AppInput,
  },
}
</script>
