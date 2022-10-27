<template>
  <table class="w-full" :class="{hidden: isShow}">
    <thead>
      <tr>
        <th>Дата</th>
        <th>Начало</th>
        <th>Конец</th>
        <th>Итого</th>
      </tr>
    </thead>
    <tr v-for="item in historyList">
      <td>{{ item.date }}</td>
      <td>{{ new Date(item.start).toLocaleTimeString() }}</td>
      <td>{{ item.stop && new Date(item.stop).toLocaleTimeString() }}</td>
      <td>{{ item.total && getFormatedTime(item.total) }}</td>
    </tr>
  </table>
</template>
<script>
import outputTime from '../utils/outputTime'

export default {
  props: ['history-list', 'is-show'],
  methods: {
    getFormatedTime(total) {
      return outputTime(Math.floor(total / 1000))
    },

  }
}
</script>