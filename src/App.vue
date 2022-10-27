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

    <app-time-list
      :times="times"
      @delete-item="deleteTime"
      @start-pause="startPauseItem"
      @click-item="clickItem"
      v-if="times.length"
    ></app-time-list>
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
      if (!this.isStart && !this.interval) return
      this.isStart = false
      clearInterval(this.interval)
      this.interval = null
      this.timer = '00:00:00'
      const stop = Date.now()
      const total = stop - this.startOfTask
      const date = new Date().toLocaleDateString()
      this.times.push({
        id: stop,
        desc: this.description,
        date,
        start: this.startOfTask,
        stop,
        total,
        isActive: false,
        isShow: false,
        history: [
          {
            id: Math.random(),
            date,
            start: this.startOfTask,
            stop,
            total,
          },
        ],
      })
      this.addToStorage()
      this.startOfTask = 0
      this.description = ''
      this.pausedTime = 0
    },
    startPauseItem(id) {
      const idx = this.times.findIndex((item) => item.id === id)
      if (this.times[idx].isActive) {
        clearInterval(this.interval)
        this.interval = null
        this.times[idx].isActive = false
        this.times[idx].stop = Date.now()
        this.times[idx].history[this.times[idx].history.length - 1].stop =
          Date.now()
        this.times[idx].history[this.times[idx].history.length - 1].total =
          this.times[idx].stop - this.times[idx].start
        this.addToStorage()
        return
      }
      this.times[idx].isActive = true
      this.times[idx].start = Date.now()
      this.times[idx].history.push({
        id: Math.random(),
        date: new Date().toLocaleDateString(),
        start: Date.now(),
      })
      this.times[idx].pausedTime = this.times[idx].total
      this.interval = setInterval(() => {
        this.times[idx].total =
          this.times[idx].pausedTime + (Date.now() - this.times[idx].start)
      }, 1000)
    },
    addToStorage() {
      const timesJson = JSON.stringify(this.times)
      localStorage.setItem('times', timesJson)
    },
    deleteTime(id) {
      const newTimes = this.times.filter((time) => time.id !== id)
      this.times = newTimes
      this.addToStorage()
    },
    clickItem(id) {
      const idx = this.times.findIndex(time => time.id === id)
      this.times[idx].isShow = !this.times[idx].isShow
    }
  },
  components: {
    AppButton,
    AppTimeList,
    AppInput,
  },
}
</script>
